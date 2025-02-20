## 概念和作用

`preconnect`是一种*资源提示*（Resource Hint），**用于提前建立与源服务器的连接，以加速后续资源的加载**。通过提前建立连接，可以减少后续资源请求时的延迟，*特别是对于跨域资源*，能显著提高性能。例如，当你知道需要*从某个特定的第三方服务器加载资源时*，使用`preconnect`可以让浏览器提前建立连接，为资源的快速获取做好准备。

![[_posts/base/js/Advanced Web Development Quiz/media/ee28aac9ca5c1097c7d1112a5a8f8cdf_MD5.jpeg]]

![[_posts/base/js/Advanced Web Development Quiz/media/e20a5ca2c0a38643d2716515a2a5f547_MD5.jpeg]]

## 使用方法

   - 在 HTML 的 `<head>` 部分使用 `<link>` 元素并设置 `rel` 属性为 `preconnect`。例如，如果你要提前连接到一个 CDN 服务器：
   ```html
   <head>
     <link rel="preconnect" href="https://cdn.example.com" />
   </head>
   ```
   - 这里的 `href` 指向要提前连接的服务器地址。

## 工作原理

   - 当浏览器解析到 `preconnect` 标签时，它会执行以下操作：
     - 执行 DNS 查找：解析 `href` 中的域名，将其转换为 IP 地址。
     - 建立 TCP 连接：完成三次握手，建立与服务器的 TCP 连接。
     - 对于 HTTPS 资源，还会进行 TLS 协商，以确保安全连接。
   - 这些操作都是在后台进行的，不会阻塞页面的渲染，一旦后续需要从该服务器请求资源，连接已经建立，可直接发送请求，节省了时间。

## 应用场景

   - **第三方资源**：当使用第三方库或服务，如字体库、CDN 存储的脚本或样式表时，使用 `preconnect` 可提高性能。例如：
   ```html
   <head>
     <link rel="preconnect" href="https://fonts.googleapis.com" />
     <link rel="preconnect" href="https://ajax.googleapis.com" />
   </head>
   ```
这里分别提前连接了 Google 的字体服务和 AJAX 服务的服务器，为后续从这些服务器加载资源做好准备。

   - **跨域资源**：对于跨域的 API 或其他资源，提前连接可以加快请求响应。例如：
   ```html
   <head>
     <link rel="preconnect" href="https://api.thirdparty.com" />
   </head>
   ```

## 与其他资源提示的配合使用

   - **`dns-prefetch`**：`dns-prefetch` *仅进行 DNS 查找*，而 `preconnect` *除了 DNS 查找外，还会建立 TCP 连接和进行 TLS 协商*（如果是 HTTPS）。如果只需要进行 DNS 查找，可以使用 `dns-prefetch`。但如果要更全面的提前准备，`preconnect` 更合适。例如：
   ```html
   <head>
     <link rel="dns-prefetch" href="https://cdn.example.com" />
   </head>
   ```

   - **`prefetch` 和 `preload`**：`prefetch` 是预取资源，用于未来可能需要的资源；`preload` 是预加载资源，用于当前页面后续需要的资源。`preconnect` 主要是提前建立连接，为后续的资源加载做准备，可以**与 `prefetch` 和 `preload` 结合使用，形成完整的性能优化策略：**
   ```html
   <head>
     <link rel="preconnect" href="https://cdn.example.com" />
     <link rel="preload" href="https://cdn.example.com/script.js" as="script" />
     <link rel="prefetch" href="https://cdn.example.com/style.css" />
   </head>
   ```
   - 这里先使用 `preconnect` 建立连接，然后使用 `preload` 预加载脚本，使用 `prefetch` 预取样式表，不同的资源提示发挥不同的性能优化作用。

## 性能优化效果

   - 使用 `preconnect` 可以显著减少首次请求的延迟，特别是对于关键的第三方资源或需要频繁请求的跨域资源，通过提前建立连接，能让资源更快到达客户端，从而提高页面的加载速度和用户体验。

总之，`preconnect` 是前端性能优化中的一个重要工具，通过提前建立连接，减少后续资源请求时的延迟，帮助提高页面的整体性能和响应速度。合理使用它可以为用户提供更流畅、快速的网页体验。
