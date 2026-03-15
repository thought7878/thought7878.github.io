

## 源码
`packages/react-reconciler/src/ReactFiberHooks.old.js`

```ts
/**
 * Hook对象的结构定义，用于存储单个hook的状态和相关信息
 * 每个Hook对象代表一个函数组件中使用的一个useXxx hook实例
 */
export type Hook = {|
  /**
   * 存储当前渲染周期完成后的最终状态值
   * 这是组件下次渲染时将使用的状态
   */
  memoizedState: any,

  /**
   * 存储上一次渲染开始时的基础状态值
   * 用于确定在更新过程中哪些部分已经处理过
   */
  baseState: any,

  /**
   * 存储未完成的更新的队列，这些更新将在下一次渲染时被应用
   * 当前渲染周期中调度的更新会被暂存在这里
   */
  baseQueue: Update<any, any> | null,

  /**
   * 存储hook关联的更新队列（updateQueue）
   * 不同类型的hook可能有不同的队列结构
   */
  queue: any,

  /**
   * 链表中的下一个hook引用
   * 所有hook通过next属性形成一个链表结构，保存在fiber.memoizedState中
   */
  next: Hook | null,
|};

```


## memoizedState 与 baseState 的区别

### memoizedState

- **定义**：存储Hook的当前状态值，即*组件本次渲染时实际使用的状态*
- **作用**：代表*渲染完成后的最终状态*，也是*组件在本次渲染周期中实际访问的状态值*
- **更新时机**：*在每次渲染结束时更新，包含所有已完成的更新*

### baseState

- **定义**：基础状态，作为计算更新后*新状态的起始点*
- **作用**：用于在处理更新队列时*作为基准状态*，确保能够正确计算新状态
- **更新时机**：在处理更新队列时被用来*作为计算起点*，**只包含已处理的更新**

### 关键区别

1. **处理优先级更新**：
    - 当存在不同优先级的更新时，React可能*只处理一部分高优先级更新，低优先级更新会被推迟*
    - `memoizedState` 包含**所有已处理的更新（包括高优先级更新）**
    - `baseState` 是**计算下一轮更新的起始点，只包含已完成的更新**
2. **渲染一致性**：
    - memoizedState 确保在渲染期间组件看到的是完整的、一致的状态
    - baseState 用于计算下一次渲染时的起始状态
3. **更新队列处理**：
    - 在处理更新队列时，React从current.baseState开始计算新状态
    - 最终计算出的新状态会赋值给hook.memoizedState

举个例子：如果你有一个计数器，连续触发了多次更新，但*由于优先级原因某些更新被推迟了*。此时：
- `memoizedState` 会是*你能看到的当前状态（比如计数器显示的值）*
- `baseState` 会是*下次处理剩余更新时的起始点*

这种设计允许React实现可中断渲染和优先级调度，使得高优先级更新（如用户输入）可以立即响应，而低优先级更新（如UI背景更新）可以在空闲时处理。

## baseQueue 与 queue 的区别

### queue

- **定义**：*包含所有更新，包括新到达的更新*（**上次渲染后剩余的更新+本次新增的更新**）
- **组成**：
    - `pending`：指向最后一个入队的更新，其next指向第一个更新，为null时表示没有更新
    - `interleaved`：交错更新队列，用于支持时间片打断机制
    - `lanes`：当前队列中所有更新的lane（优先级）集合
    - `dispatch`：分发新更新的函数，对应dispatch函数
    - `lastRenderedReducer`：最后一次渲染时使用的reducer函数
    - `lastRenderedState`：最后一次渲染时的状态值

pending属性和interleaved属性的区别：[[queue]]

### baseQueue

- **定义**：基础队列，存储*当前渲染周期开始时已存在的更新*（**上次渲染后剩余的更新**）
- **作用**：作为本次渲染要处理的更新队列的基准，包含渲染开始时已知的待处理更新

### 关键区别

1. **处理优先级更新**：
    - `queue` 包含所有更新，包括新到达的更新（**上次渲染后剩余的更新+本次新增的更新**）
    - `baseQueue` 只包含当前渲染周期开始时已有的更新（**上次渲染后剩余的更新**）
2. **渲染过程中的更新处理**：
    - 当组件*正在渲染时*，如果*有新的更新*到达，这些更新会*先放在queue.pending中*
    - *渲染结束后，这些新到达的更新会合并到baseQueue中，供下次渲染使用*
3. **更新流程**：
    
```ts
// 在更新开始时：
let baseQueue = current.baseQueue;  // ！！！获取上次渲染后剩余的更新
const pendingQueue = queue.pending; // ！！！获取新到达的更新

if (pendingQueue !== null) {
  // ！！！将新更新合并到基础队列中（放到基础队列的末尾）
  if (baseQueue !== null) {
    const baseFirst = baseQueue.next;
    const pendingFirst = pendingQueue.next;
    baseQueue.next = pendingFirst;
    pendingQueue.next = baseFirst;
  }
  current.baseQueue = baseQueue = pendingQueue;
  queue.pending = null;  // 清空pending队列
}    
```
    
4. **并发渲染支持**：
    - baseQueue 保证了在渲染过程中有一致的更新集合可以处理
    - queue 的pending 属性接收*渲染过程中到达的新更新*
    - 这种设计允许React*在渲染一个组件时接收新的更新*，而不会影响当前渲染的一致性

这种机制是React并发渲染的关键部分，它允许React在渲染过程中接收新更新，同时保证当前渲染的结果是一致且可预测的。渲染完成后，未处理的更新和新接收的更新都会被适当地安排到下次渲染中。