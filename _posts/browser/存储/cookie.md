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

前端 Cookie 的安全性可以通过多种方法来保证，以下是一些常见的措施：

### 合理设置 Cookie 属性

- **设置 HttpOnly 属性**：将 Cookie 标记为 `HttpOnly`，可以*防止通过 JavaScript 脚本访问 Cookie，减少了 XSS*（跨站脚本攻击）获取 Cookie 的风险。例如在设置 Cookie 时，服务器可以通过设置响应头 `Set-Cookie: sessionId=12345; HttpOnly` 来*使该 Cookie 只能通过 HTTP 协议访问*。
- **设置 Secure 属性**：`Secure` 属性确保 Cookie *仅在使用 HTTPS 协议的连接中发送*，这样可以防止在数据传输过程中被中间人窃取或篡改。比如服务器设置 `Set-Cookie: token=abcdef; Secure`，则*只有在 HTTPS 连接下才会发送这个 Cookie*。
- **设置 SameSite 属性**：`SameSite` 属性可以*限制 Cookie 在跨站请求中的发送*，有 `Strict`、`Lax` 和 `None` 三个值：
	- `Strict` 模式下，Cookie 仅在同站请求时发送；
	- `Lax` 模式相对宽松，允许一些安全的跨站请求发送 Cookie；
	- `None` 则允许在任何情况下发送 Cookie，但需要同时设置 `Secure` 属性。如 `Set-Cookie: userInfo=xxx; SameSite=Strict`。

### 对 Cookie 数据进行加密

- **加密存储敏感信息**：在将敏感信息存储到 Cookie 之前，使用加密算法对数据进行加密处理，如 AES（高级加密标准）等。服务器端在设置 Cookie 时先加密数据，例如：

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

- **防止 CSRF 攻击**：在 Cookie 中添加用于 CSRF 防护的令牌（CSRF Token），每次用户提交表单等操作时，服务器验证 Cookie 中的令牌与表单中的令牌是否一致，防止跨站请求伪造。一般在服务器端生成令牌并设置到 Cookie 中，如：

```javascript
const csrfToken = generateCsrfToken();
res.cookie("csrfToken", csrfToken);
```

在前端表单中包含该令牌，提交时一起发送到服务器进行验证。

### 定期更新和管理 Cookie

- **设置合理的过期时间**：为 Cookie 设置适当的过期时间，对于一些临时的或敏感的 Cookie，尽量设置较短的过期时间，如用户的登录会话 Cookie，可以设置为 30 分钟或 1 小时等。当用户长时间不操作时，Cookie 自动过期，降低被攻击的风险。
- **定期更新 Cookie 内容**：定期更新 Cookie 中的数据，如用户的登录令牌等。当用户登录后，服务器可以定期生成新的令牌并更新 Cookie 中的值，这样即使攻击者获取了旧的 Cookie，也会因为令牌过期或失效而无法利用。

### 实施安全的开发规范

- **遵循安全的编码规范**：开发人员在编写前端代码时，要遵循安全的编码实践，避免出现常见的安全漏洞，如不直接使用用户输入来构建 Cookie 等。
- **及时更新和修复漏洞**：关注浏览器和相关技术的安全更新，及时修复发现的安全漏洞，保持前端应用的安全性。
