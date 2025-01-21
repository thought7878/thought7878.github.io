## Cookie

Cookie 是前端开发中用于在*浏览器和服务器*之间\*存储和传递**\*少量数据**的一种机制。

### 基本概念

**Cookie 本质上是一小段文本信息**，_由服务器发送到用户的浏览器，浏览器会将其存储在本地_。之后，_当浏览器向同一服务器发送请求时_，会**自动**将相应的 Cookie 包含在*请求头中*发送给服务器，**从而实现服务器对用户状态的跟踪**等功能。

### 主要用途

- **用户身份识别**：当用户登录网站时，服务器可以通过设置 Cookie 来记录用户的登录状态和身份信息，下次用户访问网站时，服务器通过*读取 Cookie 就能识别用户身份*，**无需用户再次登录**。
- **记录用户偏好**：网站可以使用 Cookie _记录用户的个性化设置_，如*字体大小、颜色主题、语言偏好*等，当用户下次访问时，网站根据 Cookie 中的信息为用户呈现符合其偏好的界面。
- **购物车功能**：在电商网站中，Cookie 可用于*记录用户添加到购物车中的商品信息*，即使用户关闭了浏览器，再次打开时购物车中的商品依然存在。
- **跟踪用户行为**：网站可以利用 Cookie 来*记录用户的浏览历史、点击行为*等，以便进行数据分析，为用户提供个性化的推荐内容，或帮助网站优化用户体验。

### 操作方法

在 JavaScript 中，可以使用 `document.cookie` 属性来操作 Cookie。

- **创建 Cookie**

```javascript
document.cookie =
  "username=John Doe; expires=Thu, 18 Dec 2025 12:00:00 UTC; path=/";
```

上述代码创建了一个名为 `username`，值为 `John Doe` 的 Cookie，并且设置了过期时间为 2025 年 12 月 18 日 12 点，`path=/` 表示该 Cookie _在整个网站根目录下都有效_。

- **读取 Cookie**

```javascript
console.log(document.cookie);
```

执行上述代码会将当前页面的所有 Cookie 以字符串形式打印出来，格式为 `name1=value1; name2=value2;...`。通常需要进一步解析这个字符串来获取具体的 Cookie 值。

- **更新 Cookie**
  要更新一个已存在的 Cookie，只需*重新设置它的值*，例如：

```javascript
document.cookie =
  "username=Jane Doe; expires=Thu, 18 Dec 2025 12:00:00 UTC; path=/";
```

这将把 `username` 的值更新为 `Jane Doe`。

- **删除 Cookie**
  通过将 Cookie 的*过期时间设置为过去的时间*，可以达到删除 Cookie 的目的，例如：

```javascript
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/";
```

### 限制和注意事项

- **大小限制**：单个 Cookie 的大小一般限制在 4KB 左右，不同浏览器可能会有所不同。如果存储的数据超过这个限制，_可能会导致数据被截断或无法正确存储_。
- **数量限制**：浏览器对每个域名下的 Cookie 数量也有限制，_通常在 50 个左右_，这意味着不能无限制地创建 Cookie。
- **安全性**：Cookie 中的数据是**以明文形式存储**在用户浏览器中的，因此*不适合存储敏感信息*，如密码、信用卡号等。如果需要存储敏感信息，应该使用加密技术对数据进行加密处理。
- **跨域问题**：Cookie 具有同源策略限制，即一个网站只能访问和操作自己域名下的 Cookie，不能访问其他域名的 Cookie，这是为了保证数据的安全性和隔离性。

### 与其他存储方式的比较

与 HTML5 的 `localStorage` 和 `sessionStorage` 相比，Cookie 的主要特点是会在每次浏览器与服务器通信时*自动发送*到服务器，而 `localStorage` 和 `sessionStorage` 仅在浏览器端存储数据，*不会自动发送*到服务器。`localStorage` 的数据*没有过期时间*，会一直保存在本地，`sessionStorage` 的数据在浏览器*会话结束时（关闭浏览器窗口或标签页）会被清除*，而 Cookie 可以*设置过期时间*，灵活性较高。

## Cookie 的安全性

> 参考 [[_posts/browser/安全/XSS|XSS]] 等

前端 Cookie 的安全性可以通过多种方法来保证，以下是一些常见的措施：

### 合理设置 Cookie 属性

