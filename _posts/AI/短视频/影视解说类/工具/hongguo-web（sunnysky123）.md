哥哥，我把整个源码仓库拉下来逐文件读了一遍（151 commits，约 13,900 行），完整报告已生成。核心结论：

**一句话定位**：这是一个跑在本机的「红果短剧」网页播放器，本质是逆向工程项目——它把 App 接口签名、加密视频解密、MP4 容器改写整条链路用 Node.js 重写了一遍。作者在 README 明确写了「仅供技术研究」，且 MIT 许可只覆盖代码，不覆盖它解密出来的内容。

**技术栈出乎意料地简单**：`dependencies: {}`，零第三方依赖，`node:http` 裸写后端（含自研 JSON 序列化），前端是三个静态文件、一个 IIFE、无框架无构建。运行时只要 Node ≥ 18，ffmpeg 可选。自带 165 项离线自检 + 5 平台内置 Node 的发行包。

**最值钱的两个文件**：

- `core/pure-sign.js`（916 行）：纯 JS 复刻 x-gorgon、x-khronos、x-ladon、x-argus、x-helios、x-medusa 六个签名头，不用 unidbg 跑 .so。细节如 `branch==1` 会被服务端拒（110001），代码里抖 `_rticket` 最多 32 次绕开。
- `core/mp4.js`（1247 行）：BMFF 盒解析 + CENC AES-128-CTR 逐样本原地解密（计数器 `(base_iv_high64 + idx) << 64`）+ **纯 JS 减壳**（删 sinf、senc、saio、saiz、pssh，平移 stco，mdat 原样拷贝）。1080×1920 整集约 1 秒，比 ffmpeg 转码的 16 秒快一个数量级。

**两个我核出来的文档与代码不一致**（报告里也标了）：
1. `/img` 号称「含 HEIC 转换」，实际只把 `Content-Type` 覆写成 `image/jpeg`，字节流还是 HEIC — 浏览器不支持时封面照样裂。
2. `/download` 在 API 表里写作「下载成品」，实际返回 JSON `{ok, path, size}` 给本机路径，不吐文件流。

**风险**：整条链路依赖逆向出的签名算法、域名和字段名，上游一改版就成片失效（官网 SSR 已不再输出 `main_url`，该项目就彻底放弃了官网取址）；改动 `BIND_HOST=0.0.0.0` 等于把解密能力开放到局域网。

<svg viewBox="0 0 680 262" width="100%" role="img" xmlns="http://www.w3.org/2000/svg">
  <title>hongguo-web 的两段链路</title>
  <desc>上排是应用层：浏览器前端调用 Node 服务，服务带签名访问红果上游。下排是媒体层：拿到加密直链后经 spade 与 AES-CTR 解密、纯 JS 减壳、缓存后以 Range 串流回浏览器。</desc>
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>

  <g>
    <rect x="56" y="40" width="160" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
    <text x="136" y="61" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#0C447C">浏览器前端</text>
    <text x="136" y="80" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#185FA5">静态三件套 · 零构建</text>
  </g>
  <g>
    <rect x="260" y="40" width="160" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
    <text x="340" y="61" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#0C447C">Node 服务</text>
    <text x="340" y="80" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#185FA5">路由 · 鉴权 · 限流</text>
  </g>
  <g>
    <rect x="464" y="40" width="160" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
    <text x="544" y="61" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#0C447C">红果上游</text>
    <text x="544" y="80" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#185FA5">App 签名 · 官网兜底</text>
  </g>

  <path d="M216 68 H256" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>
  <path d="M420 68 H460" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

  <path d="M340 96 V128 H103 V162" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>
  <text x="240" y="123" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#5F5E5A">取直链</text>

  <g>
    <rect x="40" y="166" width="126" height="56" rx="8" fill="#FAEEDA" stroke="#854F0B" stroke-width="0.5"/>
    <text x="103" y="187" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#633806">加密直链</text>
    <text x="103" y="206" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#854F0B">CENC 密文 MP4</text>
  </g>
  <g>
    <rect x="198" y="166" width="126" height="56" rx="8" fill="#FAEEDA" stroke="#854F0B" stroke-width="0.5"/>
    <text x="261" y="187" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#633806">解密</text>
    <text x="261" y="206" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#854F0B">spade + AES-CTR</text>
  </g>
  <g>
    <rect x="356" y="166" width="126" height="56" rx="8" fill="#FAEEDA" stroke="#854F0B" stroke-width="0.5"/>
    <text x="419" y="187" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#633806">纯 JS 减壳</text>
    <text x="419" y="206" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#854F0B">删信令 · 平移 stco</text>
  </g>
  <g>
    <rect x="514" y="166" width="126" height="56" rx="8" fill="#FAEEDA" stroke="#854F0B" stroke-width="0.5"/>
    <text x="577" y="187" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="13" font-weight="500" fill="#633806">缓存串流</text>
    <text x="577" y="206" text-anchor="middle" dominant-baseline="central" font-family="system-ui, sans-serif" font-size="11" fill="#854F0B">.play.mp4 · Range</text>
  </g>

  <path d="M166 194 H194" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>
  <path d="M324 194 H352" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>
  <path d="M482 194 H510" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>
