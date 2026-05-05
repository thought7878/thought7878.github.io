参考：[前端安全！XSS攻击！](https://www.bilibili.com/video/BV1cYubzdEiH/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)


`XSS`（Cross-Site Scripting，为避免与 CSS 混淆简写为 XSS）是 Web 安全中**历史最久、危害最广、变种最多**的客户端漏洞。尽管*现代框架与浏览器已大幅降低其发生率*，但**只要存在“用户输入 → 页面渲染”的数据流，XSS 风险就始终存在**。

---

## 一、核心定义与本质

| 维度            | 说明                                                                   |
| ------------- | -------------------------------------------------------------------- |
| **定义**        | 攻击者将恶意脚本注入到目标网页中，当其他用户访问该页面时，浏览器将其作为可信代码执行                           |
| **本质**        | **数据与代码未分离**。浏览器*无法区分“用户提交的数据”与“开发者编写的逻辑”，将不可信输入当作可执行脚本解析*           |
| **执行上下文**     | 恶意脚本在**受害者浏览器**中运行，继承当前页面的源（Origin）、Cookie、DOM 访问权与 API 调用权限         |
| **与 CSRF 区别** | XSS 是“代码注入+主动执行”，可读取数据、伪造任意请求；CSRF 是“诱导浏览器发请求”，无法读取响应，依赖 Cookie 自动携带 |

> 💡 核心原则：**永远不要信任用户的输入、任何来自外部的数据**（URL、表单、API 响应、第三方回调、富文本、甚至 `localStorage`）。

---

## 二、三大分类与攻击链路

| 类型                         | 数据流向                                 | 持久性         | 典型触发场景                                          | 危害等级                 |
| -------------------------- | ------------------------------------ | ----------- | ----------------------------------------------- | -------------------- |
| **反射型（Reflected）**         | 用户请求 → 服务端未过滤直接返回 → 浏览器执行            | 非持久（仅当次请求）  | 钓鱼链接、搜索框、错误提示页、URL 参数回显                         | 中（需诱导点击）             |
| **存储型（Stored/Persistent）** | 用户提交 → 服务端存入 DB/文件 → 其他用户访问时渲染       | 持久（影响所有访问者） | 评论区、个人资料、留言板、CMS 内容、工单系统                        | 高（自动触发，易蠕虫化）         |
| **DOM 型（DOM-based）**       | 前端 JS 读取不可信数据 → 直接写入 DOM 危险 API → 执行 | 可持久可非持久     | `location.hash`、`postMessage`、URL 参数、第三方 SDK 回调 | 高（不经过服务端，传统 WAF 难拦截） |

### 🔍 DOM 型 XSS 的 Source → Sink 模型
```js
// Source（不可信数据来源）
const input = location.hash.slice(1); // #<img src=x onerror=alert(1)>
const msg = new URLSearchParams(location.search).get('name');
const data = JSON.parse(localStorage.getItem('userConfig'));

// Sink（危险执行点）
document.getElementById('box').innerHTML = input;        // ❌ HTML 注入
document.write(`<h1>${msg}</h1>`);                       // ❌ 字符串拼接写入
setTimeout(`console.log('${data}')`, 100);               // ❌ 字符串执行
element.setAttribute('onclick', `handle('${msg}')`);     // ❌ 事件属性注入
```
> DOM XSS 完全在客户端完成，服务端日志与 WAF 通常无法感知。

---

## 三、注入上下文与 Payload 解析

XSS 能否成功，**高度依赖注入点的 HTML/JS 上下文**。不同上下文需要不同的绕过与编码策略：

| 上下文 | 危险点 | 典型 Payload | 防御编码方式 |
|--------|--------|--------------|--------------|
| **HTML 正文** | 直接解析标签 | `<script>alert(1)</script>`<br>`<svg onload=alert(1)>` | HTML Entity 编码：`<`→`&lt;` `>`→`&gt;` `&`→`&amp;` |
| **HTML 属性** | 闭合属性注入事件 | `<input value="x" onfocus=alert(1) autofocus>` | 属性值编码 + 引号包裹：`"`→`&quot;` `'`→`&#x27;` |
| **JavaScript 字符串** | 闭合引号执行代码 | `var name = 'x'; alert(1);//';` | JS 字符串转义：`\` `"` `'` `/` `\n` `\r` 等 |
| **URL / href / src** | `javascript:` 协议或开放重定向 | `<a href="javascript:alert(1)">` | 协议白名单（仅 `http/https/mailto`）+ URL 编码 |
| **Style / CSS** | 表达式或 `url()` 注入（旧浏览器） | `style="background:url(javascript:alert(1))"` | 禁用 `expression`，CSS 值严格过滤，现代浏览器已免疫多数 |
| **模板引擎** | 未转义插值或逻辑注入 | `{{ user.input }}`（若引擎默认不转义） | 启用自动转义，禁用 `raw`/`unescape` 语法 |

> ⚠️ **上下文混淆是绕过主因**：若服务端对 HTML 正文做了编码，但前端将其插入 `innerHTML` 前又用 `decodeURIComponent` 解码，防护即失效。

---

## 四、攻击者能做什么？（危害全景）

一旦 XSS 成功执行，攻击者即获得**与合法用户同等的浏览器上下文权限**：

| 攻击目标 | 实现方式 |
|----------|----------|
| **会话劫持** | 读取非 HttpOnly Cookie / LocalStorage 中的 Token，发送至攻击服务器 |
| **账号接管** | 调用修改密码/绑定手机 API，或伪造表单提交 |
| **数据窃取** | `document.cookie`、`localStorage`、DOM 抓取（如订单、聊天记录）、截图（`html2canvas`） |
| **键盘记录 / 钓鱼** | 监听 `keydown`，或动态插入伪造登录框覆盖原 UI |
| **内网探测 / 端口扫描** | 利用受害者浏览器发起 `fetch('http://192.168.1.1:8080')`，通过 CORS 错误/加载时间判断服务 |
| **蠕虫传播** | 自动发帖/私信携带 Payload（如 2005 年 Samy MySpace 蠕虫，20 小时感染 100 万用户） |
| **资源滥用** | 植入挖矿脚本、广告劫持、DDoS 肉鸡 |

> 📌 注意：即使 Cookie 设置 `HttpOnly`，XSS 仍可**以用户身份发起请求**（Session Riding），完成转账、发帖、删数据等操作。因此 XSS 权限 > CSRF。

---

## 五、现代纵深防御体系

XSS 无法靠单一手段根除，必须采用**多层协同防御**：

### 1. 输出编码（Output Encoding）✅ 核心防线
- **原则**：在数据写入渲染上下文前，按目标上下文进行编码。
- **不要依赖输入过滤**：输入校验用于业务逻辑，输出编码用于安全。
- **上下文感知编码示例**：
  ```js
  // HTML 正文
  function escapeHTML(str) {
    return str.replace(/[&<>"']/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#x27;'}[m]));
  }
  // 现代框架已内置，无需手动实现
  ```

### 2. 安全 DOM 操作 ✅ 前端责任
| 危险 API | 安全替代 |
|----------|----------|
| `element.innerHTML = html` | `element.textContent = text` 或 `DOMPurify.sanitize(html)` |
| `document.write(html)` | 移除或改用 `createElement` + `appendChild` |
| `eval(str)` / `new Function(str)` | 解析 JSON 用 `JSON.parse()`，逻辑用白名单映射 |
| `setTimeout(str, ms)` / `setInterval(str, ms)` | 传入函数引用：`setTimeout(fn, ms)` |
| `element.setAttribute('onclick', str)` | `element.addEventListener('click', fn)` |

### 3. 富文本安全处理
- **绝不直接使用 `innerHTML` 渲染用户富文本**
- 使用专业清洗库：`DOMPurify`（推荐）、`sanitize-html`
- 配置最小化白名单：
  ```js
  import DOMPurify from 'dompurify';
  const clean = DOMPurify.sanitize(dirtyHTML, {
    ALLOWED_TAGS: ['p', 'b', 'i', 'a', 'ul', 'li', 'img'],
    ALLOWED_ATTR: ['href', 'src', 'alt', 'title'],
    ALLOWED_URI_REGEXP: /^(https?|mailto):/i // 禁止 javascript:
  });
  ```

### 4. 浏览器层防御（配合 CSP）
- **CSP**：阻断未授权脚本执行（详见前文 CSP 详解）
- **Trusted Types**：强制 DOM 注入点使用安全类型，根治 DOM XSS
  ```http
  Content-Security-Policy: require-trusted-types-for 'script';
  ```
- **HttpOnly + SameSite Cookie**：防 Cookie 窃取与 CSRF 协同
- **X-XSS-Protection**：⚠️ 已废弃（Chrome/Edge/Firefox 均移除），现代浏览器依赖 CSP

---

## 六、前端框架的 XSS 防护与陷阱

| 框架 | 默认防护机制 | 危险逃生舱（Escape Hatch） | 常见误用 |
|------|--------------|---------------------------|----------|
| **React** | JSX 插值自动 HTML 编码 | `dangerouslySetInnerHTML` | 直接传入未清洗的 API 响应或 URL 参数 |
| **Vue** | `{{ }}` 与 `v-bind` 自动转义 | `v-html`、`v-bind:innerHTML` | 在组件中透传用户输入未过滤 |
| **Angular** | 内置上下文感知 sanitizer | `bypassSecurityTrustHtml/Script/Url` | 滥用 `DomSanitizer` 跳过校验 |
| **Svelte** | `{expr}` 自动转义 | `{@html expr}` | 未配合 sanitize 直接渲染 |

✅ **框架安全守则**：
1. 默认插值永远安全，**不要手动关闭转义**
2. 必须渲染 HTML 时，走 `DOMPurify` 管道
3. 第三方 UI 库（如富文本编辑器、Markdown 渲染器）需审查其 XSS 历史与配置
4. 避免在模板中拼接字符串生成 HTML/JS

---

## 七、高级绕过与典型漏洞场景

| 绕过手法 | 原理 | 防御要点 |
|----------|------|----------|
| **双重编码 / 解码顺序错误** | 服务端编码一次，前端 `decodeURIComponent` 后写入 DOM | 统一在最终渲染点编码，避免中间层反复编解码 |
| **JSONP 端点滥用** | `callback=<script>alert(1)</script>` 被直接拼接返回 | 废弃 JSONP，改用 CORS；或严格校验 callback 格式（仅字母数字） |
| **SVG / Markdown 解析器漏洞** | 解析器未过滤 `onload`、`javascript:`、`<iframe>` | 使用安全解析器（如 `marked` + `DOMPurify`），禁用危险标签/属性 |
| **Mutation XSS (mXSS)** | 浏览器 HTML 解析器 quirks 导致清洗后仍生成可执行代码 | 使用最新版 DOMPurify，避免自定义正则清洗 |
| **CSP 配置错误绕过** | `script-src` 包含开放 CDN、JSONP 端点、或 `unsafe-inline` | 严格白名单 + `nonce` + `strict-dynamic`，定期用 CSP Evaluator 检查 |
| **PostMessage 未验 origin** | 恶意窗口发送 `{html: '<img onerror=...>'}`，接收方直接 `innerHTML` | 必须校验 `event.origin`，内容走 sanitize |

---

## 八、检测、审计与工程化实践

### 🔍 代码审计重点（SAST）
```bash
# 全局搜索危险 Sink
grep -rn "innerHTML\|document\.write\|eval(\|setTimeout(.*string\|v-html\|dangerouslySetInnerHTML\|bypassSecurityTrust" src/
```
- 检查所有用户输入源：`location.*`、`URLSearchParams`、`postMessage`、`localStorage`、API 响应
- 确认每个 Sink 前有编码/清洗/框架保护

### 🧪 动态测试（DAST）
- 工具：Burp Suite Scanner、OWASP ZAP、XSStrike、dalfox
- 手动 Fuzz：在参数、Header、文件名、JSON 字段注入 `<svg/onload=alert(1)>`、`"onfocus=alert(1) autofocus="`
- 观察：控制台 CSP 违规、网络请求外联、DOM 结构变化

### 🏗 工程化落地
| 阶段 | 实践 |
|------|------|
| **开发** | ESLint 规则：`no-eval`、`no-implied-eval`、`react/no-danger`、`vue/no-v-html` |
| **构建** | 自动化扫描依赖漏洞；CSP Nonce 注入插件；Trusted Types Polyfill |
| **测试** | CI 集成 DAST；CSP Report-Only 收集误报；模糊测试核心输入点 |
| **运行** | 部署严格 CSP；监控 `window.onerror` 与 CSP 上报；敏感操作二次验证 |

---

## 九、总结与核心原则

### 🔑 防御黄金法则
1. **数据与代码严格分离**：浏览器执行的一切必须来自可信源
2. **输出编码 > 输入过滤**：在最终渲染上下文进行编码，而非在入口处“猜”用途
3. **默认安全**：使用框架默认插值，禁用危险 API，富文本必清洗
4. **纵深防御**：编码 + CSP + HttpOnly + Trusted Types + 权限最小化
5. **持续监控**：CSP Report-Only 灰度 → 上报分析 → 策略收紧 → 自动化门禁

### 📊 XSS 防护成熟度模型
| 级别 | 特征 |
|------|------|
| L1 基础 | 手动转义，无 CSP，Cookie 无 HttpOnly |
| L2 规范 | 框架默认防护，关键接口编码，基础 CSP |
| L3 强化 | 富文本 DOMPurify，严格 CSP（nonce+strict-dynamic），HttpOnly+SameSite |
| L4 现代 | Trusted Types 强制，CSP 上报监控，CI/CD 安全门禁，零 unsafe 策略 |

> 🌐 趋势：浏览器正推动 **Secure by Default**。未来 XSS 将更多出现在“开发者主动绕过安全机制”或“第三方供应链污染”场景。安全左移、自动化策略生成、运行时行为监控将成为标配。

如需针对具体场景提供**可落地的方案**（如：Vue 项目 v-html 安全改造、DOMPurify 与 CSP 协同配置、Trusted Types 迁移指南、或 XSS 漏洞应急响应流程），可提供技术栈与业务形态，我将输出完整代码与配置模板。