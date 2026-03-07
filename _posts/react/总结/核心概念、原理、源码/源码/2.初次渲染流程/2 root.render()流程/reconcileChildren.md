- 首次渲染时，使用mountChildFibers函数，创建子fiber（根据ReactElement），不会跟踪副作用
- 更新渲染时，使用reconcileChildFibers函数，构建子fiber（复用旧的fiber，diff算法对比新的ReactElement与旧的current fiber；不能复用，就新建），跟踪副作用
- 为workInProgress.child赋值

## 源码

```ts
/**
 * 协调子节点，根据当前节点状态决定是挂载新子节点还是协调现有子节点
 * 这是React核心算法diff算法的关键入口之一，用于构建work-in-progress树
 * @param {Fiber | null} current - 当前已渲染的fiber节点，可能为null（首次渲染）
 * @param {Fiber} workInProgress - 正在处理的工作中fiber节点
 * @param {any} nextChildren - 下一次渲染的子节点
 * @param {Lanes} renderLanes - 渲染优先级通道
 */
export function reconcileChildren(
  current: Fiber | null,
  workInProgress: Fiber,
  nextChildren: any,
  renderLanes: Lanes,
) {
  if (current === null) {
    // If this is a fresh new component that hasn't been rendered yet, we
    // won't update its child set by applying minimal side-effects. Instead,
    // we will add them all to the child before it gets rendered. That means
    // we can optimize this reconciliation pass by not tracking side-effects.
    
    // 首次渲染时，使用mountChildFibers函数，创建子fiber，不会跟踪副作用
    workInProgress.child = mountChildFibers(
      workInProgress,
      null,
      nextChildren,
      renderLanes,
    );
  } else {
    // If the current child is the same as the work in progress, it means that
    // we haven't yet started any work on these children. Therefore, we use
    // the clone algorithm to create a copy of all the current children.

    // If we had any progressed work already, that is invalid at this point so
    // let's throw it out. 返回fiber，不论是复用或是重新创建。
    
    // 更新渲染时，使用reconcileChildFibers函数，构建子fiber（复用旧的fiber，diff算法对比新的ReactElement与旧的current fiber；不能复用，就新建），跟踪副作用
    workInProgress.child = reconcileChildFibers(
      workInProgress,
      current.child,
      nextChildren,
      renderLanes,
    );
  }
}

```