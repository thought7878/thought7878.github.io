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
```ts
// packages/react-reconciler/src/ReactFiberHooks.js :1338
mountReducer(){

  mountWorkInProgressHook()

  const dispatch: Dispatch<A> = (queue.dispatch = (dispatchReducerAction.bind(

null,

currentlyRenderingFiber,

queue,

): any));

return [hook.memoizedState, dispatch];
}




```

# 参考

[Hook 规则背后的原因](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=68)

- [00:37](https://www.bilibili.com/video/BV1SDm2Y3ETD?p=68&t=37.958153#t=37.96) Hook 类型
- [02:31](https://www.bilibili.com/video/BV1SDm2Y3ETD?p=68&t=151.072178#t=02:31.07) Hook 如何存储

[源码解读](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=69)

[useReducer的源码解读](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=70)
