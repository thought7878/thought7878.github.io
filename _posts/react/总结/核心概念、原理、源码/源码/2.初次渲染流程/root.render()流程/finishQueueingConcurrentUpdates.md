这段代码定义了 [finishQueueingConcurrentUpdates](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react-reconciler/src/ReactFiberConcurrentUpdates.old.js#L43-L67) 函数，用于**处理并发更新队列中的所有更新**。**这个函数会在批量处理阶段*将所有暂存的更新添加到相应的队列中***，**把concurrentQueues的内容添加到fiber的queue中**

```javascript
// 处理并发更新队列中的所有更新
// 这个函数会在批量处理阶段将所有暂存的更新添加到相应的队列中
// 把concurrentQueues的内容添加到fiber的queue中
export function finishQueueingConcurrentUpdates(): void {
  // 获取当前暂存的并发队列的结束索引
  const endIndex = concurrentQueuesIndex;
  // 重置索引为0，准备下一批处理
  concurrentQueuesIndex = 0;

  // 重置全局变量，表示当前没有正在并发更新的车道
  concurrentlyUpdatedLanes = NoLanes;

  // 初始化索引，用于遍历暂存的并发队列
  let i = 0;
  // 遍历暂存的每个并发更新
  while (i < endIndex) {
    // 获取更新关联的 Fiber 节点
    const fiber: Fiber = concurrentQueues[i];
    concurrentQueues[i++] = null;  // 清空已处理的元素，帮助垃圾回收
    
    // 获取更新队列
    const queue: ConcurrentQueue = concurrentQueues[i];
    concurrentQueues[i++] = null;  // 清空已处理的元素
    
    // 获取更新对象
    const update: ConcurrentUpdate = concurrentQueues[i];
    concurrentQueues[i++] = null;  // 清空已处理的元素
    
    // 获取更新的优先级车道
    const lane: Lane = concurrentQueues[i];
    concurrentQueues[i++] = null;  // 清空已处理的元素

    // 如果队列和更新都不为空，则将更新添加到队列中
    // 注意:这里构建完之后的fiber.updateQueue.shared.pending数据类型是Update，但是其实这里构建成了一个单向循环链表，所以fiber.updateQueue.shared.pending其实是指循环链表的最后一个update，它的next指向的是第一个update
    if (queue !== null && update !== null) {
      // 获取队列中当前等待的更新
      const pending = queue.pending;
      if (pending === null) {
        // 如果没有等待的更新，这是第一个更新，创建一个循环链表
        update.next = update;  // 让更新的 next 指向自己，形成循环
      } else {
        // 如果已有等待的更新，将新更新插入到链表中
        update.next = pending.next;  // 新更新的 next 指向原来的第一个更新
        pending.next = update;       // 原来的最后一个更新指向新更新
      }
      // 将新更新设置为等待处理的更新（最新的更新）
      queue.pending = update;
    }

    // 如果更新有优先级车道，则标记从 Fiber 到根节点的更新车道
    if (lane !== NoLane) {
      // 更新fiber.lanes
      // 从当前节点开始,往上找到根节点,更新childLanes
      
      // 这个函数会标记从当前 Fiber 到根节点的路径上的车道
      // 以便知道哪些部分需要重新渲染
      markUpdateLaneFromFiberToRoot(fiber, update, lane);
    }
  }
}
```

这个函数是 React 并发更新机制的关键部分，负责**批量处理在并发更新过程中暂存的所有更新。它将暂存的更新添加到各自的队列中，并标记需要更新的车道，确保 React 知道哪些部分需要重新渲染**。这是 React 实现高效并发更新的核心机制之一。

参考：[[markUpdateLaneFromFiberToRoot]]