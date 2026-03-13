首次渲染时（mount），调用`useState`，最终执行`mountState`


**算法：**
- 创建一个新的 hook 对象，并添加到当前正在渲染的 fiber 的 memoizedState 保存的hook链表中，[[mountWorkInProgressHook]]
	- 如果初始状态是一个函数，则执行该函数获取实际的初始状态值
	- 将初始状态设置为 hook 的 memoizedState 和 baseState
- 创建一个更新队列
	- 将 update 队列赋值给 hook.queue
- 创建 dispatch 函数，绑定当前 fiber 和队列
- 返回状态值和更新函数

## 源码
`packages/react-reconciler/src/ReactFiberHooks.old.js`

```ts

function mountState<S>(
  initialState: (() => S) | S, // 初始状态，可以是值或返回值的函数
): [S, Dispatch<BasicStateAction<S>>] {// 返回状态值和更新函数的元组
  
  // 创建一个新的 hook 对象，并添加到当前正在渲染的 fiber 的 hooks 链表中
  const hook = mountWorkInProgressHook();

  // 如果初始状态是一个函数，则执行该函数获取实际的初始状态值
  if (typeof initialState === 'function') {
    // $FlowFixMe: Flow doesn't like mixed types
    initialState = initialState();
  }

  // 将初始状态设置为 hook 的 memoizedState 和 baseState
  hook.memoizedState = hook.baseState = initialState;

  // 创建一个更新队列，来管理状态的更新
  const queue: UpdateQueue<S, BasicStateAction<S>> = {
    pending: null, // 等待处理的更新
    interleaved: null, // 交错更新
    lanes: NoLanes, // 优先级通道
    dispatch: null, // 分发函数，稍后设置
    lastRenderedReducer: basicStateReducer, // 最后一次渲染时使用的 reducer
    lastRenderedState: (initialState: any), // 最后一次渲染时的状态
  };

  // 将 update 队列赋值给 hook.queue
  hook.queue = queue;

  // 创建 dispatch 函数，绑定当前 fiber 和队列
  const dispatch: Dispatch<BasicStateAction<S>> = (queue.dispatch =
    (dispatchSetState.bind(null, currentlyRenderingFiber, queue): any));

  // 返回状态值和更新函数
  return [hook.memoizedState, dispatch];
}
```