>[Partial Prerendering (PPR)](https://nextjs.org/docs/app/glossary#partial-prerendering-ppr)
>
>A rendering optimization that combines prerendering and dynamic rendering in a single route. The static shell is served immediately while dynamic content streams in when ready, providing the best of both rendering strategies. Learn more in [Cache Components](https://nextjs.org/docs/app/getting-started/caching).  
>这是一种**渲染优化技术**，它**将预渲染和动态渲染结合在同一个处理流程中**。静态内容会**立即被呈现出来**，而动态内容则会**在准备好后再逐步加载**。这样一来，就能**兼得两种渲染方式的优点**。更多详情请参阅“缓存组件”部分。


这份字幕文件详细介绍了 Next.js 中一项*具有革命性的新渲染策略*：`局部预渲染（Partial Pre-rendering, 简称 PPR）`。

以下是内容的结构化总结：

## 1. 核心痛点：非黑即白的渲染困境
*   在传统的 Next.js 渲染模型中，每个路由（Route）要么是 **100% 静态**，要么是 **100% 动态**。
*   **问题场景**：假设一个页面 90% 的内容是静态的（如文章正文），只有 10% 是动态的（如导航栏显示当前登录用户的名字）。因为这一个动态元素的存在，**整个页面都会被迫降级为动态渲染**，从而**失去了静态页面可以通过 CDN 极速分发的性能优势**。

## 2. 什么是局部预渲染 (PPR)？
*   `PPR` 是一种**混合渲染策略**，允许在*同一个路由*中**同时存在静态和动态内容**，*打破了“全静态”或“全动态”的限制*。
*   它是*静态预渲染（Pre-rendering）和动态渲染的完美中间地带*。

## 3. PPR 的工作原理（三步走）
1.  **发送静态外壳 (Static Shell)**：当用户访问页面时，服务器会立即从 CDN 极速返回一个完全静态的页面“外壳”。
2.  **预留动态“空洞” (Holes)**：这个静态外壳中会预留出需要动态数据的区域（即空洞），并显示一个静态的 Fallback（如加载骨架屏）。
3.  **流式传输动态内容 (Streaming)**：与此同时，服务器在后台渲染动态部分。一旦渲染完成，就会通过流（Streaming）将这些动态内容发送到客户端，无缝填补静态外壳中的“空洞”。

## 4. 如何在代码中实现 PPR？
*(注：在视频录制时的 Next.js 14 中，PPR 仍为实验性/需手动开启的功能，但其 API 设计非常符合 React 现有习惯)*
*   **开启功能**：需要在 `next.config.js` 中显式开启（Opt-in）。
*   **使用 `<Suspense>` 边界**：PPR 直接复用了 React 现有的 `<Suspense>` API，无需学习新语法。
    *   将依赖动态数据（如读取 cookies、headers 或未缓存的 fetch）的组件包裹在 `<Suspense fallback={<Loading />}>` 中。
    *   **隔离作用**：`<Suspense>` 边界会**隔离**动态性，防止动态渲染“污染”整个路由。边界外的内容依然保持静态预渲染，边界内的内容则被标记为动态渲染。
    *   当动态内容准备就绪时，它会自动替换掉 `<Suspense>` 的 fallback 内容。

## 5. PPR 的核心优势
*   **极致的首屏性能**：大部分页面内容作为静态资源从边缘节点（Edge/CDN）瞬间送达。
*   **避免过度动态化**：不再因为页面中微小的动态需求（如读取一个 cookie）而牺牲整个页面的静态缓存优势。
*   **开发体验友好**：直接利用开发者已经熟悉的 React `<Suspense>` 组件即可实现，学习成本极低。

---
**💡 总结提示**：
PPR 代表了 Next.js 渲染策略的未来方向。它通过“静态外壳 + 动态流式填充”的模式，在保证极致加载速度的同时，完美兼容了个性化的动态需求。建议在实际项目中关注 Next.js 官方文档，待其稳定后积极应用以提升应用性能。