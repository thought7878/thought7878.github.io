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
- 有尚未处理的新更新（wipFiber.workInProgressHook.queue.*pending*中的更新），将它们添加到基础队列中（currentFiber.currentHook.*baseQueue*的末尾）
- 有尚未处理的更新（baseQueue有值），*遍历更新队列，处理每个更新：*
	- 如果update优先级不足，跳过此更新
		- 克隆这个update
		- 将克隆的update放入newBaseQueue中
		- 将这个update的优先级保存到wipFiber.lanes中（标记下来，给下次渲染处理）
		- 标记跳过的更新优先级，将其lane记录到workInProgressRootSkippedLanes中，[[markSkippedUpdateLanes]]
	- 如果update优先级足够，
		- 如果之前已经*有更新被跳过了*（即优先级不够，无法在此次渲染中处理），被跳过的更新后面的更新都需按顺序被添加进新的基础队列，保证顺序
			- 创建一个新的 Update 对象，复制当前更新的关键属性
			- 将新克隆的更新，添加到新基础队列的末尾
		- **处理此更新，计算新状态**
	- 向后移动到下一个update，继续下一个循环
- 标记fiber收到了更新/执行了工作，但仅当新状态与当前状态不同时，[[markWorkInProgressReceivedUpdate]]
- 更新hook对象的属性（memoizedState、baseState、baseQueue）
- 更新queue的lastRenderedState

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

## 更新被跳过的处理逻辑

### 源码：

```ts
// 之前已经有更新被跳过了（即优先级不够，无法在此次渲染中处理）
if (newBaseQueueLast !== null) {
  // 创建一个新的 Update 对象，复制当前更新的关键属性
  const clone: Update<S, A> = {
	// 将lane设置为 NoLane (值为 0)，这很重要，因为它确保这个更新永远不会被跳过，因为 0 是所有位掩码的子集
	lane: NoLane,
	action: update.action,
	hasEagerState: update.hasEagerState,
	eagerState: update.eagerState,
	next: (null: any),
  };
  // 将新克隆的更新添加到新基础队列的末尾
  // 将当前尾节点的 next 指针指向新克隆的更新
  // 更新尾节点引用，使其指向新添加的节点
  newBaseQueueLast = newBaseQueueLast.next = clone;
}
```

### 详细解释：

1. **条件判断** (`newBaseQueueLast !== null`)
   - 这个条件检查是否已经有一个"基础更新队列"被建立
   - `newBaseQueueLast` 是一个链表的尾节点引用，用于存储那些将在后续渲染中继续保留的更新
   - 当 `newBaseQueueLast !== null` 时，意味着之前已经有更新被跳过了（即优先级不够，无法在此渲染中处理）

