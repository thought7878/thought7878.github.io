

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
   * 存储未完成的更新队列，这些更新将在下一次渲染时被应用
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