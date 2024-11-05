如果我们不清楚 React 15 的运作机制，就无从把握它的局限性；如果我们不能确切地把握 React 15 的局限性，就无法从根本上理解 React 16 大改版背后的设计动机

根据 Diff 实现形式的不同，调和过程被划分为了**以 React 15 为代表**的 *“栈调和”* 以及 **React 16** 以来的 *“Fiber 调和”*。

### 调和（Reconciliation）过程与 Diff 算法

**Reconciliation**，“调和”又译为“**使一致**、协调”。

*调和过程的官方定义*，藏在 React 官网对虚拟 DOM 这一概念的解释中，原文如下：
> Virtual DOM 是一种编程概念。在这个概念里，UI 以一种理想化的，或者说“虚拟的”表现形式被保存于内存中，并通过如 ReactDOM 库*使 Virtual DOM 与“真实的” DOM 同步*。这一过程叫作[协调](https://zh-hans.reactjs.org/docs/reconciliation.html)（调和）。


- **调和是“使一致”的过程，使虚拟DOM和真实DOM一致。**
- **Diff 是“找不同”的过程，找新和旧的虚拟DOM的不同，它只是“使一致”过程中的一个环节。**











