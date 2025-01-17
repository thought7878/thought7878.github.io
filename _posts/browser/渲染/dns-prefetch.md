## 概念解释

`dns-prefetch` 是一种*资源提示（Resource Hint）*，它的主要目的是**告诉浏览器预先解析特定 URL 的 DNS**。DNS 解析是将域名转换为 IP 地址的过程，这个过程可能会引入延迟，尤其是对于来自不同域的资源。通过使用 `dns-prefetch`，*提前完成 DNS 解析，为后续的资源请求节省时间*。

![[_posts/base/js/Advanced Web Development Quiz/media/aac299094c6d2655007dfdc83402fa9d_MD5.jpeg]]

![[_posts/base/js/Advanced Web Development Quiz/media/bf964f9cbda55ef062104ea4c1b4560d_MD5.jpeg]]


## 使用方法

   - 在 HTML 的 `<head>` 部分使用 `<link>` 元素并设置 `rel` 属性为 `dns-prefetch`。例如：
   ```html
   <head>
     <link rel="dns-prefetch" href="https://example.com" />
   </head>
   ```
   - 这里的 `href` 是要提前进行 DNS 解析的 URL。

## 工作原理

   - 当浏览器遇到 `dns-prefetch` 元素时，它会在后台启动一个 DNS 解析过程，将 `href` 中的域名解析为 IP 地址。这个过程不会阻塞页面的渲染，所以不会影响用户看到页面的速度。一旦后续请求需要从该域名获取资源，由于 DNS 已经解析完成，请求可以更快地进行，因为不需要等待 DNS 解析这一步骤。

## 使用场景

   - **第三方资源**：对于使用第三方服务或库的情况，如使用 CDN 托管的资源、外部 API 等，提前进行 DNS 解析可以提高性能。例如：
   ```html
   <head>
     <link rel="dns-prefetch" href="https://cdn.jsdelivr.net" />
     <link rel="dns-prefetch" href="https://api.github.com" />
   </head>
   ```
   - 这里分别为一个 CDN 服务和 GitHub 的 API 服务提前进行 DNS 解析，为后续可能的资源请求做好准备。
   - **跨域资源**：当你知道要从其他域请求资源时，使用 `dns-prefetch` 可以减少请求延迟。例如，在一个网站中使用来自不同域的图片、脚本或样式表：
   ```html
   <head>
     <link rel="dns-prefetch" href="https://external-image-service.com" />
     <link rel="dns-prefetch" href="https://external-script-service.com" />
   </head>
   ```

## 与其他资源提示的区别和联系

   - **与 `preconnect` 的区别**：
     - `dns-prefetch` 仅进行 DNS 解析，而 `preconnect` 不仅进行 DNS 解析，还会建立 TCP 连接和进行 TLS 协商（对于 HTTPS 资源）。所以 `preconnect` 是一个更全面的提前准备步骤，但可能会消耗更多资源，因为涉及更多的网络操作。
     - 如果只需要提前完成 DNS 解析，使用 `dns-prefetch` 更合适；如果需要更完整的连接准备，包括 TCP 和 TLS，使用 `preconnect`。例如：
     ```html
     <head>
       <link rel="dns-prefetch" href="https://example.com" />
       <link rel="preconnect" href="https://example.com" />
     </head>
     ```
     - 上述代码中，第一个元素只进行 DNS 解析，第二个元素会做更多的连接准备工作。
   - **与 `preload` 和 `prefetch` 的区别**：
     - `preload` 用于预加载当前页面后续肯定会用到的资源，而 `prefetch` 用于预取可能会在未来某个时间使用的资源。它们主要是关于资源的加载，而 `dns-prefetch` 只是针对 DNS 解析的优化。
     - 例如：
     ```html
     <head>
       <link rel="dns-prefetch" href="https://example.com" />
       <link rel="preload" href="https://example.com/script.js" as="script" />
       <link rel="prefetch" href="https://example.com/style.css" />
     </head>
     ```
     - 这里 `dns-prefetch` 提前解析域名，`preload` 预加载脚本，`prefetch` 预取样式表，各自发挥不同的性能优化作用。

## 性能优化效果

   - 使用 `dns-prefetch` 可以减少首次请求资源时的延迟，尤其是对于首次访问的页面或加载大量跨域资源的页面，提前完成 DNS 解析可以使资源请求更快，提高页面加载速度和用户体验。

总的来说，`dns-prefetch` 是一个简单但有效的前端性能优化工具，通过提前完成 DNS 解析，它可以在一定程度上提高页面的响应速度，对于优化跨域资源的加载性能特别有用。
