**ClassComponent 与 HostRoot 共用一套 Update 结构， FunctionComponent 单独使用一种 Update 结构**。

## Function Component
React调度更新的数据结构类型：
- **Update类型**：表示单个更新对象，包含更新优先级、动作、状态等信息
- **UpdateQueue类型**：表示更新队列，管理待处理更新、交错更新、调度函数等

这两个类型用于React内部状态更新机制，确保组件状态*按正确顺序和优先级进行更新*。

参考：[[basicStateReducer]]

`packages/react-reconciler/src/ReactFiberHooks.old.js`

```ts
/**
 * Update对象表示一个状态更新操作
 * 包含更新所需的各种信息，如优先级、动作、状态等
 */
export type Update<S, A> = {|
  /** 更新的优先级通道 */
  lane: Lane,
  /** 更新的动作数据，将被传递给reducer */
  action: A,
  /** 标识是否已预先计算新状态 */
  hasEagerState: boolean,
  /** 预先计算的新状态（如果有的话） */
  eagerState: S | null,
  /** 指向下一个更新的指针，用于构建循环链表 */
  next: Update<S, A>,
|};

/**
 * UpdateQueue是一个更新队列，管理一系列待处理的状态更新
 * 使用循环链表结构存储多个更新
 */
export type UpdateQueue<S, A> = {|
  /** 指向最后一个入队的更新，其next指向第一个更新，为null时表示没有更新 */
  pending: Update<S, A> | null,
  /** 交错更新队列，用于支持时间片打断机制 */
  interleaved: Update<S, A> | null,
  /** 当前队列中所有更新的lane集合 */
  lanes: Lanes,
  /** 分发新更新的函数，对应dispatch函数 */
  dispatch: ((A) => mixed) | null,
  /** 最后一次渲染时使用的reducer函数（状态更新函数，如basicStateReducer） */
  lastRenderedReducer: ((S, A) => S) | null,
  /** 最后一次渲染时的状态值 */
  lastRenderedState: S | null,
|};

```


## Class Component、HostRoot
`packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js`

```ts
// 定义更新类型，描述一次状态或属性的更改
export type Update<State> = {|
  // TODO: 临时字段。将通过在根节点上存储 transition -> event time 映射来移除此字段
  eventTime: number,           // 事件发生的时间戳，用于优先级计算和调度

  lane: Lane,                 // 该更新所属的优先级车道，决定更新的执行顺序

  tag: 0 | 1 | 2 | 3,        // 更新标签，区分不同类型的更新操作：
                              // 0 = UpdateState (状态更新)
                              // 1 = ReplaceState (替换状态)
                              // 2 = ForceUpdate (强制更新)
                              // 3 = CaptureUpdate (捕获更新，用于错误处理)

  payload: any,               // 更新的负载数据，根据更新类型不同而不同：
                              // - 对于状态更新，是新的状态值或状态计算函数
                              // - 对于属性更新，是新的属性对象

  callback: (() => mixed) | null,  // 更新完成后的回调函数，通常用于 componentDidUpdate
                                   // 或 setState 的回调参数

  next: Update<State> | null,      // 指向下一个更新的指针，形成链表结构，用于连接同一队列中的多个更新
|};


// 共享队列类型定义，用于跟踪等待中的更新
export type SharedQueue<State> = {|
  pending: Update<State> | null,  // 指向等待处理的最新更新，形成一个循环链表
  lanes: Lanes,                   // 表示当前等待的更新所处的优先级车道
|};


// 更新队列类型定义，完整描述了一个组件的更新状态
export type UpdateQueue<State> = {|
  baseState: State,                                    // 计算更新时的基准状态
  firstBaseUpdate: Update<State> | null,               // 第一个待处理的基准更新，之前渲染剩下的，因为优先级不够
  lastBaseUpdate: Update<State> | null,                // 最后一个待处理的基准更新，之前渲染剩下的，因为优先级不够
  shared: SharedQueue<State>,                          // 共享队列，包含待处理的本次更新添加的新的更新
  effects: Array<Update<State>> | null,                // 存储产生副作用的更新数组，主要用于DevTools调试
|};

```