

## 源码

`packages/react-reconciler/src/ReactFiberHooks.old.js`

```ts
/**
 * 更新或创建一个work-in-progress hook
 * 
 * 这个函数用于更新阶段和由渲染阶段更新触发的重新渲染。
 * 它假设有两种情况之一存在：一个我们可以克隆的current hook，
 * 或者一个来自之前渲染过程的work-in-progress hook，我们可以将其作为基础。
 * 当我们到达基础列表的末尾时，我们必须切换到挂载时使用的调度器。
 * 
 * @returns {Hook} 返回当前正在处理的work-in-progress hook
 */
function updateWorkInProgressHook(): Hook {
  // 获取下一个current hook（上一次渲染时对应的hook）
  let nextCurrentHook: null | Hook;
  if (currentHook === null) {
    // 如果当前没有current hook，则从fiber的alternate中获取第一个hook
    const current = currentlyRenderingFiber.alternate;
    if (current !== null) {
      nextCurrentHook = current.memoizedState;
    } else {
      nextCurrentHook = null;
    }
  } else {
    // 否则获取下一个current hook
    nextCurrentHook = currentHook.next;
  }

  // 获取下一个work-in-progress hook（本次渲染过程中已处理的下一个hook）
  let nextWorkInProgressHook: null | Hook;
  if (workInProgressHook === null) {
    // 如果当前没有work-in-progress hook，则从fiber中获取第一个
    nextWorkInProgressHook = currentlyRenderingFiber.memoizedState;
  } else {
    // 否则获取下一个work-in-progress hook
    nextWorkInProgressHook = workInProgressHook.next;
  }

  if (nextWorkInProgressHook !== null) {
    // 已经存在一个work-in-progress hook，复用它
    workInProgressHook = nextWorkInProgressHook;
    // 更新指针到下一个work-in-progress hook
    nextWorkInProgressHook = workInProgressHook.next;

    // 更新currentHook指向正确的current hook
    currentHook = nextCurrentHook;
  } else {
    // 需要从current hook克隆一个新的work-in-progress hook
    
    // 如果nextCurrentHook为空，说明当前组件渲染的hooks数量多于前一次渲染
    // 这违反了React Hooks的规则（不能在条件语句中调用hook）
    if (nextCurrentHook === null) {
      throw new Error('Rendered more hooks than during the previous render.');
    }

    // 设置currentHook为下一个要处理的hook
    currentHook = nextCurrentHook;

    // 创建新的hook并复制相关状态
    const newHook: Hook = {
      // 复制上一次渲染时的状态值
      memoizedState: currentHook.memoizedState,
      // 复制基础状态
      baseState: currentHook.baseState,
      // 复制待处理的更新队列
      baseQueue: currentHook.baseQueue,
      // 复制更新队列（包含分发函数等）
      queue: currentHook.queue,
      // 初始化下一个hook指针为null
      next: null,
    };

    if (workInProgressHook === null) {
      // 这是本次渲染的第一个hook，设置为fiber的memoizedState
      currentlyRenderingFiber.memoizedState = workInProgressHook = newHook;
    } else {
      // 将新hook添加到现有hook链表的末尾
      workInProgressHook = workInProgressHook.next = newHook;
    }
  }
  // 返回当前处理的work-in-progress hook
  return workInProgressHook;
}
```