参考：[CSP内容安全策略【渡一教育】](https://www.bilibili.com/video/BV1ZzHDz5Ewu/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)

`CSP（Content Security Policy，内容安全策略）` 是现代 Web 安全体系中**防御 XSS、数据注入与资源劫持的核心机制**。它*通过白名单模型告诉浏览器*：“**只允许加载/执行我明确声明的资源，其余一律拦截**”。CSP 不替代输入校验与输出转义，而是作为**纵深防御的最后一道运行时屏障**。

---

## 一、CSP 核心定位与工作原理

| 维度       | 说明                                                     |
| -------- | ------------------------------------------------------ |
| **本质**   | 由服务端通过 HTTP 响应头（或 `<meta>`）下发的浏览器安全策略                  |
| **执行方**  | 浏览器内核（渲染/网络层），服务端无法绕过                                  |
| **核心思想** | 默认拒绝（Default-Deny） + 显式授权（Whitelist） + 违规上报（Reporting） |
| **防护目标** | XSS（反射/存储/DOM型）、数据外泄、点击劫持、恶意第三方资源加载、Base/Form 劫持       |
| **局限**   | 不防服务端漏洞、不替代输出编码、对已注入的纯数据窃取（如 API 响应）需配合其他策略            |

> 💡 CSP 不是“开发限制”，而是“安全契约”。现代框架与构建工具已高度适配，合理配置几乎不影响正常业务。

---

## 二、策略语法与核心指令

`CSP 策略`由**指令（Directives）** 与 **源表达式（Source Expressions）** 组成，多个指令用分号分隔：

```http
Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-abc123'; frame-ancestors 'none';
```

### 核心指令分类

| 指令 | 控制范围 | 常用值示例 | 说明 |
|------|----------|------------|------|
| `default-src` | 所有未明确指定指令的 fallback | `'self'` | 建议始终设置，作为安全基线 |
| `script-src` | JS 执行与加载 | `'self' 'nonce-xxx' 'strict-dynamic'` | **最核心指令**，直接决定 XSS 防护强度 |
| `style-src` | CSS 加载与内联样式 | `'self' 'unsafe-inline'` | 现代浏览器支持 nonce/hash，但工程化支持较弱 |
| `img-src` / `font-src` / `media-src` | 图片/字体/音视频 | `'self' data: https:` | 控制静态资源来源 |
| `connect-src` | XHR/fetch/WebSocket/EventSource | `'self' https://api.example.com wss:` | 限制数据外传与 API 调用 |
| `frame-src` / `child-src` | iframe/worker 嵌入 | `'self' https://pay.example.com` | 控制子框架来源 |
| `frame-ancestors` | 允许哪些页面嵌入本页 | `'none'` / `'self'` / `https://partner.com` | 替代 `X-Frame-Options`，防点击劫持 |
| `base-uri` | `<base>` 标签允许的来源 | `'self'` | 防 Base 劫持（篡改相对路径解析） |
| `form-action` | 表单提交目标 | `'self' https://checkout.example.com` | 防表单重定向攻击 |
| `upgrade-insecure-requests` | 自动升级 HTTP 为 HTTPS | （无值） | 平滑迁移 HTTPS 站点 |
| `report-uri` / `report-to` | 违规上报端点 | `/csp-report` / `csp-endpoint` | 用于灰度与监控 |

### 源表达式（Source Values）

| 表达式 | 含义 | 安全建议 |
|--------|------|----------|
| `'self'` | 同源 | ✅ 推荐基线 |
| `'none'` | 禁止任何来源 | ✅ 用于 `object-src`、`frame-ancestors` |
| `https:` / `*.example.com` | 协议或域名通配 | ⚠️ `script-src` 中慎用，易被绕过 |
| `'unsafe-inline'` | 允许内联 `<script>`/`<style>`/事件属性 | 🚫 脚本中绝对避免；样式中可暂用 |
| `'unsafe-eval'` | 允许 `eval()`/`new Function()`/模板编译 | 🚫 现代构建工具已无需，彻底禁用 |
| `'nonce-xxx'` | 一次性随机令牌 | ✅ 现代 CSP 核心机制 |
| `'sha256-xxx'` | 脚本/样式内容哈希 | ✅ 适用于静态内联代码 |
| `'strict-dynamic'` | 信任链传递（CSP Level 3） | ✅ 解决动态加载依赖问题 |
| `'wasm-unsafe-eval'` | 允许 WebAssembly 编译 | ⚠️ 仅 WASM 项目需要 |

---

## 三、关键安全机制详解

### 1. Nonce（一次性令牌）
- **原理**：服务端为每次请求生成加密随机字符串，注入 HTML 与 CSP 头。浏览器仅执行匹配 nonce 的脚本。
- **要求**：
  - 每次请求必须不同（不可缓存）
  - 至少 128 位加密随机数（如 `crypto.randomUUID()` 或 `openssl rand -hex 16`）
  - 不可出现在 URL、日志或可被 JS 读取的位置
- **示例**：
  ```http
  Content-Security-Policy: script-src 'self' 'nonce-a1b2c3d4e5f6';
  ```
  ```html
  <script nonce="a1b2c3d4e5f6">console.log('safe');</script>
  ```

### 2. Hash（内容哈希）
- **原理**：对内联脚本/样式计算 `sha256/sha384/sha512` 哈希，写入 CSP。内容不变则哈希有效。
- **适用**：静态内联代码（如 GA 埋点、Critical CSS）。
- **生成**：`echo -n "console.log(1)" | openssl dgst -sha256 -binary | base64`
- **限制**：内容微调即失效，不适合动态渲染或频繁变更的代码。

### 3. `'strict-dynamic'`（CSP Level 3 核心）
- **作用**：允许被 nonce/hash 信任的脚本**动态创建的其他脚本**自动获得执行权限，无需重复添加 nonce。
- **行为**：
  - 启用后，`script-src` 中的域名白名单（如 `https:`、`*.cdn.com`）**自动失效**
  - 仅信任“由可信脚本创建的脚本”，阻断攻击者直接注入的 `<script src="evil.js">`
- **示例**：
  ```http
  script-src 'self' 'nonce-xxx' 'strict-dynamic';
  ```
  ✅ 现代 SPA/SSR 项目强烈推荐，彻底解决第三方库动态加载问题。

### 4. 违规上报（Reporting）
- `report-uri`（旧版）：直接指定 URL，浏览器 POST JSON 违规报告。
- `report-to`（新版）：配合 `Report-To` 响应头使用，支持批量上报与重试。
- **报告结构**：
  ```json
  {
    "csp-report": {
      "document-uri": "https://example.com/page",
      "violated-directive": "script-src",
      "effective-directive": "script-src",
      "blocked-uri": "https://evil.com/xss.js",
      "line-number": 42,
      "source-file": "https://example.com/app.js"
    }
  }
  ```
- **用途**：灰度期收集误报、监控线上攻击尝试、自动化策略调优。

---

## 四、部署模式与灰度发布流程

| 模式 | 响应头 | 浏览器行为 | 适用阶段 |
|------|--------|------------|----------|
| **Report-Only** | `Content-Security-Policy-Report-Only` | 仅上报违规，不拦截 | 初期接入、策略调优 |
| **Enforce** | `Content-Security-Policy` | 拦截违规资源 + 上报 | 生产环境正式启用 |

### 🔄 标准落地流程
1. **部署 Report-Only**：配置宽松策略，收集 1~2 周真实流量报告。
2. **分析日志**：区分“业务必需”与“恶意/冗余”请求，修复误报（如补充 CDN 域名、替换内联脚本）。
3. **收紧策略**：移除 `unsafe-*`，引入 `nonce`/`strict-dynamic`，限制 `connect-src`/`form-action`。
4. **切换 Enforce**：灰度 10% → 50% → 100%，持续监控上报端点。
5. **常态化运营**：将 CSP 纳入 CI/CD 校验，新增第三方资源需同步更新策略。

---

## 五、现代 Web 最佳实践配置模板

### ✅ 推荐基线（SPA/SSR 通用）
```http
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' 'nonce-{RANDOM}' 'strict-dynamic';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  font-src 'self' https://fonts.gstatic.com;
  connect-src 'self' https://api.example.com wss://ws.example.com;
  frame-src 'self' https://pay.example.com;
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self';
  upgrade-insecure-requests;
  report-to csp-endpoint;
```

### 📝 关键说明
- `style-src 'unsafe-inline'`：当前工程化对 CSS nonce 支持较弱，且 CSS 注入危害远低于 JS，可暂保留。后续可迁移至哈希或构建时提取。
- `connect-src`：明确列出 API 与 WebSocket 域名，阻断数据外传（如 `https://attacker.com/steal`）。
- `frame-ancestors 'none'`：彻底防点击劫持，比 `X-Frame-Options: DENY` 更灵活。
- `base-uri` / `form-action`：常被忽略，但可防相对路径劫持与表单重定向。

---

## 六、常见陷阱与绕过风险（高危配置）

| 错误配置 | 风险 | 修复方案 |
|----------|------|----------|
| `script-src 'unsafe-inline'` | 完全丧失 XSS 防护 | 改用 `nonce` + `strict-dynamic` |
| `script-src https:` 或 `*` | 任意 HTTPS 站点可加载恶意 JS | 明确域名白名单，或依赖 `strict-dynamic` |
| 允许已知 JSONP 端点 | 攻击者可构造回调执行任意代码 | 移除 JSONP 域名，或改用 CORS API |
| `script-src` 包含 `data:` | `data:text/javascript,alert(1)` 可执行 | 移除 `data:`，或仅限 `img-src` 使用 |
| Nonce 可预测/被缓存/泄露 | 攻击者复用 nonce 注入脚本 | 服务端每请求生成加密随机数，禁止 CDN 缓存 HTML |
| 缺少 `base-uri` / `form-action` | `<base href="https://evil.com/">` 劫持所有相对请求 | 显式设置为 `'self'` 或业务域名 |
| `report-uri` 未做鉴权/限流 | 攻击者伪造海量报告打垮日志服务 | 上报接口加签名/频率限制/异步队列 |

> ⚠️ **CSP 绕过经典案例**：若 `script-src` 包含 `https://cdn.example.com`，而该 CDN 支持用户上传 JS 或存在开放重定向，攻击者即可绕过 CSP。白名单必须“最小化+可信”。

---

## 七、调试、监控与工程化落地

### 🛠 开发调试
- **浏览器控制台**：违规时输出 `[Report Only]` 或 `Refused to load/execute...` 警告。
- **DevTools → Security 面板**：查看当前生效策略、隔离状态、违规统计。
- **本地绕过**：仅调试时可用浏览器插件临时禁用 CSP，**绝不可用于生产**。

### 📊 监控与上报
- 自建端点：接收 JSON，解析 `violated-directive`、`blocked-uri`，接入 ELK/Prometheus。
- 第三方服务：`report-uri.com`、`sentry.io`（支持 CSP 报告聚合与去重）。
- 告警规则：同一 IP/页面高频违规 → 疑似攻击；新域名频繁出现 → 策略遗漏。

### ⚙️ 工程化集成
| 技术栈 | 实现方式 |
|--------|----------|
| **Nginx** | `add_header Content-Security-Policy "...";`（Nonce 需结合 `sub_filter` 或后端生成） |
| **Node.js/Express** | `helmet({ contentSecurityPolicy: { directives: {...} } })` |
| **Next.js** | `next.config.js` → `headers()` 动态注入，支持每请求 Nonce |
| **Vite/Webpack** | 插件自动生成内联脚本 Hash；构建时校验策略完整性 |
| **CI/CD** | `csp-validator`、`eslint-plugin-security` 拦截危险配置；策略文件版本化管理 |

---

## 八、总结与演进方向

### 🔑 核心原则
1. **默认严格**：从 `default-src 'none'` 起步，按需放开。
2. **消灭 unsafe**：`unsafe-inline`/`unsafe-eval` 是 CSP 失效的主因。
3. **信任链管理**：优先 `nonce` + `strict-dynamic`，替代域名白名单。
4. **灰度先行**：Report-Only → 分析 → Enforce，避免线上阻断。
5. **持续运营**：CSP 不是“配完即忘”，需配合上报监控与依赖变更同步更新。

### 🌐 演进趋势
- **CSP Level 3 普及**：`strict-dynamic`、`wasm-unsafe-eval`、路径匹配（`https://cdn.com/js/`）成为标配。
- **与 Trusted Types 深度绑定**：CSP 控制“加载源”，Trusted Types 控制“DOM 注入点”，双剑合璧根治 DOM XSS。
- **自动化策略生成**：构建工具扫描资源依赖，自动输出最小化 CSP；AI 辅助分析上报日志调优策略。
- **浏览器默认收紧**：未来可能默认启用基础 CSP 或强制 Report-Only，推动生态安全基线提升。

---

📎 **附：快速自查清单**
- [ ] 是否已移除 `script-src` 中的 `unsafe-inline` / `unsafe-eval`？
- [ ] 是否使用每请求加密 Nonce + `strict-dynamic`？
- [ ] 是否限制 `connect-src`、`form-action`、`base-uri`？
- [ ] 是否配置 `frame-ancestors 'none'` 防点击劫持？
- [ ] 是否部署 Report-Only 并建立上报监控？
- [ ] HTML 是否禁止 CDN 缓存（保障 Nonce 唯一性）？

如需针对具体场景输出**可落地的配置代码**（如 Next.js 动态 Nonce 注入、Nginx 上报端点配置、CSP 与 Vite 构建集成、或上报日志分析脚本），可提供技术栈与业务形态，我将提供完整工程方案。