</svg>

---

> hongguo-web 项目解析报告

> 仓库：https://github.com/sunnysky123/hongguo-web  
> 版本：1.1.4（package.json）｜许可：MIT｜最后一次提交：2026-10-22（`add-download` 分支合并）  
> 分析方式：完整克隆源码（151 commits，约 13,900 行代码）逐文件阅读

---

## 一、一句话定位

**一个跑在本机的「红果短剧」网页播放器**：Node.js 写后端（零第三方依赖），起一个 HTTP 服务，把红果 App / 官网的内容抓下来、在本地解密成普通 MP4 再喂给浏览器 `<video>`，前端是一张单页静态网页。

它本质是**逆向工程项目**，不是红果官方产品。作者在 README 结尾写明「仅供技术研究与学习，请勿用于批量抓取或传播受版权保护的内容」。

---

## 二、整体用途与目标用户

### 用途

绕过红果只能在手机 App 内观看的限制，在电脑浏览器里：

- 浏览推荐 / 榜单 / 最新 / 分类筛选的短剧列表
- 搜索剧名，查看分集列表
- 直接在线播放（服务端解密后以 HTTP Range 串流）
- 自动连播 + 下一集预热
- 批量把整部剧下载成 `第N集.mp4` 落盘
- 记录播放历史与收藏（纯本地 localStorage）

### 目标用户

| 人群             | 诉求                                            |
| -------------- | --------------------------------------------- |
| 想在 PC 大屏看短剧的用户 | 免装 App、免模拟器，浏览器直接用                            |
| 想离线收藏剧集的用户     | 批量下载解密后的干净 MP4                                |
| 学习者 / 研究者      | 一份完整的「App 接口签名 + CENC 解密 + MP4 容器改写」的 JS 参考实现 |

不是给企业、团队用的产品：无多用户、无数据库、无云端，默认只监听 `127.0.0.1`。

---

## 三、系统架构与数据流

```
浏览器 (web/index.html + app.js + styles.css)
        │  ① GET /api-key        自动签发本地密钥
        │  ② GET /search /rank /latest /filters /browse /episodes   列表数据
        │  ③ GET /stream?vid=…   要播放地址
        ▼
Node HTTP 服务 (server/src/server.js)
        │  鉴权(x-api-key) + 限流(120/min) + 静态资源优先
        ▼
业务层 (service/*)
        ├─ client.js ──→ 红果 App API (api5-normal-sinfonlinea)     搜索/榜单/分集
        │      └─ 六个签名头由 core/pure-sign.js 进程内算
        ├─ web-source.js ─→ hongguoduanju.com SSR 页面（免签兜底）
        └─ app-source.js ─→ b 域名 multi_video_model/v1（签名）→ fallback_api
        ▼
取到加密直链 (CENC 密文 mp4)
        ▼
core/spade.js     spade_a(base64) ──字节变换──> content key
        ▼
core/mp4.js       BMFF 盒解析 → 逐样本 AES-128-CTR 解密 → 纯 JS 减壳
        ▼
server/stream-cache/<vid>_<q>.play.mp4  （命中即秒开）
        ▼
HTTP 206 Range 串流回浏览器
```

---

## 四、主要功能模块

### 4.1 前端（`web/`，纯静态零构建）

