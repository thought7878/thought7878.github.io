>ETag 与 Last-Modified 深度对比

`ETag` 与 `Last-Modified` 是 HTTP **协商缓存（Cache Validation）** 的两大核心机制。*当强缓存（`Cache-Control`/`Expires`）失效或配置为 `no-cache` 时，浏览器通过它们向服务器询问*：“资源有没有变？没变就返回 304，变了再返回 200+新内容”。

尽管目标一致，但两者*在验证依据、精度、性能、分布式兼容性*上存在本质差异。**现代 Web 架构中，`ETag` 已成为事实标准，`Last-Modified` 仅作为降级兼容**。

---

## 一、核心机制对比

| 维度 | `Last-Modified` / `If-Modified-Since` | `ETag` / `If-None-Match` |
|------|--------------------------------------|--------------------------|
| **验证依据** | 文件最后修改时间（文件系统 `mtime`） | 资源内容标识（哈希/版本/大小+时间等） |
| **精度** | **秒级**（1s） | **内容级**（可精确到字节变化） |
| **请求头** | `If-Modified-Since: <GMT时间>` | `If-None-Match: "<标识符>"` |
| **响应头** | `Last-Modified: <GMT时间>` | `ETag: "<标识符>"` 或 `W/"<弱标识>"` |
| **304 判定** | 服务端 `mtime ≤ If-Modified-Since` | 服务端生成的 ETag == `If-None-Match` |
| **RFC 规范** | RFC 7232（HTTP/1.1 缓存验证） | RFC 7232（优先级高于 Last-Modified） |
| **典型生成方式** | 直接读取文件系统元数据 | 内容 Hash（SHA/MD5）、版本号、`inode-size-mtime` 组合 |

---

## 二、Last-Modified 的三大致命缺陷

### 1. 秒级精度不足
- 文件在 1 秒内多次修改，`mtime` 不变 → 浏览器命中 304，**拿到旧内容**。
- 高频更新场景（如实时配置、动态生成的 JSON）极易出现缓存不一致。

### 2. “时间变了，内容没变” 导致无效 200
- 部署脚本 `touch` 文件、备份恢复、容器重建、同步工具（rsync）未保留时间戳 → `mtime` 更新但内容未变。
- 浏览器收到 200 + 完整响应体，**浪费带宽与延迟**，失去协商缓存意义。

### 3. 分布式/CDN 环境时间不一致
- 多节点部署时，文件同步延迟或服务器时钟漂移 → 不同节点返回不同的 `Last-Modified`。
- 负载均衡下请求打到不同节点，**304/200 反复横跳**，缓存命中率暴跌。

---

## 三、ETag 的强弱语义与生成策略

### 🔹 强 ETag（Strong）
```http
ETag: "a1b2c3d4e5f6"
```
- **语义**：字节级完全一致（Byte-for-byte identical）
- **用途**：标准缓存验证、Range 请求（断点续传）
- **要求**：内容任意字节变化，ETag 必须改变

### 🔹 弱 ETag（Weak）
```http
ETag: W/"a1b2c3d4e5f6"
```
- **语义**：语义等价，但字节可能不同（如动态生成的 HTML 含时间戳/请求ID）
- **限制**：**不可用于 Range 请求**，部分 CDN/代理会忽略弱 ETag 的 304 判定
- **场景**：API 响应、服务端渲染页面、内容语义稳定但格式微调的资源

### ⚙️ 常见生成策略
| 策略 | 性能 | 一致性 | 适用场景 |
|------|------|--------|----------|
| **内容 Hash**（SHA256/MD5） | 低（需读全文计算） | 极高 | 静态资源、构建产物、关键配置 |
| **版本号/构建ID** | 极高 | 高（需发版同步） | 前后端分离产物、API 响应 |
| `inode-size-mtime`（Nginx 默认） | 高 | 中（集群需同步 inode） | 单机/同构集群静态文件 |
| **业务自定义**（如 DB `updated_at`） | 高 | 取决于业务逻辑 | 动态接口、数据库驱动内容 |

> 📌 注意：Nginx 默认 `etag on;` **不是纯内容哈希**，而是 `hex(inode)-hex(size)-hex(mtime)`。在跨节点部署时若 inode 不同，会导致 ETag 不一致。需通过 `etag off;` + 应用层生成，或使用同步工具保留 inode。

---

## 四、协同工作与优先级（RFC 7232 规范）

浏览器与服务端遵循明确的优先级规则：

```
浏览器请求逻辑：
1. 若缓存中有 ETag → 发送 If-None-Match
2. 若无 ETag 但有 Last-Modified → 发送 If-Modified-Since
3. 两者都有 → 仅发送 If-None-Match（忽略 If-Modified-Since）

服务端响应逻辑：
1. 优先校验 If-None-Match
   - 匹配 → 304
   - 不匹配 → 继续校验 If-Modified-Since（兼容旧客户端）
2. 若仅收到 If-Modified-Since → 按时间校验
```

✅ **最佳实践**：服务端同时返回 `ETag` + `Last-Modified`，但**校验逻辑必须以 ETag 为准**。

---

## 五、工程配置示例