2. **创建更新克隆** (`const clone: Update<S, A>`)
   - 创建一个新的 [Update](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js#L243-L263) 对象，复制当前更新的关键属性
   - `lane: NoLane` - 将车道设置为 `NoLane` (值为 0)，这很重要，因为它确保这个更新永远不会被跳过，因为 0 是所有位掩码的子集
   - 其他属性（[action](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js#L254-L254)、[hasEagerState](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactUpdateQueue.js#L31-L31)、[eagerState](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactUpdateQueue.js#L30-L30)）被保留，以维持更新的完整信息

3. **链表操作** (`newBaseQueueLast = newBaseQueueLast.next = clone`)
   - 这是一个链表追加操作，将新克隆的更新添加到基础队列的末尾
   - `newBaseQueueLast.next = clone` - 将当前尾节点的 next 指针指向新克隆的更新
   - `newBaseQueueLast = ...` - 更新尾节点引用，使其指向新添加的节点

### 业务场景：

*这段代码出现在处理高优先级更新的分支中，当一个更新有足够的优先级被处理时：*

1. **如果已经有低优先级的更新被跳过**（`newBaseQueueLast !== null` 表示已经有跳过的更新），**那么当前处理的这个高优先级更新也需要被添加到基础队列中，因为它也将在未来的渲染中需要被处理**
2. 将 [lane](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react-reconciler/src/ReactFiberReconciler.old.js#L502-L502) 设置为 `NoLane` 是*为了确保这个更新不会在未来被跳过，因为它已经被确认处理过了*

### React 并发机制背景：

- React 使用优先级系统来决定哪些更新应该被立即处理，哪些可以推迟
- 当用户快速连续触发多次更新时，低优先级的更新（如滚动）可能被高优先级的更新（如点击响应）打断
- 这段代码确保被跳过的更新能够被保留并在适当的时候重新处理，从而避免更新丢失

### 总结：

这段代码是 React 并发更新机制的核心部分，确保了即使在渲染过程中有优先级更高的更新到来，较低优先级的更新也不会丢失，而是被妥善保存起来，在合适的时机重新进入更新队列，从而保证了状态更新的完整性。


## 处理交错更新
这段代码来自React的源码，位于[ReactFiberHooks.old.js](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberHooks.old.js)文件中，它*处理React Hooks中的交错更新(Interleaved Updates)机制*。

```javascript
// 获取队列中最后一次交错更新的引用
const lastInterleaved = queue.interleaved;

// 如果存在交错更新（在当前渲染过程中新加入的更新）
if (lastInterleaved !== null) {
  // 初始化遍历指针，从最后一个交错更新开始
  let interleaved = lastInterleaved;
  
  // 循环遍历整个交错更新链表，直到回到起点
  do {
    // 获取当前交错更新的lane（优先级）
    const interleavedLane = interleaved.lane;
    
    // 将这个更新的lane合并到当前正在渲染的fiber的lanes中
    // ！！！这样做是为了追踪还有哪些更新需要处理
    currentlyRenderingFiber.lanes = mergeLanes(
      currentlyRenderingFiber.lanes,
      interleavedLane,
    );
    
    // ！！！标记这些lane的更新被跳过了，以便React知道这些优先级的更新并未完成
    markSkippedUpdateLanes(interleavedLane);
    
    // 移动到链表中的下一个更新
    interleaved = ((interleaved: any).next: Update<S, A>);
  } while (interleaved !== lastInterleaved);  // 继续循环直到回到起始点
} 
else if (baseQueue === null) {
  // 如果没有交错更新，且基础队列(baseQueue)为空
  // `queue.lanes` 用于纠缠转换（entangling transitions）。一旦队列为空，我们可以将其设置回零
  // 这里重置队列的lanes为NoLanes（表示没有优先级）
  queue.lanes = NoLanes;
}
```


### 1. 交错更新(Interleaved Updates)概念
在React的并发特性中，**当一个组件正在渲染时，可能有新的状态更新被调度。这些在渲染过程中新加入的更新被存储在`queue.interleaved`中，而不是立即处理**。

### 2. 代码第一部分 
`lastInterleaved !== null`
- 当前渲染过程中产生了新的更新，这些更新被存储在交错队列中
- 循环遍历这些交错更新，将它们的优先级(lane)合并到当前fiber的lanes中
- 调用`markSkippedUpdateLanes`标记这些更新被跳过了，以便后续处理

### 3. 代码第二部分 
`baseQueue === null`
- 当没有交错更新且基础队列也为空时，意味着没有待处理的更新
- 将队列的lanes重置为`NoLanes`，这是一种优化，避免不必要的追踪

### 4. 为什么需要这种机制？
- **并发渲染**: 在并发模式下，React可以中断渲染过程，交错更新机制**确保这些中断期间产生的更新不会丢失**
- **优先级管理**: 确保高优先级的更新能够正确地影响渲染结果
- **时间切片**: 允许React在长时间运行的渲染过程中接收新的更新

这个机制是React并发特性的核心部分，它使得React能够在渲染过程中响应更高优先级的更新，同时确保所有更新最终都会被正确处理。