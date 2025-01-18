## 概念解释

   - `stale-while-revalidate` 是一种 HTTP 缓存策略指令，*它允许使用过期的缓存，同时在后台重新验证该缓存的有效性*。这意味着当缓存过期时，不会立即阻塞请求并等待新的响应，而是*先使用过期的缓存来提供服务，同时在后台向服务器发送请求以更新缓存*。*这种策略可以在保证一定性能的同时，使客户端尽快获得内容，又能保证内容的相对新鲜度*。

![[_posts/browser/缓存/缓存指令/media/986bfbcea9ada9be343c3b7c9e555be1_MD5.jpeg]]

## 使用场景

   - **高流量页面**：对于高流量的页面或资源，使用 `stale-while-revalidate` 可以在不影响用户体验的情况下更新缓存。例如，热门文章的页面，即使缓存已过期，用户仍然可以快速看到内容，同时在后台更新缓存，避免了高流量下大量用户同时等待新资源的情况。
   - **非关键信息**：对于一些不那么关键的信息，如博客文章的评论部分，即使稍显陈旧也不会对用户体验造成太大影响，但仍需要更新，`stale-while-revalidate` 可以让用户快速看到内容，同时更新缓存。

## 设置方法

   - **HTTP 响应头设置**：在服务器的响应头中设置 `Cache-Control` 包含 `stale-while-revalidate` 指令，并通常与 `max-age` 结合使用。以下是在 Node.js 的 Express 框架中的示例：

   ```javascript
   const express = require("express");
   const app = express();

   app.get("/article", (req, res) => {
     res.set("Cache-Control", "max-age=60, stale-while-revalidate=300");
     res.send("这是一篇文章的内容");
   });

   app.listen(3000, () => {
     console.log("服务器运行在 http://localhost:3000");
   });
   ```

   - 在这个例子中，服务器为 `/article` 路径设置了 `Cache-Control` 为 `max-age=60, stale-while-revalidate=300`，表示该资源的缓存有效期为 60 秒，当缓存过期后，在接下来的 300 秒内可以使用过期的缓存，同时在后台重新验证缓存。

## 工作原理

   - 当客户端首次请求资源时，服务器会发送包含 `stale-while-revalidate` 的 `Cache-Control` 指令。
   - 客户端会根据 `max-age` 存储和使用缓存。
   - 当缓存过期时：
     - 客户端会立即使用过期的缓存来提供服务，同时在后台向服务器发送请求。
     - 服务器收到请求后，会验证缓存是否有效：
       - 如果有效，会更新客户端的缓存。
       - 如果无效，会发送新的资源给客户端，更新缓存。

## 与其他缓存指令的比较

   - **与 `must-revalidate` 的区别**：
     - `must-revalidate` 要求缓存过期后必须重新验证才能使用，而 `stale-while-revalidate` 允许在缓存过期后先使用过期的缓存，再重新验证。例如：
     ```javascript
     res.set("Cache-Control", "max-age=60, must-revalidate");
     ```
     - `must-revalidate` 更注重缓存过期后的严格验证，而 `stale-while-revalidate` 更注重性能和用户体验，允许在一定时间内使用过期缓存。
   - **与 `no-cache` 的区别**：
     - `no-cache` 要求在使用缓存前验证，而 `stale-while-revalidate` 允许使用过期缓存同时在后台验证。例如：
     ```javascript
     res.set("Cache-Control", "no-cache");
     ```
     - `no-cache` 会在每次使用缓存前进行验证，可能导致等待，而 `stale-while-revalidate` 不会阻塞用户的体验。
   - **与 `no-store` 的区别**：
     - `no-store` 禁止缓存，而 `stale-while-revalidate` 允许缓存和使用过期缓存。例如：
     ```javascript
     res.set("Cache-Control", "no-store");
     ```
     - `no-store` 不允许任何缓存，而 `stale-while-revalidate` 是一种更灵活的缓存策略，平衡了性能和内容更新。

## 性能影响

   - 使用 `stale-while-revalidate` 可以提高用户体验，尤其是在高流量或资源更新不那么频繁的情况下。它可以避免用户等待新资源的时间，同时通过后台更新缓存，保证了资源的相对新鲜度。但对于高度动态的内容，可能会使用户在一段时间内看到稍旧的信息。

总之，`stale-while-revalidate` 是一种灵活的 HTTP 缓存策略，它通过允许使用过期缓存并在后台更新，在性能和内容更新之间找到了平衡，适合一些对用户体验要求较高且对内容新鲜度有一定容忍度的场景。在使用时，要根据资源的重要性和更新频率来合理设置缓存策略。