| 模块        | 说明                                                    |
| --------- | ----------------------------------------------------- |
| 四个标签页     | 推荐 / 榜单 / 最新 / 筛选（`/browse` 组合维度）                     |
| 搜索        | 输入 + 回车，带分页「加载更多」卡片（不用无限滚动，因移动端触发不稳）                  |
| 播放器弹层     | 网页全屏、选集卡片（一行五个）、简介悬浮气泡、关联剧集、收藏                        |
| 自动连播      | 播第 N 集时并行 `/prewarm?vid=第N+1集`，切集秒开；开关存 `hg_autonext` |
| 播放历史 / 收藏 | 共用一套列表组件，存 `hg_history` / `hg_fav`，上限各 200 条，不上传服务器   |
| 续播推断      | 点开看过的剧自动跳到上次那集，集号失效回落第 1 集                            |
| 批量下载面板    | 范围选择（起始/结束集）+ 1.5s 轮询进度 + 顶栏 ⤓ 角标                     |
| 密钥自愈      | 指数退避重试（1→2→4…10s，最多 12 次）；401 熔断（累计 5 次、间隔 2s）        |
| 主题切换      | 亮/暗（`◐`）                                              |

### 4.2 后端对外 API（`server/src/server.js`）

免鉴权：`/`、`/ui`、`/img`、`/health`、`/api-key`

| 路径                                     | 作用                                   |
| -------------------------------------- | ------------------------------------ |
| `/search` `/search/page`               | 搜索（App 签名接口为主，官网兜底）                  |
| `/rank` `/latest` `/filters` `/browse` | 榜单 / 最新 / 筛选项 / 组合浏览，均支持 `offset` 分页 |
| `/episodes` `/metrics/batch`           | 分集列表 / 批量查分集                         |
| `/play` `/video_url`                   | 取各集「上游直链」（仍是密文）                      |
| `/stream`                              | **可播放**：解密 + 减壳 + Range 串流           |
| `/prewarm`                             | 后台预热下一集（立即返回 `cached`/`warming`）     |
| `/download` `/download/task`           | 单集确保解密落盘 / 批量下载任务（POST 建、GET 查）      |
| `/img`                                 | 封面代理（域名白名单 + LRU 字节缓存）               |
| `/stats` `/admin/keys*`                | 运行统计、密钥增删（需 `admin_token`）           |

### 4.3 三个核心引擎（`server/src/core/`）

1. **`pure-sign.js`（916 行）** —— 六个请求头纯 JS 计算
2. **`spade.js`（144 行）** —— `spade_a` → content key
3. **`mp4.js`（1247 行）** —— BMFF 解析 + CENC 解密 + 纯 JS 减壳

---

## 五、技术栈与框架

| 层      | 选型                                                   | 备注                                                                        |
| ------ | ---------------------------------------------------- | ------------------------------------------------------------------------- |
| 运行时    | **Node.js ≥ 18**                                     | 发行包内含对应平台官方 Node LTS                                                      |
| 后端框架   | **无**，裸 `node:http`                                  | `dependencies: {}`，零第三方包                                                  |
| 加密     | `node:crypto`                                        | MD5 / SHA-512 / **SM3** / AES-128-CTR / AES-128-CBC；SM3 依赖 OpenSSL ≥1.1.1 |
| 大整数    | 原生 `BigInt`                                          | 对齐 Java `long` 语义                                                         |
| 转码（可选） | ffmpeg / ffprobe 外部进程                                | 默认不调用；只在转码或减壳失败时回落                                                        |
| 前端     | 原生 HTML/CSS/JS，IIFE 全部包在一个函数里                        | 无框架、无打包器、无构建步骤                                                            |
| 测试     | 自研 `selftest.js`（1160 行 / 21 组 / **165 项断言**）        | 离线可跑，不访问网络                                                                |
| CI/CD  | GitHub Actions（`.github/workflows/release.yml`）      | 版本号变化才打包 5 平台并发布 Release                                                  |
| 打包     | `scripts/pack.sh`（404 行 bash + curl + python3 + zip） | 从 nodejs.org 下载运行时并 SHA-256 校验，缓存 `.pack-cache/`                          |

**设计哲学**：零依赖、零构建、代码即产物。改完 `server/src/` 任意文件重跑命令即生效。

---

## 六、项目目录结构

