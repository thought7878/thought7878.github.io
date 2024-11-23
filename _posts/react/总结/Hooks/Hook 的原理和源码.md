**Hook 使⽤规则**：不要在循环，条件或嵌套函数中调⽤ Hook， 确保总是在你的
React 函数的最顶层以及任何 return 之前调⽤他们。

React 函数：React 组件函数、⾃定义 Hook。

函数组件的 fiber.memoizedState 存储的是 Hook 的单链表的头节点。单链表的每个 hook 节点没有名字或者 key，因为除了它们的顺序，我们⽆法记录它们的唯⼀性。**因此为了确保某个 Hook 是它本身，我们不能破坏这个链表的稳定性**。

# Hook 类型

源码中 Hook 类型定义如下：

```ts
// packages/react-reconciler/src/ReactFiberHooks.js :198
export type Hook = {
  memoizedState: any; // 不同情况下，取值也不同：useState/useReducer存的是state，useEffect/useLayoutEffect存的是effect单向循环链表
  baseState: any;
  baseQueue: Update<any, any> | null;
  queue: any;
  next: Hook | null; // 下⼀个Hook，如果为null，证明这是最后⼀个hook
};
```

# Hook 存储

fiber.memorizedState = (hook0) -next-> (hook1) -next-> (hook2) -next-> (hook3)-next->null

```ts
let workInProgressHook = null; //当前正在工作中的Hook，记录执行到第几个Hook

//let workInProgressHook = null
//hook3

function FunctionalComponent() {
  // hook0
  const [hook0, setState1] = useState(0);
  // hook1
  useEffect(() => {

	}, [dependency1, dependency2,...]);
  // hook2
  const ref = useRef(initialValue);
  // hook3
  const [hook3, setState3] = useState(1);


  //...
}
```

# 源码

如果以下代码没有特殊标记路径的源码，那么路径都是 react/packages/react-reconciler/src/ReactFiberHooks.js

## useReducer

### 函数组件挂载阶段

#### mountReducer

```ts
// packages/react-reconciler/src/ReactFiberHooks.js :1338
mountReducer(){

  mountWorkInProgressHook()

  const dispatch = dispatchReducerAction()

  return [hook.memoizedState, dispatch];
}


```

#### mountWorkInProgressHook

```ts
function mountWorkInProgressHook(): Hook {
  const hook: Hook = {
    memoizedState: null,
    baseState: null,
    baseQueue: null,
    queue: null,

    next: null,
  };
  // 构建单链表
  if (workInProgressHook === null) {
    // This is the first hook in the list
    // 头结点
    currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
  } else {
    // Append to the end of the list
    // 加入单链表尾部，同时更新workInProgressHook
    workInProgressHook = workInProgressHook.next = hook;
  }
  return workInProgressHook;
}
```

#### dispatchReducerAction

```ts
// packages/react-reconciler/src/ReactFiberHooks.js :3713
function dispatchReducerAction<S, A>(
  fiber: Fiber,
  queue: UpdateQueue<S, A>,
  action: A,
): void {
  const lane = requestUpdateLane(fiber);
  // ! 1.创建update对象
  const update: Update<S, A> = {
    lane,
    revertLane: NoLane,
    action,
    hasEagerState: false,
    eagerState: null,
    next: (null: any),
  };

  if (isRenderPhaseUpdate(fiber)) {
    enqueueRenderPhaseUpdate(queue, update);
  } else {
    // 2.把update暂存到concurrentQueues数组中
    const root = enqueueConcurrentHookUpdate(fiber, queue, update, lane);
    if (root !== null) {
      // 3.调度更新
      startUpdateTimerByLane(lane);
      scheduleUpdateOnFiber(root, fiber, lane);
      entangleTransitionUpdate(root, queue, lane);
    }
  }

  markUpdateInDevTools(fiber, lane, action);
}
```

### 函数组件更新阶段

#### updateReducer

