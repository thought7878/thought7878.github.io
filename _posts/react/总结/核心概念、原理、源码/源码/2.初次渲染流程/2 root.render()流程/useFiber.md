复用已存在的fiber节点（workInProgress/current.alternate），使用新元素的 props

## 源码

```ts
/**
   * 复用已存在的fiber节点，使用新元素的 props
   *
   * @param {Fiber} fiber - 要克隆的原始fiber节点
   * @param {mixed} pendingProps - 新的待处理属性，将应用到克隆的fiber节点
   * @returns {Fiber} 返回已克隆的fiber节点，其index被设置为0，sibling被设置为null
   */
  function useFiber(fiber: Fiber, pendingProps: mixed): Fiber {
    // We currently set sibling to null and index to 0 here because it is easy
    // to forget to do before returning it. E.g. for the single child case.
    const clone = createWorkInProgress(fiber, pendingProps);
    clone.index = 0;
    clone.sibling = null;
    return clone;
  }

```