```
hongguo-web/
├── README.md                 38KB，事实上的产品说明书（含 21 条 FAQ）
├── package.json              4 个 script：start / selftest / version
├── LICENSE                   MIT
├── .github/workflows/release.yml   版本变更 → 打包 → 发 Release
├── screenshot/               主界面 + 播放器截图
├── scripts/
│   ├── start.sh / start.bat  「薄壳」：只找 Node + 拉起入口
│   ├── stop.sh / stop.bat    按进程类型 + 入口路径双匹配杀进程
│   └── pack.sh               5 平台打包，内置 Node 运行时
├── server/
│   ├── src/
│   │   ├── main.js        67   入口（--config/--stop/--selftest/--version）
│   │   ├── launcher.js   673   启动编排、浏览器打开、banner、端口检查
│   │   ├── server.js     931   HTTP 路由 + 鉴权 + 限流 + 图片代理
│   │   ├── selftest.js  1160   165 项断言
│   │   ├── mp4-fixture.js 337  自检用的最小加密 MP4 夹具
│   │   ├── core/
│   │   │   ├── pure-sign.js  916   六头签名
│   │   │   ├── mp4.js       1247   BMFF + CENC + 减壳
│   │   │   ├── safeguards.js 295   缓存/节流/风控识别/设备池
│   │   │   ├── keystore.js   179   本地密钥 + admin_token 持久化
│   │   │   ├── spade.js      144   spade → key
│   │   │   └── device.js      70   稳定 device_id
│   │   ├── service/
│   │   │   ├── client.js     1339  上游 API 客户端（搜索/榜单/分集）
│   │   │   ├── stream.js      690  下载 → 解密 → 减壳 → 缓存
│   │   │   ├── app-source.js  530  App 取址链路
│   │   │   ├── download.js    286  批量下载任务
│   │   │   ├── res.js         228  JSON/静态/Range 响应
│   │   │   └── web-source.js  192  官网 SSR 兜底
│   │   └── util/
│   │       ├── config.js  188   配置：命令行 > 环境变量 > JSON > 默认
│   │       ├── http.js    139   node:http/https 封装
│   │       ├── json.js    136   手写 JSON（保序 + 数字语义）
│   │       ├── crypto.js   68   MD5 / AES-CTR / hex
│   │       └── log.js      52   UTF-8 输出锁定
│   └── config/
│       ├── config.json       端口/监听/上游参数/调优（带 `_xxx提示` 注释）
│       └── apikeys.json      运行时生成：密钥 + admin_token + device_id（不入库）
└── web/
    ├── index.html   197   骨架：顶栏 / 列表 / 播放器弹层 / 各类卡片
    ├── styles.css  1100   含响应式（320px 起可用）与浅深主题
    └── app.js      2026   全部逻辑，单 IIFE
```

行数分布：后端约 9,000 行（含自检 1,160），前端约 3,300 行，脚本 + CI 约 700 行。

---

## 七、核心代码与关键逻辑

### 7.1 六个签名头（`core/pure-sign.js`）

红果 App 接口要求六个自研签名字段，作者用纯 JS 复刻，不再需要 unidbg 模拟 ARM64 `.so`：

| 头           | 算法                                                                                             |
| ----------- | ---------------------------------------------------------------------------------------------- |
| `x-gorgon`  | RC4 变体 + 位旋转 + 位反转后处理，随机种子 `rndInt(65536)`                                                     |
| `x-khronos` | 秒级时间戳                                                                                          |
| `x-ladon`   | `base64(be32(khronos))`                                                                        |
| `x-argus`   | `base64(le32(khronos))`                                                                        |
| `x-helios`  | MD5 → 64 位字表 → 类 Feistel 变换 → base64                                                           |
| `x-medusa`  | `sm3(query)` + `md5(body)` → `hashF13` 分支 → 手写 protobuf → `xmxor` 混淆 → 逆序 → 异或 → 自研 `AesV3` 加密 |

两个工程细节值得注意：

- **字节级对齐 Java**：`byte[]` 有符号 → Buffer + `& 0xff`；`int` 溢出用 `| 0`；`long` 用 `BigInt`。
- **`branchOf` 抖动规避**：当签名走 `branch == 1` 变体时服务端返回 `110001`。`client.js` 里最多抖 32 次 `_rticket`（`Date.now()+1ms`）直到绕开该分支；`app-source.js` 同样处理。这是实测踩出来的规则，也写进了自检第 11 组。