```ts
function updateReducer<S, I, A>(
  reducer: (S, A) => S,
  initialArg: I,
  init?: I => S,
): [S, Dispatch<A>] {
  const hook = updateWorkInProgressHook();
  return updateReducerImpl(hook, ((currentHook: any): Hook), reducer);
}
```

#### updateWorkInProgressHook

```ts
function updateWorkInProgressHook(): Hook {
  let nextCurrentHook: null | Hook;
  if (currentHook === null) {
    const current = currentlyRenderingFiber.alternate;
    if (current !== null) {
      nextCurrentHook = current.memoizedState;
    } else {
      nextCurrentHook = null;
    }
  } else {
    nextCurrentHook = currentHook.next;
  }

  let nextWorkInProgressHook: null | Hook;
  if (workInProgressHook === null) {
    nextWorkInProgressHook = currentlyRenderingFiber.memoizedState;
  } else {
    nextWorkInProgressHook = workInProgressHook.next;
  }

  if (nextWorkInProgressHook !== null) {
    // There's already a work-in-progress. Reuse it.
    workInProgressHook = nextWorkInProgressHook;
    nextWorkInProgressHook = workInProgressHook.next;

    currentHook = nextCurrentHook;
  } else {
    // Clone from the current hook.

    if (nextCurrentHook === null) {
      const currentFiber = currentlyRenderingFiber.alternate;
      if (currentFiber === null) {
        // This is the initial render. This branch is reached when the component
        // suspends, resumes, then renders an additional hook.
        // Should never be reached because we should switch to the mount dispatcher first.
        throw new Error(
          "Update hook called on initial render. This is likely a bug in React. Please file an issue."
        );
      } else {
        // This is an update. We should always have a current hook.
        throw new Error("Rendered more hooks than during the previous render.");
      }
    }

    currentHook = nextCurrentHook;

    const newHook: Hook = {
      memoizedState: currentHook.memoizedState,

      baseState: currentHook.baseState,
      baseQueue: currentHook.baseQueue,
      queue: currentHook.queue,

      next: null,
    };

    if (workInProgressHook === null) {
      // This is the first hook in the list.
      currentlyRenderingFiber.memoizedState = workInProgressHook = newHook;
    } else {
      // Append to the end of the list.
      workInProgressHook = workInProgressHook.next = newHook;
    }
  }
  return workInProgressHook;
}
```

#### updateReducer

#### updateReducerImpl

## useState

### 函数组件挂载阶段

#### mountState

```ts
function mountState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  const hook = mountStateImpl(initialState);
  const queue = hook.queue;
  const dispatch: Dispatch<BasicStateAction<S>> = (dispatchSetState.bind(
    null,
    currentlyRenderingFiber,
    queue,
  ): any);
  queue.dispatch = dispatch;
  return [hook.memoizedState, dispatch];
}
```

#### dispatchSetState

```ts
function dispatchSetState<S, A>(
  fiber: Fiber,
  queue: UpdateQueue<S, A>,
  action: A
): void {
  const lane = requestUpdateLane(fiber);
  const didScheduleUpdate = dispatchSetStateInternal(
    fiber,
    queue,
    action,
    lane
  );
  if (didScheduleUpdate) {
    startUpdateTimerByLane(lane);
  }
  markUpdateInDevTools(fiber, lane, action);
}
```

### 函数组件更新阶段

```ts
function updateState<S>(
  initialState: (() => S) | S
): [S, Dispatch<BasicStateAction<S>>] {
  return updateReducer(basicStateReducer, initialState);
}
```

# 参考

[Hook 规则背后的原因](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=68)

- [00:37](https://www.bilibili.com/video/BV1SDm2Y3ETD?p=68&t=37.958153#t=37.96) Hook 类型
- [02:31](https://www.bilibili.com/video/BV1SDm2Y3ETD?p=68&t=151.072178#t=02:31.07) Hook 如何存储

[源码解读](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=69)

[useReducer 的源码解读](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=70)
