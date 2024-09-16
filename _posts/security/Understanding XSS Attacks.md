# Understanding XSS Attacks  
了解 XSS 攻击

Cross-Site Scripting (XSS) is an attack wherein attackers inject malicious scripts into webpages viewed by other users. These scripts can bypass the same-origin policy, allowing the attacker to **steal sensitive data**, **hijack user sessions**, **deface websites**, or **spread malware**.  
跨站脚本 (XSS) 是一种攻击，*攻击者将恶意脚本注入其他用户查看的网页中*。*这些脚本可以绕过同源策略*，允许攻击者**窃取敏感数据**、**劫持用户会话**、**破坏网站**或**传播恶意软件**。

Imagine you're on a blog reading the comment section. Little did you know that this comment contained a hidden script that, when executed, stole your session cookie. Oops, you're now a victim of an XSS attack. But how does this happen?  
想象一下您正在博客上阅读评论部分。您几乎不知道*此评论包含一个隐藏脚本*，该脚本在执行时会*窃取您的会话 cookie*。糟糕，您现在是 XSS 攻击的受害者。但这是怎么发生的呢？

### [How XSS Attacks Occur](https://vercel.com/guides/understanding-xss-attacks#how-xss-attacks-occur-)

XSS attacks happen in several stages:  
XSS 攻击分几个阶段发生：

**1. The attacker identifies a target site that is vulnerable to XSS.** A website is vulnerable if it blindly trusts user input and doesn't sanitize it before rendering.  
**1. 攻击者识别出易受 XSS 攻击的目标站点。** **如果网站盲目信任用户输入并且在渲染之前不对其进行清理，则该网站很容易受到攻击**。