### 🟢 Nginx
```nginx
location /static/ {
    # 开启 ETag（默认 inode-size-mtime）
    etag on;
    
    # 强缓存 1 年 + 协商缓存兜底（现代构建产物推荐）
    add_header Cache-Control "public, max-age=31536000, immutable";
    
    # 若需纯内容 Hash ETag，需关闭默认 etag 由上游应用生成
    # etag off;
}
```

### 🟢 Node.js / Express
```js
const express = require('express');
const app = express();

// express.static 默认生成强 ETag（基于内容 hash）
app.use('/assets', express.static('public', {
  etag: true,
  lastModified: true,
  maxAge: '1y',
  immutable: true
}));

// 动态接口自定义 ETag
app.get('/api/config', (req, res) => {
  const data = { version: '1.2.3', theme: 'dark' };
  const etag = `"${require('crypto').createHash('md5').update(JSON.stringify(data)).digest('hex')}"`;
  
  res.set({
    'ETag': etag,
    'Cache-Control': 'no-cache' // 强制协商验证
  });
  
  if (req.headers['if-none-match'] === etag) {
    return res.status(304).end();
  }
  res.json(data);
});
```

### 🟢 CDN / 云存储
- 阿里云 OSS / 腾讯云 COS / AWS S3：**自动基于内容生成强 ETag**（MD5 或分片 Hash）
- Cloudflare / 阿里云 CDN：默认透传源站 ETag，支持 `ETag 一致性校验` 开关
- ⚠️ 若源站与 CDN ETag 算法不同，需统一或关闭 CDN 的 ETag 改写

---

## 六、选型决策矩阵

| 场景 | 推荐方案 | 原因 |
|------|----------|------|
| 构建产物（JS/CSS/图片带 Hash 文件名） | `Cache-Control: max-age=1y, immutable` + **无需协商缓存** | 文件名已版本化，永远不更新，直接强缓存 |
| 无 Hash 的静态文件（favicon、robots.txt） | `ETag` + `Cache-Control: no-cache` | 内容可能变，需精确验证，避免时间漂移 |
| 动态 API 响应 | 自定义 `ETag`（业务版本/内容Hash） + `no-cache` | 无文件 mtime，需语义或内容级验证 |
| 老旧系统 / 兼容 IE8 | `Last-Modified` 兜底 | 早期浏览器不支持 ETag |
| 集群部署且无法同步 inode | 应用层生成内容 Hash ETag，**关闭 Nginx 默认 etag** | 避免节点间 ETag 不一致导致 304 失效 |

---

## 七、常见陷阱与避坑指南

| 陷阱 | 现象 | 修复方案 |
|------|------|----------|
| **盲目关闭 ETag 追求性能** | 协商缓存失效，每次返回 200 完整体 | ETag 校验开销远低于传输完整资源，保留并优化生成逻辑 |
| **集群 ETag 不一致** | 同一 URL 交替返回 304/200，缓存命中率 < 30% | 统一生成算法（内容Hash/版本号），或启用 CDN 一致性校验 |
| **弱 ETag 用于 Range 请求** | 视频/大文件断点续传失败或返回错误分片 | Range 请求必须使用强 ETag，校验失败应返回 412 Precondition Failed |
| `Cache-Control: no-cache` 误解 | 以为“不缓存”，实际是“必须协商验证” | `no-cache` = 存缓存但每次问服务器；`no-store` = 绝对不存 |
| **动态接口未处理 If-None-Match** | 客户端发 304 请求，服务端仍返回 200+全量数据 | 框架中间件统一拦截校验，或手动比对 `req.headers['if-none-match']` |

---

## 八、**总结**

| 核心结论                         | 说明                                                |
| ---------------------------- | ------------------------------------------------- |
| **ETag 是现代标准**               | 精度高、抗时间漂移、适配动态内容，RFC 明确优先级更高                      |
| **Last-Modified 仅用于兼容**      | 秒级精度+元数据敏感，在分布式/高频更新场景已不适用                        |
| **两者可共存，但校验必须以 ETag 为准**     | 浏览器自动优先发 `If-None-Match`，服务端需遵循相同逻辑               |
| **ETag 不是银弹**                | 生成策略决定效果：内容 Hash 最准但耗 CPU，版本号最快需发版协同              |
| **与 Cache-Control 配合才是完整方案** | `max-age` 控强缓存周期，`no-cache` 强制协商，`immutable` 跳过验证 |

> 💡 **工程心法**：  
> 1. 带内容哈希的文件名 → 直接 `max-age=1y, immutable`，**跳过协商缓存**  
> 2. 无版本标识的资源 → `ETag` + `no-cache`，**精准验证**  
> 3. 动态接口 → 业务版本号/轻量 Hash 作 ETag，**避免全量计算**  
> 4. 集群部署 → **统一 ETag 生成源**（应用层或对象存储），禁用节点本地元数据组合

如需针对具体场景输出**可落地的配置**（如：微服务网关统一 ETag 校验中间件、Nginx 集群 ETag 一致性方案、动态 API 高性能 ETag 生成策略、或 CDN 缓存调试全链路排查清单），可提供技术栈与业务形态，我将提供完整代码与架构图。