`updateReducer` 是 React Fiber 架构中*用于处理 `useState`、`useReducer` Hook* 的核心函数，**它负责计算state，在组件渲染期间处理状态更新逻辑**。函数会*根据更新优先级决定是否处理特定更新，并维护基础状态与待处理更新队列之间的关系*。


主要负责以下几个方面：

1. **获取当前 Hook**：通过 updateWorkInProgressHook() 获取当前正在工作的 Hook 对象。
2. **处理待处理更新**：合并之前未完成的更新队列 (pendingQueue) 和基础队列 (baseQueue)。
3. **优先级处理**：根据更新的优先级判断是否应该在当前渲染中应用更新。如果更新的优先级不够高（低于 renderLanes），则将其放入基础队列中等待后续处理。
4. **状态计算**：遍历更新队列，按顺序应用每个更新操作，计算出最新的状态值。
5. **性能优化**：利用 eagerState 属性避免不必要的重复计算。
6. **返回结果**：返回当前状态值和 dispatch 函数，这正是 `useReducer` Hook 返回的结果。

该函数还处理了交错更新队列，并确保正确标记 Fiber 节点已接收更新，这对于 React 的并发渲染机制至关重要。整个过程体现了 React 在处理状态更新时对性能和用户体验的深度优化考虑。

**算法：**
- 获取workInProgress hook，当前useState对应的hook，[[updateWorkInProgressHook]]
- 获取 workInProgress hook的queue（update queue）
- 有尚未处理的新更新（wipFiber.workInProgressHook.queue.pending中的更新），将它们添加到基础队列中（currentFiber.currentHook.baseQueue的末尾）

---

这段JavaScript代码是React内部的`useReducer`钩子实现。它负责：

1. 获取当前工作进行中的钩子状态
2. 处理队列中的更新操作，包括合并待处理更新到基础队列
3. 根据优先级筛选并执行更新，跳过低优先级更新
4. 计算新状态并更新钩子的备忘录状态
5. 返回包含当前状态和派发函数的数组



## 源码
`packages/react-reconciler/src/ReactFiberHooks.old.js`

```ts


```

## currentHook 与 workInProgressHook 的区别

```ts
// Hooks are stored as a linked list on the fiber's memoizedState field. The
// current hook list is the list that belongs to the current fiber. The
// work-in-progress hook list is a new list that will be added to the
// work-in-progress fiber.

// 指向current fiber节点的hook链表，存储现有的hooks状态
let currentHook: Hook | null = null;
// 指向workInProgress fiber节点的新hook链表，用于记录更新过程中的hooks
let workInProgressHook: Hook | null = null;
```

这两个变量都是 React 内部用来管理组件中 Hook 链表的指针，但它们分别指向不同的数据：

### currentHook

- **含义**: 指向*当前已渲染完成的 Hook 链表（current fiber上的hook链表）*
- **来源**: 来自wip Fiber 节点的 alternate（即上一次渲染的结果）
- **作用**: *提供上一次渲染时的状态，用于比较和更新*
- **生命周期**: 在组件更新时使用，代表之前的 Hook 状态

### workInProgressHook

- **含义**: 指向*正在进行渲染的 Hook 链表（workInProgress fiber上的hook链表）*
- **来源**: 存储在当前正在渲染的 workInProgress Fiber 节点上
- **作用**: 表示*本次渲染过程中创建或更新的 Hook 状态*
- **生命周期**: 在当前渲染过程中使用，渲染完成后会成为下一次渲染的 currentHook

### 关系

在 React 的 Fiber 架构中，这两个变量的关系体现了双缓冲机制：

1. 每次渲染开始时，currentHook 指向上一次渲染的结果
2. 渲染过程中，新的 Hook 状态被构建到 workInProgressHook 链表中
3. 渲染完成后，workInProgressHook 链表成为新的当前结果
4. 下次渲染时，它又成为了 currentHook

这种设计使得 React 能够在渲染过程中保持状态的一致性，并且能够有效地进行协调（reconciliation）算法。