- **设置 HttpOnly 属性**：将 Cookie 标记为 `HttpOnly`，可以*防止通过 JavaScript 脚本访问 Cookie，减少了 XSS*（跨站脚本攻击）获取 Cookie 的风险。例如在设置 Cookie 时，服务器可以通过设置响应头 `Set-Cookie: sessionId=12345; HttpOnly` 来*使该 Cookie 只能通过 HTTP 协议访问*。
- **设置 Secure 属性**：`Secure` 属性确保 Cookie _仅在使用 HTTPS 协议的连接中发送_，这样可以防止在数据传输过程中被中间人窃取或篡改。比如服务器设置 `Set-Cookie: token=abcdef; Secure`，则*只有在 HTTPS 连接下才会发送这个 Cookie*。
- **设置 SameSite 属性**：`SameSite` 属性可以*限制 Cookie 在跨站请求中的发送*，有 `Strict`、`Lax` 和 `None` 三个值：

  - `Strict` 模式下，Cookie 仅在同站请求时发送；
  - `Lax` 模式相对宽松，允许一些安全的跨站请求发送 Cookie；
  - `None` 则允许在任何情况下发送 Cookie，但需要同时设置 `Secure` 属性。

  - 如 `Set-Cookie: userInfo=xxx; SameSite=Strict`。

### 对 Cookie 数据进行加密

- **加密存储敏感信息**：在将敏感信息存储到 Cookie 之前，使用加密算法对数据进行加密处理，如 AES（高级加密标准）等。_服务器端在设置 Cookie 时先加密数据_，例如：

```javascript
const crypto = require("crypto");
const secretKey = "mysecretkey";

function encryptData(data) {
  const cipher = crypto.createCipheriv("aes-256-cbc", secretKey, "ivvector");
  let encrypted = cipher.update(data, "utf8", "hex");
  encrypted += cipher.final("hex");
  return encrypted;
}

const encryptedCookieData = encryptData("sensitive information");
// 设置加密后的Cookie
res.cookie("encryptedData", encryptedCookieData);
```

浏览器端获取到加密后的 Cookie 后，只有在服务器端才能使用相应的密钥进行解密，即使攻击者获取了 Cookie，也难以破解其中的内容。

### 进行输入验证和过滤

- **防止 XSS 攻击**：在将用户输入的数据存储到 Cookie 之前，对数据进行严格的输入验证和过滤，防止恶意脚本注入。可以使用 JavaScript 的 `DOMPurify` 库等工具来对输入进行净化，例如：

```javascript
const DOMPurify = require("dompurify");
const sanitizedData = DOMPurify.sanitize(userInput);
// 将净化后的数据存储到Cookie
document.cookie = `userInput=${sanitizedData}; path=/`;
```

这样可以有效防止攻击者通过构造恶意脚本，利用 Cookie 进行 XSS 攻击。

- **防止 CSRF 攻击**：在 Cookie 中*添加用于 CSRF 防护的令牌（CSRF Token）*，每次用户提交表单等操作时，服务器验证 Cookie 中的令牌与表单中的令牌是否一致，防止跨站请求伪造。一般在服务器端生成令牌并设置到 Cookie 中，如：

```javascript
const csrfToken = generateCsrfToken();
res.cookie("csrfToken", csrfToken);
```

在前端表单中包含该令牌，提交时一起发送到服务器进行验证。

参考 [[CSRF]]

### 定期更新和管理 Cookie

- **设置合理的过期时间**：为 Cookie 设置适当的过期时间，对于一些临时的或敏感的 Cookie，尽量设置较短的过期时间，如用户的登录会话 Cookie，可以设置为 30 分钟或 1 小时等。当用户长时间不操作时，Cookie 自动过期，降低被攻击的风险。
- **定期更新 Cookie 内容**：定期更新 Cookie 中的数据，如用户的登录令牌等。当用户登录后，服务器可以定期生成新的令牌并更新 Cookie 中的值，这样即使攻击者获取了旧的 Cookie，也会因为令牌过期或失效而无法利用。

### 实施安全的开发规范

- **遵循安全的编码规范**：开发人员在编写前端代码时，要遵循安全的编码实践，避免出现常见的安全漏洞，如不直接使用用户输入来构建 Cookie 等。
- **及时更新和修复漏洞**：关注浏览器和相关技术的安全更新，及时修复发现的安全漏洞，保持前端应用的安全性。

## 服务器设置 Cookie

在 Next.js 中设置 Cookie 可以通过以下几种方式，以下是一些常见的方法：

### 1. 使用 `set-cookie` 头（Serverless Functions）

如果你使用的是 Next.js 的 API 路由（位于 `pages/api` 目录下），你可以在服务器端函数中通过设置响应头来添加 Cookie。

