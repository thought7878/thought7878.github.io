[CSRF（跨站请求伪造）攻击与防御](https://www.bilibili.com/video/BV1xF4m1M7kQ/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)、[CSRF / XSS：攻击和防御的应用层故事](https://v.douyin.com/2nVDoa661qQ/)


`CSRF（Cross-Site Request Forgery）`是 Web 安全中**利用浏览器信任机制**的经典攻击。与 XSS 的“代码注入”不同，CSRF 不窃取数据，而是 **“借刀杀人”** ：*诱导已登录用户的浏览器，以用户身份向目标网站发起非自愿的操作请求*。

尽管现代浏览器默认策略已大幅压缩其生存空间，*但在复杂业务架构、子域名共享、第三方嵌入等场景中，CSRF 仍是高危漏洞*。理解其原理与防御，是构建可信 Web 应用的必修课。

---

## 一、核心定义与本质

| 维度       | 说明                                                             |
| -------- | -------------------------------------------------------------- |
| **定义**   | 攻击者*构造恶意页面，诱导已登录用户访问，利用浏览器自动携带目标站凭证（Cookie）的特性，伪造用户意图发起状态变更请求* |
| **本质**   | **身份冒用 + 凭证自动携带 + 缺乏请求意图验证**。服务端*无法区分请求是用户主动操作，还是第三方页面诱导触发*    |
| **攻击目标** | *资金转账、修改密码/绑定信息、删除数据、关注/点赞、订单确认*等**写操作**                       |
| **核心特征** | “盲打”（攻击者无法读取响应）、依赖用户登录态、跨域发起、服务端日志显示为合法用户操作                    |

> 💡 `核心原则`：Cookie 证明“你是谁”，但不证明“你想做什么”。*CSRF 防御的核心*是验证请求意图。

---

## 二、攻击原理与完整链路

```
用户浏览器                目标站 (bank.com)              恶意站 (evil.com)
   │                          │                              │
   │  1. 登录成功，设置 Session Cookie                        │
   ├─────────────────────────►│                              │
   │◄─────────────────────────┤ Set-Cookie: sid=abc123       │
   │                          │                              │
   │  2. 未退出，访问恶意页面                                 │
   ├────────────────────────────────────────────────────────►│
   │                          │                              │
   │  3. 页面自动提交隐藏表单/加载资源                        │
   │◄────────────────────────────────────────────────────────┤ <form action="https://bank.com/api/transfer" method="POST">
   │                          │                              │   <input name="to" value="attacker">
   │  4. 浏览器自动携带 bank.com 的 Cookie 发起跨域请求       │   <input name="amount" value="10000">
   ├─────────────────────────►│                              │ </form><script>document.forms[0].submit()</script>
   │  (携带 Cookie: sid=abc123)│                              │
   │                          │ 5. 服务端验证 Cookie 有效，执行转账 │
   │◄─────────────────────────┤ 200 OK (转账成功)            │
```

🔑 **关键点**：
- 浏览器遵循 Cookie 的 `Domain`/`Path` 规则，**自动附加**匹配域名的 Cookie，无论请求来源。
- 攻击者不需要知道 Cookie 内容，只需诱导浏览器发请求。
- 服务端若仅依赖 Cookie 鉴权，无法识别请求是否出自用户本意。

---

## 三、为什么同源策略（SOP）防不住 CSRF？

| SOP 限制范围        | CSRF 行为                                      | 结果               |
| --------------- | -------------------------------------------- | ---------------- |
| 阻止跨域**读取响应**    | 仅跨域**发送请求**，不关心响应内容                          | ✅ SOP 不拦截        |
| 阻止跨域 DOM 访问     | 不操作 DOM，仅触发表单/标签请求                           | ✅ SOP 不拦截        |
| 限制 XHR/fetch 跨域 | 传统 CSRF 使用 `<form>`/`<img>`/`<link>` 等标签发起请求 | ✅ 标签请求不受 CORS 限制 |

> ⚠️ **核心结论**：SOP 保护的是**数据机密性**，CSRF 利用的是**身份信任链**。两者防御维度不同。

---

## 四、典型触发方式与现代演进

| 触发方式 | 原理 | 现代环境有效性 |
|----------|------|----------------|
| **GET 型** | `<img src="https://bank.com/api/delete?id=1">` | ⚠️ 仍有效（若后端用 GET 执行写操作）。现代浏览器 `SameSite=Lax` 允许顶级 GET 导航携带 Cookie |
| **POST 表单型** | 隐藏 `<form>` + JS 自动 `submit()` | ✅ 最常用。`SameSite=Lax` 会拦截跨站 POST，但若用户从恶意站点击链接跳转（顶级导航），仍可能触发 |
| **JSON API 型** | `fetch()` 发送 `application/json` | ❌ 默认触发 CORS 预检（OPTIONS），跨域被拦截。**除非目标站 CORS 配置错误**（如 `Allow-Origin: *` + `Allow-Credentials: true`） |
| **子域名攻击** | `evil.sub.bank.com` 向 `api.bank.com` 发请求 | ✅ Cookie `Domain=.bank.com` 时共享。SameSite 不防同站（Same-Site）子域 |

> 📌 现代前后端分离架构中，**JSON API + 自定义请求头** 天然具备一定 CSRF 免疫力（触发 CORS 预检），但不可作为唯一防线。

---

## 五、纵深防御体系（核心方案）

CSRF 防御必须**以服务端为主，前端与浏览器策略协同**。

### 🔒 1. CSRF Token（同步器令牌模式）✅ 最可靠
- **原理**：服务端生成加密随机 Token，下发给前端。前端在每次状态变更请求中携带（Header 或 Form 隐藏字段），服务端校验一致性。
- **流程**：
  ```http
  # 1. 服务端下发（可放在 Cookie 或 HTML meta 中）
  Set-Cookie: csrf_token=xyz789; SameSite=Strict; Secure
  
  # 2. 前端读取并附加到请求头
  fetch('/api/transfer', {
    method: 'POST',
    headers: { 'X-CSRF-Token': 'xyz789' }
  })
  
  # 3. 服务端校验：Cookie 中的 Token == Header 中的 Token
  ```
- **要求**：Token 必须不可预测、每会话/每请求更新、与用户会话绑定。

### 🔒 2. 双重提交 Cookie（Double Submit Cookie）✅ 适合无状态/SPA
- **原理**：服务端将 CSRF Token 写入 Cookie（前端可读），前端 JS 读取该 Cookie 并放入请求头/Body。服务端比对两者是否一致。
- **优势**：无需服务端存状态，适合 JWT/微服务架构。
- **局限**：若存在 XSS，攻击者可读取 Cookie 并伪造 Header。**前提是无 XSS**。

### 🔒 3. 验证 Origin / Referer 头 ✅ 辅助防线
- **原理**：服务端检查请求头中的 `Origin`（优先）或 `Referer`，确认来源域名是否在白名单内。
- **配置示例**（Node.js/Express）：
  ```js
  app.use((req, res, next) => {
    if (['POST','PUT','DELETE'].includes(req.method)) {
      const origin = req.headers.origin || req.headers.referer;
      if (!origin || !origin.startsWith('https://bank.com')) {
        return res.status(403).send('CSRF: Invalid Origin');
      }
    }
    next();
  });
  ```
- **局限**：隐私策略/代理可能隐藏 Referer；`Origin` 在部分 GET 导航中不携带；不可作为唯一防线。

### 🔒 4. 自定义请求头 + CORS 预检拦截
- **原理**：前端强制携带自定义头（如 `X-Requested-With: XMLHttpRequest`）。浏览器对跨域请求自动发 `OPTIONS` 预检，恶意站无法伪造预检响应，请求被拦截。
- **适用**：纯 JSON API 架构。需确保服务端不盲目响应 `OPTIONS`。

### 🔒 5. 敏感操作二次验证
- 支付、改密、解绑等关键操作，强制要求输入密码、短信验证码、生物识别或 TOTP。
- **价值**：即使 CSRF 成功发起请求，也无法完成最终操作。

---

## 六、现代浏览器默认防护：SameSite 革命

`SameSite` Cookie 属性是浏览器层防御 CSRF 的里程碑。现代浏览器（Chrome 80+ / Firefox 79+ / Safari 13+）默认将其设为 `Lax`。

| 属性值 | 跨站请求是否携带 Cookie | 适用场景 | CSRF 防护强度 |
|--------|------------------------|----------|----------------|
| `Strict` | ❌ 任何跨站请求都不携带 | 内部系统、高安全后台 | ⭐⭐⭐⭐⭐（最强，但影响用户体验） |
| `Lax`（默认） | ✅ 仅允许**顶级导航 GET** 请求携带<br>❌ 拦截 POST/iframe/img/fetch 跨站请求 | 绝大多数现代 Web 应用 | ⭐⭐⭐⭐（防御大部分 CSRF） |
| `None` | ✅ 所有跨站请求都携带 | 第三方嵌入、跨站 SSO、支付网关 | ⭐（必须配合 `Secure`，且需应用层 CSRF 防护） |

> ⚠️ **SameSite 不是银弹**：
> 1. `Lax` 允许顶级 GET 导航，若后端用 GET 执行写操作仍 vulnerable
> 2. 子域名共享 Cookie（`Domain=.example.com`）视为 Same-Site，不拦截
> 3. 旧浏览器/WebView 兼容性需兜底
> 4. **必须与 CSRF Token 配合使用**

---

## 七、CSRF vs XSS 核心区别

| 维度 | CSRF | XSS |
|------|------|-----|
| **攻击本质** | 伪造用户请求（身份冒用） | 注入恶意脚本（代码执行） |
| **依赖条件** | 用户已登录 + 浏览器自动携带 Cookie | 页面存在未过滤的渲染点 |
| **能力边界** | 只能发请求（盲打），**无法读取响应** | 完全控制浏览器上下文，可读写 DOM/Cookie/发起任意请求 |
| **防御核心** | 验证请求意图（Token/Origin/SameSite） | 数据与代码分离（编码/CSP/Trusted Types） |
| **相互关系** | XSS 可完全绕过 CSRF 防护（读取 Token 或伪造同源请求） | CSRF 无法利用 XSS 漏洞 |
| **日志特征** | 显示为合法用户正常操作 | 可能包含异常脚本加载或外联请求 |

> 🔑 **关键结论**：防住 XSS 是防住 CSRF 的前提之一。若存在 XSS，所有 CSRF 防护均可被绕过。

---

## 八、常见误区与工程实践

### 🚫 高危误区
| 误区 | 事实 |
|------|------|
| “用了 HTTPS 就防 CSRF” | HTTPS 仅加密传输，不验证请求意图。CSRF 请求同样走 HTTPS |
| “前后端分离用 JSON 绝对安全” | 若 CORS 配置错误（如反射 Origin + 允许凭证），或后端接受 `text/plain`，仍可被攻击 |
| “验证 Referer 就够了” | Referer 可被客户端策略屏蔽、代理篡改，且部分场景不携带 |
| “SameSite=Lax 可替代 Token” | Lax 不防子域攻击、不防 GET 写操作、旧端不兼容。Token 仍是应用层基线 |

### 🛠 工程化落地清单
1. **网关/中间件统一拦截**：在 API Gateway 或框架中间件层集中校验 Origin/Token，避免业务代码遗漏。
2. **Token 生命周期管理**：登录时生成，敏感操作前刷新，登出时失效。避免长期固定。
3. **无状态架构适配**：JWT 项目使用 Double Submit Cookie 或独立 CSRF Token 接口。
4. **CI/CD 安全门禁**：集成 `eslint-plugin-security`、ZAP/Burp CSRF 扫描，拦截未防护的写接口。
5. **监控与告警**：记录 `Origin` 校验失败、Token 不匹配事件，高频触发视为攻击尝试。

---

## 九、总结与防护基线

### 🔑 核心原则
1. **不信任自动携带的凭证**：Cookie 证明身份，不证明意图。
2. **写操作必须验证来源**：所有 `POST/PUT/DELETE/PATCH` 接口强制 CSRF 防护。
3. **遵循 RESTful 规范**：`GET` 仅用于安全、幂等的读操作，绝不改变状态。
4. **纵深防御**：`SameSite`（浏览器基线） + `CSRF Token`（应用层核心） + `Origin 校验`（辅助） + `二次验证`（关键操作）。

### 📋 快速自查清单
- [ ] 所有状态变更接口是否拒绝 `GET` 方法？
- [ ] 是否配置 `SameSite=Lax` 或 `Strict`？（第三方嵌入除外）
- [ ] 是否实现 CSRF Token 校验（Header/Form）？
- [ ] 网关/中间件是否统一校验 `Origin`/`Referer`？
- [ ] CORS 配置是否避免 `Allow-Origin: *` + `credentials: true`？
- [ ] 敏感操作（支付/改密/解绑）是否要求二次验证？
- [ ] 是否已修复所有 XSS 漏洞？（XSS 可绕过 CSRF 防护）

> 🌐 趋势：浏览器默认策略日益严格（SameSite 默认化、第三方 Cookie 淘汰），CSRF 攻击面持续收窄。但业务架构复杂化（微服务、子域共享、跨端嵌入）要求**应用层 Token 校验不可替代**。安全设计应“默认拒绝意图不明的写请求”，而非依赖浏览器兜底。

如需针对具体架构输出**可落地的方案**（如：Spring Security CSRF 配置、Next.js/Express 双重提交实现、微服务网关统一校验中间件、或 CSRF Token 与 JWT 协同架构），可提供技术栈，我将提供完整代码与部署模板。