### 7.2 设备身份（`core/device.js`）

实测结论写在注释里：

- 搜索接口缺 `device_id` → 上游 `code=100103 PARAM_INVALID`（**与签名无关**）
- 有 `device_id` 但带随机 `iid` → HTTP 200 + **空 body**（风控）
- `iid=0` → `code=0` 正常

所以：只生成 `device_id`（16 位十进制，1e15 起），持久化到 `apikeys.json`，优先级 `HONGGUO_DEVICE_ID` > config > apikeys.json > 新生成。身份短期内反复跳变会被判异常流量。

### 7.3 取直链链路（`service/app-source.js`）

老链路 a 域名 `preload/v1` 已对部分分集不再返回 `video_model`，所以统一改走：

1. 合成真机身份（Redmi 25053RT47C / Android 16 / 每请求随机 `iid`、`device_id`）→ 签名 POST **b 域名** `multi_video_model/v1`
2. 响应只给 `fallback_api` 地址，**二次 GET**（不带签名）才拿到 `video_info.data.video_list`
3. 媒体 URL 可能被 `key_seed` 加密：`sha512(seed)` → `sha512(h1‖CONST)` 派生 AES-128-CBC 的 key/IV 解密
4. 白名单校验：接口域 `fqnovel.com` / `snssdk.com`，CDN 域 `qznovelvod.com` / `fqnovelvod.com` / `douyinvod.com`；必须 https、端口 443、无 userinfo —— 防签名 body 被重定向带到第三方
5. 选流：**H.264 优先**（`codecRank` 0/1/2），`best` 时按编码优先级再按像素；指定 `1080p` 时按短边升序

query 编码刻意复刻 Python `urllib.parse.quote`：`! ' ( ) *` 也编码（`resolution=1280*2772` 里的 `*` 就是坑），否则签名用的串和服务端收到的不一致。

### 7.4 解密主链路（`service/stream.js` + `core/mp4.js`）

```
videoTrack(vid,q)  →  spade_a + 加密直链 + encrypt 标志
   ↓
download(url, <vid>_<q>.raw.mp4)      // 先写 .part 再 rename
   ↓
offlineDecrypt(spadeA, raw, raw)      // 原地解密
   ├─ spadeToKey(spadeA) → 16B content key（仅 ver1；ver2 = AES-GCM 不支持）
   ├─ findMoov(FileHeadReader)         // 只读盒头，尾部 moov 也能找到
   ├─ analyzeEncryptedTracks(moov)     // 逐 trak 取 senc/saiz/saio/tenc
   ├─ verifyVideoIv(...)               // ⚠️ 必须先验 IV 再解密
   └─ decryptTrack(store, t, key, iv)  // 逐样本 AES-128-CTR 原地写回
   ↓
toPlayable(raw)  → stripEncryption() → *.play.mp4
                     └ 失败 → ffmpeg remux → 再失败保留 *.raw.mp4
   ↓
pruneCache()  → 按 mtime 保留最新 30 个
```

**几个关键设计**：

- **内存与文件大小解耦**：只把 `moov` 段（几 MB）读进内存，密文流式 copy 到目标文件，再用 `RafStore`（fd 随机读写 + 256KB 缓冲）逐样本原地解密。峰值堆内存只与 `moov` 大小 + 单样本大小有关。注释写明历史实现是 50~200MB/集，32 并发直接撑爆堆。
- **IV 顺序陷阱**：必须 `verify` 再 `decrypt`。若反过来，样本 0 已是明文，`avccSelfOk`（用 NAL 长度链自证）仍会通过，错误的 key 会被误判合法。
- **计数器构造**：CENC 的 AES-128-CTR，`counter = (base_iv_high64 + sampleIndex) << 64`，低 64 位恒 0；8 字节 IV 放高 64 位，16 字节原样保留，缺失回落 `base_iv`。
- **纯 JS 减壳（`stripEncryption`）**：样本已是明文，只需从 `moov` 删掉 `sinf`/`senc`/`saio`/`saiz`/`pssh`，修补所有上层盒 size，`stco` 偏移整体平移指向新 `mdat`，`mdat` 原样拷贝，sample entry 类型按 `frma`/`hvcC` 改回 `hvc1`/`avc1`。实测 1080×1920 整集约 1 秒，转 H.264 要约 16 秒 —— 所以默认不转码，HEVC 直出。
- **缓存三变体**：`<vid>_<q>.play.mp4` / `.mp4` / `.raw.mp4` 都参与命中判定；若命中 `.raw.mp4`，下次会自动补做减壳升级。
- **并发去重**：`INFLIGHT` Map 以输出路径为键，同集并发请求只解密一次。
- **预热**：`/prewarm` 立即返回，实际解密排队执行；执行位 4 + 队列 64，满了退化为调用方同步执行；失败重试 2 次。

