- 根据ReactElement的类型，调用不同的reconcileXXX函数。
	- ReactElement是对象且是REACT_ELEMENT_TYPE类型，调用reconcileSingleElement；ReactElement是数组，调用reconcileChildrenArray。
	- `reconcileXXX函数`，diff算法就在这些函数中，满足diff算法，就复用旧的fiber；不满足，就新建fiber
- 为新建的fiber添加flags
- 返回构建的新fiber

## 源码
`react/packages/react-reconciler/src/ReactChildFiber.old.js`

```ts
/**
   * ！！！协调子节点的函数，根据新的React Element与旧的current fiber的差异，创建或更新子Fiber节点
   * ！！！这个函数处理不同类型的React元素（单个元素、数组、迭代器等），并执行相应的协调逻辑
   *
   * @param {Fiber} returnFiber - 父级Fiber节点，当前正在处理的Fiber节点
   * @param {Fiber | null} currentFirstChild - 当前的第一个子Fiber节点，用于与新的子节点进行比较
   * @param {any} newChild - 新的子节点，可以是各种类型的数据（元素、数组、字符串、数字等）
   * @param {Lanes} lanes - React渲染优先级相关的lanes信息
   * @returns {Fiber | null} 返回协调后的新子节点链表的头节点，如果没有子节点则返回null
   */
  function reconcileChildFibers(
    returnFiber: Fiber,
    currentFirstChild: Fiber | null,
    newChild: any,
    lanes: Lanes,
  ): Fiber | null {
    
    // 检查是否为顶级没有设置key属性的Fragment节点（即没有提供key属性的Fragment）
    // 处理顶层无键fragment，将其视为数组处理
    // 这会导致 <>{[...]}</> 和 <>...</> 之间的歧义
    // 我们将这些歧义情况同等对待
    const isUnkeyedTopLevelFragment =
      typeof newChild === 'object' &&
      newChild !== null &&
      newChild.type === REACT_FRAGMENT_TYPE &&
      newChild.key === null;
    // 如果是没有设置key属性的Fragment，提取其子节点作为新的待处理节点
    if (isUnkeyedTopLevelFragment) {
      newChild = newChild.props.children;
    }
    
    // 处理对象类型的ReactElement
    if (typeof newChild === 'object' && newChild !== null) {
      switch (newChild.$$typeof) {
        case REACT_ELEMENT_TYPE:
          return placeSingleChild(
            reconcileSingleElement(
              returnFiber,
              currentFirstChild,
              newChild,
              lanes,
            ),
          );
        case REACT_PORTAL_TYPE:
          return placeSingleChild(
            reconcileSinglePortal(
              returnFiber,
              currentFirstChild,
              newChild,
              lanes,
            ),
          );
        case REACT_LAZY_TYPE:
          const payload = newChild._payload;
          const init = newChild._init;
          // TODO: This function is supposed to be non-recursive.
          return reconcileChildFibers(
            returnFiber,
            currentFirstChild,
            init(payload),
            lanes,
          );
      }

      // 如果是数组类型的ReactElement，则使用数组协调逻辑
      if (isArray(newChild)) {
        return reconcileChildrenArray(
          returnFiber,
          currentFirstChild,
          newChild,
          lanes,
        );
      }

      // 如果是可迭代对象（如Map、Set等）的ReactElement，则使用迭代器协调逻辑
      if (getIteratorFn(newChild)) {
        return reconcileChildrenIterator(
          returnFiber,
          currentFirstChild,
          newChild,
          lanes,
        );
      }

      throwOnInvalidObjectType(returnFiber, newChild);
    }

    // 处理文本节点（字符串或数字）类型的ReactElement
    if (
      (typeof newChild === 'string' && newChild !== '') ||
      typeof newChild === 'number'
    ) {
      return placeSingleChild(
        reconcileSingleTextNode(
          returnFiber,
          currentFirstChild,
          '' + newChild,
          lanes,
        ),
      );
    }

    if (__DEV__) {
      if (typeof newChild === 'function') {
        warnOnFunctionType(returnFiber);
      }
    }

    // 剩余情况都被视为空节点，删除所有剩余的子节点
    return deleteRemainingChildren(returnFiber, currentFirstChild);
  }
```