```javascript
// pages/api/setCookie.js
export default function handler(req, res) {
  // 设置 Cookie
  res.setHeader(
    "Set-Cookie",
    "username=JohnDoe; Path=/; HttpOnly; SameSite=Strict; Secure"
  );
  res.status(200).json({ message: "Cookie has been set" });
}
```

**解释和使用说明：**

- `res.setHeader('Set-Cookie', '...')`：使用 `setHeader` 方法在响应头中添加 `Set-Cookie` 信息。
- `username=JohnDoe`：设置 Cookie 的名称和值。
- `Path=/`：指定 Cookie 的有效路径，`/` 表示在整个网站都有效。
- `HttpOnly`：确保该 Cookie 只能通过 HTTP 协议访问，防止通过 JavaScript 访问，增强安全性，防止 XSS 攻击。
- `SameSite=Strict`：限制 Cookie 仅在同站请求中发送，防止 CSRF 攻击。
- `Secure`：确保 Cookie 仅在使用 HTTPS 时发送，提高传输安全性。

### 2. 使用第三方库 `cookie` 或 `cookie-parser`

为了更方便地操作 Cookie，你可以使用第三方库，例如 `cookie` 或 `cookie-parser`。首先，你需要安装相应的库：

```bash
npm install cookie
```

然后在你的 API 路由中使用：

```javascript
// pages/api/setCookie.js
import { serialize } from "cookie";

export default function handler(req, res) {
  const cookie = serialize("username", "JohnDoe", {
    path: "/",
    httpOnly: true,
    sameSite: "Strict",
    secure: true,
    maxAge: 60 * 60 * 24, // 24 小时
  });

  res.setHeader("Set-Cookie", cookie);
  res.status(200).json({ message: "Cookie has been set" });
}
```

**解释和使用说明：**

- `import { serialize } from 'cookie'`：从 `cookie` 库导入 `serialize` 函数，它可以帮助你更方便地创建一个符合规范的 Cookie 字符串。
- `serialize('username', 'JohnDoe', {...})`：使用 `serialize` 函数设置 Cookie 的各种属性，包括名称、值和其他属性。
- `path`：指定 Cookie 的有效路径。
- `httpOnly`：将 Cookie 标记为仅 HTTP 访问。
- `sameSite`：设置 `SameSite` 属性，防止 CSRF 攻击。
- `secure`：确保在 HTTPS 下使用。
- `maxAge`：设置 Cookie 的最大生存时间，以秒为单位，这里设置为 24 小时。

### 3. 使用 `iron-session` 库（用于会话管理）

如果你需要更高级的会话管理功能，可以使用 `iron-session` 库。首先，安装它：

```bash
npm install iron-session
```

然后创建一个 API 路由：

```javascript
// pages/api/setSession.js
import { withIronSession } from "iron-session/next";

const handler = async (req, res) => {
  req.session.user = { name: "JohnDoe" };
  await req.session.save();
  res.status(200).json({ message: "Session has been set" });
};

export default withIronSession(handler, {
  cookieName: "mySession",
  password: "complex_password_at_least_32_characters_long",
  // 配置 Cookie 属性
  cookieOptions: {
    secure: process.env.NODE_ENV === "production",
    httpOnly: true,
    sameSite: "strict",
    maxAge: 60 * 60 * 24 * 30, // 30 天
  },
});
```

**解释和使用说明：**

- `import { withIronSession } from 'iron-session/next'`：导入 `withIronSession` 帮助函数。
- `req.session.user = {...}`：设置会话数据。
- `req.session.save()`：保存会话数据并更新 Cookie。
- `withIronSession(handler, {...})`：使用 `withIronSession` 包装处理函数，并设置 `cookieOptions`。
- `cookieName`：会话 Cookie 的名称。
- `password`：用于加密会话数据的密码，必须至少 32 个字符。
- `cookieOptions`：配置 Cookie 的各种属性，如 `secure`、`httpOnly`、`sameSite` 和 `maxAge`。

### 注意事项：

- **安全性**：确保在生产环境中使用 `Secure` 和 `HttpOnly` 属性，以防止安全问题。
- **SameSite 属性**：根据需要选择 `Strict`、`Lax` 或 `None`（需要 `Secure`），以控制跨站请求时 Cookie 的行为。
- **Path 属性**：根据需要设置 Cookie 的有效路径，确保 Cookie 在正确的页面和路由上生效。

以上是在 Next.js 中设置 Cookie 的几种常见方法，你可以根据具体的需求和项目的复杂性选择合适的方法。在处理 Cookie 时，始终要注意遵循最佳安全实践，防止潜在的安全漏洞，如 XSS 和 CSRF 攻击。