### 7.5 批量下载（`service/download.js`）

- 任务制：`POST /download/task {series_id,start,end}` → 立刻返回快照，后台跑
- **同剧只允许一个任务**，重复发起直接复用旧任务（防两任务写同一目录互相覆盖）
- 逐集复用 `ensureDecrypted`（已播过的集命中缓存，直接复制）
- 每集最多 3 次，间隔 800ms；单集成功**不打日志**，只有最终失败一行 WARN + 任务结束一行汇总
- 落盘 `download/<剧名>/第N集.mp4`，先 `.part` 再 rename，不留半成品
- 并发由 `runtime.download.concurrency` 控（默认 2，夹在 1~8）
- 任务表仅内存，保留最近 50 个

### 7.6 服务端安全与限流（`server/src/server.js`）

- 鉴权：`x-api-key` 头或 `api_key` query（后者是给 `<video src>` 用的）；无效 401
- 限流：按 key 分桶，每桶存最近 60s 时间戳，超 120/min 返回 429；桶数 > 4096 时淘汰空闲桶
- 静态资源**优先于鉴权**路由；`path.resolve` + 前缀校验防目录穿越
- `admin_token`：环境变量 > `apikeys.json` > 首次自动生成 64 位口令并写回（重启沿用）
- 图片代理：域名白名单 + 访问序 LRU（64MB 上限、单张 4MB 上限）；命中 HEIC 只把 `Content-Type` 改写成 `image/jpeg`
- 上游风控识别：`checkResponse` 用「状态码集合 + 关键词」双判据区分 `RiskControlError` 与 `AuthExpiredError`，前者指数退避重试，后者刷新登录态；`HONGGUO_SESSION_API_KEY` 托管模式下禁止刷新

### 7.7 配置优先级

```
命令行参数 > 环境变量 > server/config/config.json > 内置默认值
```

`config.json` 用 `_说明` / `_xxx提示` 键做内置文档（透传上游前会剥掉 `_` 开头的键）。`--config` 可打印生效配置。

---

## 八、依赖与运行方式

### 依赖：几乎为零

- `package.json` 的 `dependencies` 是空对象，没有 devDependencies
- 运行时只需 **Node ≥ 18**（因为用到 `crypto.getHashes('sm3')`、`BigInt`、`node:` 前缀）
- ffmpeg **可选**：只在开启转码或纯 JS 减壳不适用时才会调用
- 上游目标：`api5-normal-sinfonlinea/b.fqnovel.com`、`hongguoduanju.com`

### 运行（发行包，推荐）

1. Releases 页下载对应包：Windows-x64 / Windows-arm / Linux-x64 / Linux-arm / macOS-arm
2. 解压，看到 `web/ server/ scripts/ node/ 平台说明.txt`
3. Windows 双击 `scripts\start.bat`；Linux/macOS `bash scripts/start.sh`
4. 自动打开 `http://127.0.0.1:8000/`（start 脚本轮询 `/health` 等就绪，不是固定延时）

### 运行（源码）

```bash
git clone https://github.com/sunnysky123/hongguo-web.git
cd hongguo-web
node server/src/main.js              # 启动
node server/src/main.js --config     # 看生效配置
node server/src/main.js --selftest   # 165 项自检
node server/src/main.js --stop       # 停实例
node server/src/main.js --version    # 1.1.4
```

Node 查找顺序：`node/`（自带）> `NODE_HOME` > `PATH`。都不在会提示并退出，**不会自动下载**。

### 常用环境变量