![Group 513650 (4).png](https://vercel.com/_next/image?url=https%3A%2F%2Fimages.ctfassets.net%2Fe5382hct74si%2F4BuOVI5ytH0DYwEYNpLfJY%2Fc16f75dd41923fef0720968cdbe771c4%2FGroup_513650__4_.png&w=3840&q=75)

**2. The attacker crafts a malicious script.** This could be something like a malicious script or a broken image that captures and sends the user's session cookie to the attacker's server.  
**2. 攻击者制作恶意脚本。** 这可能是恶意脚本或损坏的图像，它捕获用户的会话 cookie 并将其发送到攻击者的服务器。

**3. The attacker embeds this script in a vulnerable site.** They could post the script as a comment, a profile status, or any other user-generated content that will be displayed to other users. In this case, the attacker embeds an erroring image that invokes its `**onerror**` attribute, which sends a request containing the user's cookie to the attacker's server.  
**3. 攻击者将此脚本嵌入到易受攻击的站点中。** 他们可以将脚本发布为评论、个人资料状态或任何其他用户生成的将显示给其他用户的内容。在这种情况下，攻击者嵌入一个调用其`**onerror**`属性的错误图像，该属性将包含用户 cookie 的请求发送到攻击者的服务器。

![Group 513651 (1).png](https://vercel.com/_next/image?url=https%3A%2F%2Fimages.ctfassets.net%2Fe5382hct74si%2F21luGzwbfU1OuqTGJpkcPH%2Fb8f282c5e47974d2079f59f6c0b2501a%2FGroup_513651__1_.png&w=3840&q=75)

**4. The script runs within the user's browser in the context of the vulnerable site.** Without adequate security measures, this malicious script can potentially access any cookies, session tokens, or other sensitive information retained by the browser for that site and send them to the attacker.  
**4. 该脚本在易受攻击的站点上下文中的用户浏览器中运行。** 如果没有足够的安全措施，此恶意脚本可能会访问浏览器为该站点保留的任何 cookie、会话令牌或其他敏感信息，并将其发送给攻击者。

![Group 513652 (1).png](https://vercel.com/_next/image?url=https%3A%2F%2Fimages.ctfassets.net%2Fe5382hct74si%2F1R9mFVI4mes1tnjHqFGVu9%2F9642675b18757947829bd0768cfcd6ce%2FGroup_513652__1_.png&w=3840&q=75)

---

## [Types of XSS Attacks XSS 攻击的类型](https://vercel.com/guides/understanding-xss-attacks#types-of-xss-attacks)

There are three types of XSS attacks: DOM-based, reflected, and persistent.  
XSS 攻击分为三种类型：基于 DOM 的攻击、反射攻击和持久攻击。

### [1. DOM-based XSS  1.基于DOM的XSS](https://vercel.com/guides/understanding-xss-attacks#1.-dom-based-xss-)

This type of XSS attack occurs when the malicious payload is executed due to modifying the DOM “on the fly” with client-side JavaScript.  
当由于使用客户端 JavaScript“动态”修改 DOM 而执行恶意负载时，就会发生这种类型的 XSS 攻击。

DOM-based XSS is distinct in that it operates entirely client-side, without sending an HTTP request to a server. The attack unfolds within the victim's browser by manipulating the DOM. The client-side execution makes these attacks harder to detect, as traditional security measures often focus on server traffic. For attackers, this requires a nuanced understanding of JavaScript and the web application's structure to exploit successfully, making it a subtler, yet challenging attack vector.  
基于 DOM 的 XSS 的独特之处在于它完全在客户端运行，而不向服务器发送 HTTP 请求。攻击通过操纵 DOM 在受害者的浏览器中展开。客户端执行使这些攻击更难以检测，因为传统的安全措施通常关注服务器流量。对于攻击者来说，这需要对 JavaScript 和 Web 应用程序的结构有细致的了解才能成功利用，从而使其成为更微妙但更具挑战性的攻击媒介。

Imagine a simple web page that reads a URL parameter and updates the content of a page using JavaScript:  
想象一个简单的网页，它读取 URL 参数并使用 JavaScript 更新页面内容：

```
<div id="content">Default content</div>    <script>           const params = new URLSearchParams(window.location.search);   document.getElementById('content').innerHTML = params.get('message');</script>
```

If a user visits a link like **`http://example.com/index.html?message=<script>alert('Hacked!')</script>`**, the malicious script would run.  
如果用户访问类似的链接，恶意脚本就会运行。

### [2. Reflected XSS  2.反射型XSS](https://vercel.com/guides/understanding-xss-attacks#2.-reflected-xss)

This attack occurs when a malicious script provided by an attacker is reflected off a web server, such as through a search result or error message, and executed immediately without being stored.  
当攻击者提供的恶意脚本从 Web 服务器反射（例如通过搜索结果或错误消息）并立即执行而不存储时，就会发生这种攻击。

Reflected XSS typically requires some form of **social engineering**, as the victim must be tricked into clicking a specially crafted link that contains the malicious payload. Once the link is clicked, the payload is sent to the vulnerable website which then reflects the attack back to the user's browser, where the malicious script is executed. Due to its non-persistent nature, the attack happens in real time and doesn't affect other users of the site.  
反射型 XSS 通常需要某种形式的**社会工程**，因为必须诱骗受害者点击包含恶意负载的特制链接。单击链接后，有效负载将被发送到易受攻击的网站，然后该网站将攻击反射回用户的浏览器，并在其中执行恶意脚本。由于其非持久性，攻击是实时发生的，不会影响网站的其他用户。

Imagine a simple search feature on a website that displays the search query back to the user. If the website fails to properly sanitize the **`query`** parameter before reflecting its content back on the page, the script will execute, resulting in an XSS attack.  
想象一下网站上有一个简单的搜索功能，可以向用户显示搜索查询。如果网站在将其内容反射回页面之前未能正确清理**`query`**参数，则脚本将执行，从而导致 XSS 攻击。

```
<div>          <!-- Vulnerable Code: Reflects the user input directly without sanitization -->          You searched for: <span id="search-result"></span>    </div>    
<script>         // Get the query parameter from the URL          const params = new URLSearchParams(window.location.search);  const query = params.get('query');
   // Reflect the query directly into the page (vulnerable!)          document.getElementById('search-result').innerHTML = query;</script>
```

If a user inputs a script instead of an ID, it'll execute on the browser, leading to a Reflected XSS attack.  
如果用户输入脚本而不是 ID，它将在浏览器上执行，从而导致反射型 XSS 攻击。

### [3. Stored XSS  3.存储型XSS](https://vercel.com/guides/understanding-xss-attacks#3.-stored-xss-)

This is the most dangerous type of XSS attack. Here, the malicious script is injected directly into a website's storage, often through forms or similar input mechanisms. When other users visit this website, they unknowingly load and execute the malicious script from the site's database, making it persistent across sessions.  
这是最危险的 XSS 攻击类型。在这里，恶意脚本通常通过表单或类似的输入机制直接注入网站的存储中。当其他用户访问该网站时，他们会在不知不觉中加载并执行该网站数据库中的恶意脚本，使其在会话中持续存在。

Consider a comment section on a blog:  
考虑博客上的评论部分：

```
<textarea id="comment"></textarea><button onclick="postComment()">Post Comment</button><div id="commentsSection"></div><script>     function postComment() {      const comment = document.getElementById('comment').value;      // Imagine this gets stored in a database without validation.              saveCommentToDB(comment)      document.getElementById('commentsSection').innerHTML += comment;   }
   function loadComments() {     // Loads comments from db without validation     const comments = getCommentsFromDB();     document.getElementById('commentsSection').innerHTML = comments; }</script>
```

A user can post a script as a comment. When another user visits the page, that script, which is stored in the database, gets executed.  
用户可以将脚本作为评论发布。当另一个用户访问该页面时，存储在数据库中的该脚本将被执行。

---

XSS attacks can have devastating effects on your users and your product.  
XSS 攻击可能会对您的用户和您的产品产生毁灭性影响。

1. **They operate covertly, hiding within legitimate websites**. This is one of the main reasons XSS attacks are so effective and perilous. Users, believing they're interacting with a trusted website, may inadvertently trigger a malicious script, thinking it's part of the website's regular operations.  
    **他们隐藏在合法网站内秘密运作**。这是 XSS 攻击如此有效和危险的主要原因之一。用户认为自己正在与受信任的网站进行交互，可能会无意中触发恶意脚本，并认为这是该网站常规操作的一部分。
2. **Anyone who views a page or web application infected with an XSS payload is at risk**. This makes such attacks incredibly scalable for cybercriminals. For instance, if an attacker injects a malicious script into a popular website's comment section, any user who views that comment could become a victim. This scalability underscores the importance of robust cybersecurity measures for popular websites that attract many visitors.  
    **任何查看受 XSS 负载感染的页面或 Web 应用程序的人都会面临风险**。这使得此类攻击对于网络犯罪分子来说具有难以置信的可扩展性。例如，如果攻击者将恶意脚本注入热门网站的评论部分，则任何查看该评论的用户都可能成为受害者。这种可扩展性强调了对于吸引大量访问者的热门网站而言，强大的网络安全措施的重要性。
3. **The** **consequences of an XSS attack are multifaceted and can have significant implications**. This includes **data theft** through cookies, session tokens, or other sensitive information, **malware distribution** by forcing a user's browser to download and run malicious software, and **phishing** by tricking users into providing personal data or login credentials.  
    **XSS 攻击的后果是多方面的，并且可能产生重大影响****。**这包括通过 cookie、会话令牌或其他敏感信息**窃取数据**，通过强制用户浏览器下载并运行恶意软件来分发**恶意**软件，以及通过诱骗用户提供个人数据或登录凭据来进行网络**钓鱼**。
4. XSS attacks can **undermine users' trust in your product**.  
    XSS 攻击会**破坏用户对您产品的信任**。

---

## [**Preventing XSS Attacks 防止 XSS 攻击**](https://vercel.com/guides/understanding-xss-attacks#preventing-xss-attacks)

Protection against XSS requires diligence, but there are some effective methods to mitigate them.  
防范 XSS 需要勤奋，但有一些有效的方法可以减轻它们。

### [**Reject unexpected inputs 拒绝意外的输入**](https://vercel.com/guides/understanding-xss-attacks#reject-unexpected-inputs)

Inputs should match expected formats; e.g. an age field should only contain numbers.  
输入应符合预期格式；例如，年龄字段应该只包含数字。

app/api/route.js 应用程序/api/route.js

```
export function POST(req)  {    const { age }  = req.body;
    if (!Number.isInteger(Number(age))) {      res.status(400).send('Invalid age input. Please enter a valid number.');      return;    }
    res.status(200).send(`Your age is: ${age}`);  } else {    res.status(405).end(); // Method Not Allowed    }};
```

### [**Validate and sanitize user inputs  验证和清理用户输入**](https://vercel.com/guides/understanding-xss-attacks#validate-and-sanitize-user-inputs-)

Use libraries such as [DOMPurify](https://github.com/cure53/DOMPurify) to validate and sanitize user inputs, ensuring malicious scripts are cleaned before rendering.  
使用[DOMPurify](https://github.com/cure53/DOMPurify)等库来验证和清理用户输入，确保在渲染之前清除恶意脚本。

app/api/route.js 应用程序/api/route.js

```
import { sanitize } from "isomorphic-dompurify";
export function POST(req) {  const { comment } = req.body;  const sanitizedComment = sanitize(comment);  ...}
```

### [**Avoid inline scripts and inline event handlers  避免内联脚本和内联事件处理程序**](https://vercel.com/guides/understanding-xss-attacks#avoid-inline-scripts-and-inline-event-handlers)

Instead, use external scripts and bind events dynamically. This reduces the chances of accidentally executing attacker-controlled scripts.  
相反，使用外部脚本并动态绑定事件。这减少了意外执行攻击者控制的脚本的机会。

```
<!-- Inline Event (Not Recommended) --><button onclick="alert('Button was clicked!')">Click me!</button>
----
<!-- External Event (Recommended) --><button id="myButton">Click me!</button>
<script>    document.getElementById("myButton").addEventListener("click", function() {   alert("Button was clicked!");});</script>
```

### [Set a **Content Security Policy (CSP)**  
设置**内容安全策略 (CSP)**](https://vercel.com/guides/understanding-xss-attacks#set-a-content-security-policy-csp)

[CSP](https://vercel.com/guides/content-security-policy) is a browser feature that helps reduce the risk of XSS attacks. It allows websites to specify which sources are allowed, blocking any malicious or unexpected content.  
[CSP](https://vercel.com/guides/content-security-policy)是一项浏览器功能，有助于降低 XSS 攻击的风险。它允许网站指定允许哪些来源，阻止任何恶意或意外内容。

```
<!-- This CSP allows only scripts loaded from the current site's origin --><meta http-equiv="Content-Security-Policy" content="default-src 'self';">
```

In Next.js, you can [use middleware to configure the CSP header](https://nextjs.org/docs/app/building-your-application/configuring/content-security-policy).  
在Next.js中，您可以[使用中间件来配置CSP标头](https://nextjs.org/docs/app/building-your-application/configuring/content-security-policy)。

### [(React) **Avoid using** **`dangerouslySetInnerHTML`**  (React)**避免使用****`dangerouslySetInnerHTML`**](https://vercel.com/guides/understanding-xss-attacks#react-avoid-using-dangerouslysetinnerhtml)

This attribute allows for directly inserting HTML content into the React application. If the content contains malicious scripts and is not appropriately sanitized, those scripts could be executed in the user's browser. If you must use it, sanitize the content first! React’s JSX escapes all variables by default. Leverage this feature and avoid manually creating DOM content.  
该属性允许直接将 HTML 内容插入到 React 应用程序中。如果内容包含恶意脚本且未经过适当清理，这些脚本可能会在用户的浏览器中执行。如果必须使用它，请先消毒内容物！ React 的 JSX 默认转义所有变量。利用此功能并避免手动创建 DOM 内容。

```
import React from 'react';
// Imagine this comment is fetched from a user or an external source  const userComment = '<img src=x onerror=alert("XSS Attack")>';
export function BadComment() {  // ❌ No sanitization on dangerouslySetInnerHTML  return <div dangerouslySetInnerHTML={{ __html: userComment }}></div>}
export function BetterComment() {   const  sanitizedComment = DOMPurify.sanitize(userComment);
  // ⚠️ Still using dangerouslySetInnerHTML, but sanitized  return <div dangerouslySetInnerHTML={{ __html: sanitizedComment }}></div>}
export function BestComment() {  // ✅ React automatically escapes content within curly brackets  return <div>{userComment}</div>}
```

### [**Use the** **`HttpOnly`** **and** **`Secure`** **attributes on cookies**  **使用****cookie 的****`HttpOnly`****和****`Secure`**属性](https://vercel.com/guides/understanding-xss-attacks#use-the-httponly-and-secure-attributes-on-cookies)

By setting the **`HttpOnly`** flag on a cookie, you prevent it from being accessed by JavaScript. The **`Secure`** flag ensures that the cookie can only be transmitted over secure HTTPS connections. [Learn more about cookies here](https://vercel.com/guides/understanding-cookies).  
通过在 cookie 上设置**`HttpOnly`**标志，可以防止 JavaScript 访问它。 **`Secure`**标志确保 cookie 只能通过安全 HTTPS 连接传输。[在此处了解有关 cookie 的更多信息](https://vercel.com/guides/understanding-cookies)。

### [Choose server-side input validation over client-side  选择服务器端输入验证而不是客户端](https://vercel.com/guides/understanding-xss-attacks#choose-server-side-input-validation-over-client-side-)

Client-side validation is primarily for user experience. Although it offers quick feedback, it's easily manipulated. If the server blindly trusts this input, malicious actors can circumvent client-side checks, resulting in XSS vulnerabilities.  
客户端验证主要是为了用户体验。虽然它提供快速反馈，但很容易被操纵。如果服务器盲目信任此输入，恶意行为者就可以绕过客户端检查，从而导致 XSS 漏洞。

Server-side validation is the last line of defense against XSS. You can prevent the execution of undesirable scripts even if malicious input gets past client-side defenses by validating and sanitizing inputs on the server.  
服务器端验证是防御 XSS 的最后一道防线。即使恶意输入通过了客户端防御，您也可以通过验证和清理服务器上的输入来防止执行不需要的脚本。

Before being used or displayed, all data must be validated and sanitized on the server. To reduce security gaps, always ensure validation rules and techniques are consistent on both client and server.  
在使用或显示之前，所有数据都必须在服务器上进行验证和清理。为了减少安全漏洞，请始终确保验证规则和技术在客户端和服务器上保持一致。

### [**Be cautious with third-party libraries  谨慎使用第三方库**](https://vercel.com/guides/understanding-xss-attacks#be-cautious-with-third-party-libraries)

Ensure any third-party React components or Next.js plugins you include do not introduce XSS vulnerabilities.  
确保您包含的任何第三方 React 组件或 Next.js 插件不会引入 XSS 漏洞。

---

## [**Conclusion 结论**](https://vercel.com/guides/understanding-xss-attacks#conclusion)

XSS attacks can cause significant damage, but they can be completely avoided with the appropriate preventive measures. Developers should make security a top priority and continuously update their knowledge to stay informed about emerging threats and protection mechanisms. Ensuring the security of a web application is an ongoing process, and remaining vigilant against threats like XSS is crucial.  
XSS 攻击可能会造成重大损害，但通过适当的预防措施可以完全避免。开发人员应将安全放在首位，并不断更新知识，以随时了解新出现的威胁和保护机制。确保 Web 应用程序的安全是一个持续的过程，对 XSS 等威胁保持警惕至关重要。

---

### [Further Reading 进一步阅读](https://vercel.com/guides/understanding-xss-attacks#further-reading)

- [https://owasp.org/www-community/attacks/xss/](https://owasp.org/www-community/attacks/xss/)
- [https://portswigger.net/web-security/cross-site-scripting](https://portswigger.net/web-security/cross-site-scripting)