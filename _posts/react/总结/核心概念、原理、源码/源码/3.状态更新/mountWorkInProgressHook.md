**算法：**
- 创建一个新的 hook 对象，初始化其各个属性
- 将新的 hook 对象，添加到fiber 的 memoizedState 保存的hook链表中
- 更新workInProgressHook（hook链表的指针）为新的hook对象
- 返回当前创建的 hook

## 源码
``

```ts
function mountWorkInProgressHook(): Hook {
  // 创建一个新的 hook 对象，初始化其各个属性
  const hook: Hook = {
    memoizedState: null, // 存储 hook 的当前状态值

    baseState: null, // 基础状态，用于计算更新后的新状态
    baseQueue: null, // 基础队列，存储待处理的更新
    queue: null, // 存储更新队列，包含待处理的更新和分发函数

    next: null, // 指向下一个 hook 的指针，用于构建 hooks 链表
  };

  // 判断当前是否是第一个 hook
  if (workInProgressHook === null) {
    // 如果是第一个 hook，则设置为 fiber 的 memoizedState，并同时更新 workInProgressHook（hook链表的指针）
    currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
  } else {
    // 如果不是第一个 hook，则将其添加到现有 hooks 链表的末尾
    workInProgressHook = workInProgressHook.next = hook;
  }
  // 返回当前创建的 hook
  return workInProgressHook;
}
```