| 变量                        | 默认                    | 说明                                 |
| ------------------------- | --------------------- | ---------------------------------- |
| `PORT` / `BIND_HOST`      | 8000 / 127.0.0.1      | 监听；改 `0.0.0.0` 必须同时设 `ADMIN_TOKEN` |
| `HONGGUO_STREAM_CACHE`    | `server/stream-cache` | 解密成品缓存                             |
| `HONGGUO_CACHE_MAX_FILES` | 30                    | 缓存文件数上限，0 为不限                      |
| `HONGGUO_DOWNLOAD_DIR`    | `download`            | 下载根目录                              |
| `HG_TRANSCODE`            | 空                     | `1` = HEVC 转 H.264（缺 HEVC 解码器时用）   |
| `HONGGUO_PURE_STRIP`      | 空（启用）                 | `0` = 强制走 ffmpeg remux 对照          |
| `HG_THROTTLE_MS`          | 260                   | 上游串行节流间隔                           |

### 打包与发版

- `bash scripts/pack.sh` 打全平台；`bash scripts/pack.sh Linux-arm` 只打一个；`--list` 列平台
- 首次联网下载约 250MB 运行时，SHA-256 校验后缓存
- CI：`package.json` 的 `version` 与上一提交不同 → 自动打包 5 平台并发 Release；`force=true` 可手动强制

---

## 九、工程质量观察

### 值得学的

1. **注释密度极高，且写「为什么」而不是「做什么」**。很多注释是实测结论（如 `iid` 陷阱、`branch==1` 抖动、IV 顺序陷阱、内存暴涨根因），读注释等于读踩坑日记。
2. **自检是回归基线**，不是覆盖率装饰。165 项断言覆盖 spade 真值、CTR 计数器进位、AVCC 自证、盒解析、端到端逐字节还原、缓存淘汰、减壳盒链完整性等，且「不访问网络、不依赖外部服务」。
3. **零依赖带来的可移植性**：连 JSON 都自己写了一份（`util/json.js`，为了保字段顺序和数字语义），换来任何 Node 18 环境解压即跑。
4. **失败路径都写了兜底**：减壳失败 → ffmpeg remux → raw 明文；密钥拿不到 → 退避重试 + 卡片状态分级；缓存只剩 raw → 下次自动补减壳。

### 需要注意的问题

1. **README 与实际代码有出入**：
   - `/img` 号称「含 HEIC 转换」，实际只把 `Content-Type` 覆写成 `image/jpeg`，字节流是原 HEIC —— 浏览器不支持 HEIC 时封面仍会裂。
   - `/download` 在 API 表里写作「下载解密后的成品」，实际返回 JSON `{ok, path, size}`（给本机路径），不吐文件流。真正的成品下载走 `/download/task` 落盘。
2. **法律与合规风险**：项目实质是绕过第三方平台的访问控制与 DRM（CENC），获取并解密受版权保护的内容。MIT 许可只覆盖代码，不覆盖内容；作者已明确「仅供技术研究」。真要长期使用需自行承担风险。
3. **维护脆弱性**：整条链路依赖逆向出的签名算法、域名、字段名、`key_seed` 格式。上游一改版（比如官网 SSR 已不再输出 `main_url`，本项目就放弃了官网取址），功能会成片失效，只能等作者跟进。
4. **单机定位**：任务表、缓存、限流桶全在内存，重启即丢；无鉴权体系（密钥就是本机自签）。改 `0.0.0.0` 对外相当于把整个解密能力开放出去，README 也警告必须设 `ADMIN_TOKEN`。
5. **代码风格不统一**：大量注释提到「移植自 Java」，能看出这条链是 Node → Java → Node 反复迁移的产物，Java 风格的 helper、`Json.obj()` 式的手写容器、`ByteView` 之类都在，可读性靠注释撑着。

---

## 十、结论

一份**完成度相当高的个人逆向工程作品**：把「App 签名 → 取址 → CENC 解密 → 容器减壳 → HTTP 串流」整条链路用零依赖 Node.js 重写了一遍，还配了 165 项离线自检和 5 平台一键发行。技术上最值得看的是 `core/pure-sign.js`（六头签名复刻）和 `core/mp4.js`（流式 CENC 解密 + 纯 JS 减壳）这两个文件；工程上最值得学的是它「注释写清为什么 + 自检固化回归」的习惯。

作为工具，它解决了「在 PC 浏览器看红果短剧」这个具体痛点；作为工程样本，它是一份很完整的「如何在 JS 里处理加密 MP4」的实践。
