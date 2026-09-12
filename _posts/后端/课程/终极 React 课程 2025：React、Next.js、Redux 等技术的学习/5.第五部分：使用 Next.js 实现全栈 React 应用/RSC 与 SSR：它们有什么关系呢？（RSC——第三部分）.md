
![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/ac5a32de644f79212f1690e005305c24_MD5.webp]]

这份字幕文件详细探讨了 **React Server Components (RSC) 与 Server-Side Rendering (SSR，服务端渲染) 之间的关系**，并重点澄清了开发者在学习 RSC 时最容易产生的概念混淆。

以下是内容的结构化总结：

## 1. 核心澄清：RSC ≠ SSR
*   **两者是互补关系**：RSC 和 SSR 是完全不同的独立技术，RSC 的目的不是替代 SSR，而是与之结合工作（通常由 Next.js 等框架来实现）。
*   **最大的概念误区**：很多开发者误以为 RSC 协议中的 "Server/Client" 等同于传统意义上的 "Web 服务器/浏览器"。
    *   **React Server**：只是一个**执行环境**，指代“非浏览器的计算机环境”。它不一定非要是运行中的 Web 服务器，理论上也可以是构建时的本地环境（用于静态站点生成）。
    *   **React Client**：同样是一个**执行环境**，指代“消费渲染结果的环境”。在 SSR 场景下，这个“客户端”环境其实是在 Web 服务器上运行的，其任务是将组件渲染为 HTML 而不是 DOM 节点。

## 2. 结合 RSC 时的 SSR 初始渲染流程
当框架（如 Next.js）将 SSR 与 RSC 结合时，初始渲染的流程如下：
*   **全部在服务端渲染**：在首次请求时，**Server Components 和 Client Components 都会在 Web 服务器上被渲染**。
*   **生成 HTML**：这并不奇怪，因为在 RSC 出现之前，所有组件（即现在的 Client Components）在 SSR 时也是由服务端渲染的。最终，整个组件树会被转换为 HTML 发送给浏览器。

## 3. 水合 (Hydration) 与 RSC Payload
HTML 发送给浏览器后，还需要让页面具备交互性，这就涉及水合（Hydration）：
*   **发送的内容**：除了 HTML，服务器还会发送 React JS 代码块 (chunks) 以及 **RSC Payload**。
*   **RSC Payload 的作用**：包含已渲染的 Server Components 数据、传递给 Client Components 的 Props，以及 Client Components 代码的 URL。它让 React 在客户端拥有完整的组件树视图，以便后续更新时能保留 UI 状态。
*   **关键区别（只有 Client 组件被水合）**：因为 Server Components 没有状态和交互性，所以**只有 Client Components 会被水合（Hydrated）**，从而让 HTML 变成可交互的 React 应用。

## 4. 初始渲染后的运行机制
SSR 仅仅影响**初始渲染（首屏）**。一旦页面在浏览器中完成水合，应用就变成了一个常规的交互式 React 应用：
*   此时，"React Server" 就是真正的 Web 服务器，"React Client" 就是用户的浏览器。
*   当用户导航导致 Server Component 重新渲染时，服务器会生成新的 RSC Payload 发送给浏览器，与现有的虚拟 DOM 树无缝合并，从而**完美保留客户端的 UI 状态**。

---

## 总结：开发者必须牢记的 2 个核心要点
讲师强调，即使不理解底层的所有细节也能使用 Next.js，但必须记住以下两点：
1.  **初始 SSR 阶段**：Server Components 和 Client Components **都在服务器上执行**（生成 HTML）。
2.  **初始渲染之后（交互阶段）**：Server Components **只在 Web 服务器上运行**，而 Client Components **只在浏览器（客户端）上运行**。
