这份字幕文件深入剖析了 **React Server Components (RSC) 的幕后渲染机制**，解释了*组件在服务端和客户端是如何一步步被渲染并最终呈现在屏幕上的*。

以下是内容的结构化总结：

## 1. 回顾：传统 React 的渲染过程
在了解 RSC 之前，先回顾*传统 React（纯客户端渲染）的机制：*
- `组件树 (Component Tree)`：将编写的组件组合在一起*形成树状结构*。
- `渲染 (Rendering)`：*调用每个组件函数*。
- `React Element (虚拟DOM)`：组件函数执行后返回 React Element，包含创建真实 DOM 所需的信息。
- `提交 (Commit)`：将虚拟 DOM 转化为真实的 DOM 元素，显示在屏幕上。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/e16b8341717ca9ec0dd49bc8c603ff4a_MD5.webp]]

---

## 2. **RSC 架构下的两步渲染流程**
在 RSC 架构中，`组件树`包含 **Server Components (SC)** 和 **Client Components (CC)**。渲染过程被拆分为两步，跨越服务端和客户端：

### 第一步：在服务端渲染 Server Components
- **执行 SC**：服务端执行 Server Components，生成对应的 React Elements（只包含 DOM 结构信息）。
- **代码“消失”**：SC 的源代码在服务端执行完毕后就被丢弃了，不会发送到客户端。
- **为什么 SC 不能用 Hooks (如 useState)？**
    - 因为 Hooks 是函数，而函数**无法被序列化**发送到客户端。
    - 服务端没有 Fiber 树来跟踪状态，即使有也无法传给客户端。
- **处理 Client Components (CC)**：CC 不会在服务端渲染。在 SC 生成的树中，CC 的位置会留下一个 **“占位符 (Placeholder/Hole)”**。
    - 占位符包含两部分关键信息：
        1.  从父 SC 传递给该 CC 的**序列化 Props**。
        2.  包含该 CC 实际代码的**脚本 URL**（由框架的打包工具生成）。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/88636717e7d69a0c30e19614d6113ab7_MD5.webp]]

#### 中间产物：生成 RSC Payload
- **第一步完成后，会生成一个混合了 *“已渲染的 SC”* 和 *“未渲染的 CC 占位符”* 的*树***，这被称为 `RSC Payload`。
- **RSC Payload 会被转换为一种*类似 JSON* 但支持 *流式传输 (Streaming)* 的*自定义数据格式*，从服务端发送到客户端**。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/4d7e6ae8c7163eff3272f65975f44e47_MD5.webp]]

### 第二步：在客户端渲染 Client Components
- 客户端接收到 RSC Payload 后，*根据占位符中的 URL 下载 CC 的 JS 代码*。
- *结合传入的 Props，在客户端执行 CC，生成完整的 React Elements*。
- *至此，客户端拥有了完整的虚拟 DOM，随后将其 Commit 到真实 DOM 中，完成页面渲染*。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/a116b3945ca0a8031aa5d51079001c56_MD5.webp]]

---

## 3. 核心疑问：为什么不直接发送 HTML？
既然在服务端渲染了，为什么不直接生成 HTML 发给浏览器，而要搞出复杂的 RSC Payload？
- **React 的核心哲学**：React 始终希望将 UI 描述为**数据（虚拟DOM/Fiber）**，而不是最终的 HTML 字符串。
- **保留 UI 状态 (State Preservation)**：当 Server Component 重新渲染（例如路由切换）时，会生成新的 RSC Payload 发给客户端。客户端 React 可以将新树与现有树进行 **Reconciliation（协调/对比）**。
- **避免体验割裂**：如果直接发送 HTML，整个页面 UI 会被强制替换，导致客户端所有的 UI 状态（如输入框的文字、展开的菜单）全部丢失。使用 RSC Payload 可以让 React 无缝合并更新，**完美保留客户端状态**。

---

## 4. 传统 React vs RSC 渲染对比总结
- **传统 React**：组件树 ➔ 虚拟DOM ➔ 真实DOM（一步到位，全在客户端）。
- **RSC 架构**：组件树 ➔ *(服务端)* 渲染 SC + 生成 CC 占位符 ➔ **RSC Payload** ➔ *(客户端)* 渲染 CC ➔ 完整虚拟DOM ➔ 真实DOM（分两步，跨环境，RSC Payload 是连接两端的桥梁）。
- **结论**：RSC 并不神秘，它本质上就是传统渲染过程在服务端和客户端的拆分。

---

## 5. 两个重要的补充观察
1.  **流式传输 (Streaming) 提升性能**：
    服务端的渲染和传输不是严格阻塞的。服务端完成一部分渲染后，就会立刻通过流 (Stream) 将 RSC Payload 发送给客户端，客户端无缝集成。这使得整个过程更加流畅，用户感知速度更快。
2.  **对 "UI = f(state)" 的哲学修正**：
    以前我们说 UI 是 state 的函数。但在 RSC 的两步流程下，更准确的表述是：
    - **第一步（服务端）**：UI 是 **Data（数据）** 的函数。
    - **第二步（客户端）**：第一步返回的结果，再作为 **State（状态）** 的函数进行渲染。
    *(讲师表示这只是一个极客视角的严谨性修正，不影响实际开发，但有助于更深刻地理解 RSC 的本质。)*