React调度更新的数据结构类型：
- **Update类型**：表示单个更新对象，包含更新优先级、动作、状态等信息
- **UpdateQueue类型**：表示更新队列，管理待处理更新、交错更新、调度函数等

这两个类型用于React内部状态更新机制，确保组件状态*按正确顺序和优先级进行更新*。

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
  /** 最后一次渲染时使用的reducer函数 */
  lastRenderedReducer: ((S, A) => S) | null,
  /** 最后一次渲染时的状态值 */
  lastRenderedState: S | null,
|};

```