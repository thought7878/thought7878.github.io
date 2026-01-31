# React 18.2.0 首次渲染完整详细调用流程

React 18.2.0的首次渲染流程是一个精心设计的多阶段过程，从应用入口到最终的DOM渲染完成，涉及数百个函数调用。以下是完整的详细调用流程。

## 1. 完整调用链总览

```
createRoot(container)
  ├─→ createRootImpl()
  ├─→ createContainer()
  ├─→ createFiberRoot()
  │     ├─→ new FiberRootNode()
  │     ├─→ createHostRootFiber()
  │     │     └─→ new FiberNode(HostRoot)
  │     ├─→ root.current = uninitializedFiber
  │     └─→ initializeUpdateQueue()
  └─→ new ReactDOMRoot(root)
        │
        └─→ root.render(<App/>)
              ├─→ updateContainer()
              │     ├─→ requestEventTime()
              │     ├─→ requestUpdateLane()
              │     ├─→ createUpdate()
              │     ├─→ enqueueUpdate()
              │     └─→ scheduleUpdateOnFiber()
              │           ├─→ checkForNestedUpdates()
              │           ├─→ markUpdateLaneFromFiberToRoot()
              │           ├─→ markRootUpdated()
              │           └─→ ensureRootIsScheduled()
              │                 ├─→ getNextLanes()
              │                 ├─→ getHighestPriorityLane()
              │                 └─→ scheduleCallback()
              │                       └─→ performConcurrentWorkOnRoot()
              │                             ├─→ renderRootConcurrent()
              │                             │     ├─→ prepareFreshStack()
              │                             │     │     ├─→ createWorkInProgress()
              │                             │     │     └─→ reset全局变量
              │                             │     ├─→ pushDispatcher()
              │                             │     └─→ workLoopConcurrent()
              │                             │           └─→ performUnitOfWork()
              │                             │                 ├─→ beginWork()
              │                             │                 │     ├─→ updateHostRoot()
              │                             │                 │     │     ├─→ processUpdateQueue()
              │                             │                 │     │     └─→ reconcileChildren()
              │                             │                 │     ├─→ updateFunctionComponent()
              │                             │                 │     │     ├─→ renderWithHooks()
              │                             │                 │     │     └─→ reconcileChildren()
              │                             │                 │     ├─→ updateHostComponent()
              │                             │                 │     │     └─→ reconcileChildren()
              │                             │                 │     └─→ updateHostText()
              │                             │                 │
              │                             │                 └─→ completeUnitOfWork()
              │                             │                       └─→ completeWork()
              │                             │                             ├─→ createInstance()
              │                             │                             ├─→ appendAllChildren()
              │                             │                             └─→ finalizeInitialChildren()
              │                             │
              │                             └─→ commitRoot()
              │                                   ├─→ commitRootImpl()
              │                                   │     ├─→ commitBeforeMutationEffects()
              │                                   │     │     └─→ commitBeforeMutationEffectOnFiber()
              │                                   │     │
              │                                   │     ├─→ commitMutationEffects()
              │                                   │     │     └─→ commitMutationEffectsOnFiber()
              │                                   │     │           ├─→ commitPlacement()
              │                                   │     │           ├─→ commitUpdate()
              │                                   │     │           └─→ commitAttachRef()
              │                                   │     │
              │                                   │     ├─→ root.current = finishedWork
              │                                   │     │
              │                                   │     └─→ commitLayoutEffects()
              │                                   │           └─→ commitLayoutEffectOnFiber()
              │                                   │                 ├─→ commitHookEffectListMount()
              │                                   │                 └─→ commitAttachRef()
              │                                   │
              │                                   └─→ scheduleCallback(NormalPriority, flushPassiveEffects)
              │                                         └─→ flushPassiveEffects()
              │                                               └─→ commitPassiveMountEffects()
              │                                                     └─→ commitHookEffectListMount()
              │
              └─→ 渲染完成
```

## 2. 阶段一：创建根节点

### 2.1 应用入口

```javascript
// 应用代码
import { createRoot } from 'react-dom/client';

const container = document.getElementById('root');
const root = createRoot(container);  // 第一步：创建根节点
root.render(<App />);                // 第二步：触发渲染
```

### 2.2 createRoot 函数

**源码位置**：`react-dom/src/client/ReactDOM.js`

```javascript
export function createRoot(
  container: Element | DocumentFragment,
  options?: CreateRootOptions,
): RootType {
  // 1. 验证容器
  if (__DEV__) {
    warnIfReactDOMContainerInDEV(container);
  }
  
  // 2. 验证容器有效性
  if (!isValidContainer(container)) {
    throw new Error('Target container is not a DOM element.');
  }
  
  // 3. 处理配置选项
  let isStrictMode = false;
  let concurrentUpdatesByDefaultOverride = null;
  
  if (options != null) {
    if (options.unstable_strictMode === true) {
      isStrictMode = true;
    }
    if (options.unstable_concurrentUpdatesByDefault === true) {
      concurrentUpdatesByDefaultOverride = true;
    }
  }
  
  // 4. 调用内部实现
  return createRootImpl(
    container,
    isStrictMode,
    concurrentUpdatesByDefaultOverride,
  );
}
```

**功能**：
- 验证DOM容器的有效性
- 处理严格模式配置
- 处理并发更新默认行为
- 调用`createRootImpl`创建根节点

---

### 2.3 createRootImpl 函数

**源码位置**：`react-dom/src/client/ReactDOMRoot.js`

```javascript
function createRootImpl(
  container: Container,
  isStrictMode: boolean,
  concurrentUpdatesByDefaultOverride: null | boolean,
) {
  // 1. 创建Fiber容器
  const root = createContainer(
    container,
    ConcurrentRoot,
    null,
    isStrictMode,
    concurrentUpdatesByDefaultOverride,
  );
  
  // 2. 标记容器为React根节点
  markContainerAsRoot(root.current, container);
  
  // 3. 处理hydrate特殊情况
  if (container.nodeType === COMMENT_NODE) {
    console.error('You are using createRoot() on a comment node.');
  }
  
  // 4. 返回ReactDOMRoot实例
  return new ReactDOMRoot(root);
}
```

**功能**：
- 创建Fiber根节点
- 标记DOM容器
- 处理特殊情况
- 返回封装的根对象

---

### 2.4 createContainer 函数

**源码位置**：`react-reconciler/src/ReactFiberReconciler.new.js`

```javascript
export function createContainer(
  containerInfo: Container,
  tag: RootTag,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
  isStrictMode: boolean,
  concurrentUpdatesByDefaultOverride: null | boolean,
  identifierPrefix: string = '',
  onRecoverableError: (error: mixed) => void = defaultOnRecoverableError,
  formState: any = null,
): OpaqueRoot {
  return createFiberRoot(
    containerInfo,
    tag,
    hydrationCallbacks,
    isStrictMode,
    concurrentUpdatesByDefaultOverride,
    identifierPrefix,
    onRecoverableError,
    formState,
  );
}
```

**功能**：
- 封装`createFiberRoot`调用
- 传递配置参数
- 返回Fiber根节点

---

### 2.5 createFiberRoot 函数

**源码位置**：`react-reconciler/src/ReactFiberRoot.new.js`

```javascript
export function createFiberRoot(
  containerInfo: any,
  tag: RootTag,
  hydrationCallbacks: null | SuspenseHydrationCallbacks,
  isStrictMode: boolean,
  concurrentUpdatesByDefaultOverride: null | boolean,
  identifierPrefix: string,
  onRecoverableError: (error: mixed) => void,
  formState: any,
): FiberRoot {
  // 1. 创建FiberRootNode实例
  const root = new FiberRootNode(containerInfo, tag, hydrationCallbacks);
  
  // 2. 配置错误处理
  root.onRecoverableError = onRecoverableError;
  root.identifierPrefix = identifierPrefix;
  
  // 3. 创建根Fiber节点
  const uninitializedFiber = createHostRootFiber(
    tag,
    isStrictMode,
    concurrentUpdatesByDefaultOverride,
  );
  
  // 4. 建立双向引用
  root.current = uninitializedFiber;
  uninitializedFiber.stateNode = root;
  
  // 5. 初始化更新队列
  initializeUpdateQueue(uninitializedFiber);
  
  // 6. 初始化表单状态
  root.formState = formState;
  root.formStateIsMounted = formState !== null;
  
  return root;
}
```

**功能**：
- 创建FiberRootNode
- 创建HostRoot类型的Fiber节点
- 建立双向引用
- 初始化更新队列
- 配置各种特性

---

### 2.6 FiberRootNode 构造函数

**源码位置**：`react-reconciler/src/ReactFiberRoot.new.js`

```javascript
function FiberRootNode(containerInfo, tag, hydrate) {
  // 基础属性
  this.tag = tag;
  this.containerInfo = containerInfo;
  this.pendingChildren = null;
  this.current = null;
  
  // 渲染状态
  this.pingCache = null;
  this.finishedWork = null;
  this.timeoutHandle = noTimeout;
  this.context = null;
  this.pendingContext = null;
  this.callbackNode = null;
  this.callbackPriority = NoLane;
  
  // 车道模型
  this.eventTimes = createLaneMap(NoLanes);
  this.expirationTimes = createLaneMap(NoTimestamp);
  
  // 更新优先级
  this.pendingLanes = NoLanes;
  this.suspendedLanes = NoLanes;
  this.pingedLanes = NoLanes;
  this.expiredLanes = NoLanes;
  this.mutableReadLanes = NoLanes;
  this.finishedLanes = NoLanes;
  
  // 调度管理
  this.entangledLanes = NoLanes;
  this.entanglements = createLaneMap(NoLanes);
  
  // 水合信息
  this.identifierPrefix = '';
  this.onRecoverableError = defaultOnRecoverableError;
  
  // 表单状态
  this.formState = null;
  this.formStateIsMounted = false;
}
```

**功能**：
- 初始化Fiber根节点的所有属性
- 配置车道模型
- 设置调度相关属性
- 为各种特性做准备

---

### 2.7 createHostRootFiber 函数

**源码位置**：`react-reconciler/src/ReactFiber.new.js`

```javascript
export function createHostRootFiber(
  tag: RootTag,
  isStrictMode: boolean,
  concurrentUpdatesByDefaultOverride: null | boolean,
): Fiber {
  // 1. 创建Fiber节点
  const fiber = createFiber(HostRoot, null, null, DefaultMode);
  
  // 2. 设置模式标志
  fiber.mode = ConcurrentMode | StrictLegacyMode;
  
  // 3. 应用严格效果模式
  if (enableStrictEffects) {
    fiber.mode |= StrictEffectsMode;
  }
  
  // 4. 处理严格模式
  if (isStrictMode) {
    fiber.mode |= StrictLegacyMode;
    if (enableStrictEffects) {
      fiber.mode |= StrictEffectsMode;
    }
  }
  
  // 5. 处理并发更新覆盖
  if (concurrentUpdatesByDefaultOverride !== null) {
    fiber.mode |= ConcurrentUpdatesByDefaultMode;
  }
  
  return fiber;
}
```

**功能**：
- 创建HostRoot类型的Fiber节点
- 设置并发模式标志
- 配置严格模式
- 初始化Fiber结构

---

### 2.8 FiberNode 构造函数

**源码位置**：`react-reconciler/src/ReactFiber.new.js`

```javascript
function FiberNode(
  tag: WorkTag,
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode,
) {
  // 实例标识
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null;
  
  // Fiber树连接
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;
  
  // 状态管理
  this.ref = null;
  this.pendingProps = pendingProps;
  this.memoizedProps = null;
  this.memoizedState = null;
  this.dependencies = null;
  
  // 副作用管理
  this.flags = NoFlags;
  this.subtreeFlags = NoFlags;
  this.deletions = null;
  
  // 优先级管理
  this.lanes = NoLanes;
  this.childLanes = NoLanes;
  
  // 更新队列
  this.updateQueue = null;
  
  // 双缓冲指针
  this.alternate = null;
  
  // 模式标志
  this.mode = mode;
}
```

**功能**：
- 初始化Fiber节点的核心数据结构
- 建立Fiber树的连接关系
- 配置状态和props管理
- 设置副作用标记系统

---

### 2.9 initializeUpdateQueue 函数

**源码位置**：`react-reconciler/src/ReactUpdateQueue.new.js`

```javascript
export function initializeUpdateQueue<State>(fiber: Fiber): void {
  const queue: UpdateQueue<State> = {
    baseState: fiber.memoizedState,
    firstBaseUpdate: null,
    lastBaseUpdate: null,
    shared: {
      pending: null,
      interleaved: null,
      lanes: NoLanes,
    },
    effects: null,
  };
  
  fiber.updateQueue = queue;
}
```

**功能**：
- 创建空的更新队列
- 初始化队列结构
- 关联到Fiber节点

---

### 2.10 ReactDOMRoot 构造函数

**源码位置**：`react-dom/src/client/ReactDOMRoot.js`

```javascript
function ReactDOMRoot(internalRoot) {
  this._internalRoot = internalRoot;
}

ReactDOMRoot.prototype.render = function(children) {
  const root = this._internalRoot;
  if (root === null) {
    throw new Error('Cannot update an unmounted root.');
  }
  
  updateContainer(children, root, null, null);
};

ReactDOMRoot.prototype.unmount = function() {
  const root = this._internalRoot;
  if (root !== null) {
    this._internalRoot = null;
    const container = root.containerInfo;
    updateContainer(null, root, null, () => {
      unmarkContainerAsRoot(container);
    });
  }
};
```

**功能**：
- 封装内部根节点
- 提供render API
- 提供unmount API
- 与updateContainer集成

---

## 3. 阶段二：触发渲染

### 3.1 root.render() 调用

```javascript
root.render(<App />);
```

这会调用`ReactDOMRoot.prototype.render`，进而调用`updateContainer`。

---

### 3.2 updateContainer 函数

**源码位置**：`react-reconciler/src/ReactFiberReconciler.new.js`

```javascript
export function updateContainer(
  element: ReactNodeList,
  container: OpaqueRoot,
  parentComponent: ?React$Component<any, any>,
  callback: ?Function,
): Lane {
  // 1. 获取当前Fiber
  const current = container.current;
  
  // 2. 获取事件时间
  const eventTime = requestEventTime();
  
  // 3. 确定更新优先级
  const lane = requestUpdateLane(current);
  
  // 4. 创建更新对象
  const update = createUpdate(eventTime, lane);
  
  // 5. 设置更新内容
  update.payload = { element };
  
  // 6. 设置回调
  if (callback !== null && callback !== undefined) {
    update.callback = callback;
  }
  
  // 7. 将更新入队
  enqueueUpdate(current, update, lane);
  
  // 8. 调度更新
  const root = scheduleUpdateOnFiber(current, lane, eventTime);
  
  // 9. 处理同步更新
  if (lane === SyncLane) {
    flushSyncCallbacks();
  }
  
  return lane;
}
```

**功能**：
- 创建表示渲染的Update对象
- 设置payload为`{ element: <App/> }`
- 将更新添加到根Fiber的更新队列
- 启动调度流程

---

### 3.3 requestEventTime 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
export function requestEventTime() {
  if ((executionContext & (RenderContext | CommitContext)) !== NoContext) {
    // 在渲染/提交阶段内，使用当前时间
    return now();
  }
  
  if (currentEventTime !== NoTimestamp) {
    // 返回已缓存的事件时间
    return currentEventTime;
  }
  
  // 获取当前时间戳
  currentEventTime = now();
  return currentEventTime;
}
```

**功能**：
- 获取一致的事件时间戳
- 在同一事件循环内共享相同时间
- 为优先级计算提供基础

---

### 3.4 requestUpdateLane 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
export function requestUpdateLane(fiber: Fiber): Lane {
  // 1. 检查是否处于过渡期
  const isTransition = ReactCurrentBatchConfig.transition !== null;
  
  // 2. 检查是否在渲染阶段
  const isRenderPhaseUpdate = (executionContext & RenderContext) !== NoContext;
  
  // 3. 处理过渡更新
  if (isTransition) {
    return requestTransitionLane();
  }
  
  // 4. 处理渲染阶段更新
  if (isRenderPhaseUpdate) {
    return SyncLane;
  }
  
  // 5. 处理离散事件
  if ((executionContext & DiscreteEventContext) !== NoContext) {
    return findUpdateLane(InputDiscreteLanePriority, currentEventWipLanes);
  }
  
  // 6. 处理默认上下文或传统模式
  if (
    (executionContext & DefaultContext) !== NoContext ||
    (fiber.mode & ConcurrentMode) === NoMode
  ) {
    return SyncLane;
  }
  
  // 7. 并发模式下的默认更新（首次渲染走这里）
  return findUpdateLane(DefaultLanePriority, currentEventWipLanes);
}
```

**返回值**：`DefaultLane` (0b100)

**功能**：
- 根据执行上下文确定更新优先级
- 首次渲染通常返回DefaultLane
- 支持不同交互类型的优先级

---

### 3.5 createUpdate 函数

**源码位置**：`react-reconciler/src/ReactUpdateQueue.new.js`

```javascript
export function createUpdate(eventTime: number, lane: Lane): Update<*> {
  const update: Update<*> = {
    eventTime,
    lane,
    
    tag: UpdateState,
    payload: null,
    callback: null,
    
    next: null,
  };
  
  return update;
}
```

**创建的Update对象**：
```javascript
{
  eventTime: 0,
  lane: 0b100, // DefaultLane
  tag: 0, // UpdateState
  payload: null, // 稍后设置
  callback: null,
  next: null
}
```

---

### 3.6 enqueueUpdate 函数

**源码位置**：`react-reconciler/src/ReactUpdateQueue.new.js`

```javascript
export function enqueueUpdate<State>(
  fiber: Fiber,
  update: Update<State>,
  lane: Lane,
) {
  // 1. 获取更新队列
  const updateQueue = fiber.updateQueue;
  if (updateQueue === null) {
    return;
  }
  
  // 2. 获取共享队列
  const sharedQueue: SharedQueue<State> = (updateQueue: any).shared;
  const pending = sharedQueue.pending;
  
  // 3. 将更新插入链表
  if (pending === null) {
    // 首个更新，创建循环链表
    update.next = update;
  } else {
    // 插入到链表末尾
    update.next = pending.next;
    pending.next = update;
  }
  
  // 4. 更新pending指针
  sharedQueue.pending = update;
  
  // 5. 标记工作
  if (fiber.lanes === NoLanes) {
    fiber.lanes = lane;
  } else {
    fiber.lanes = mergeLanes(fiber.lanes, lane);
  }
}
```

**功能**：
- 将Update添加到Fiber的更新队列
- 维护循环链表结构
- 更新Fiber的lanes标记

---

## 4. 阶段三：调度更新

### 4.1 scheduleUpdateOnFiber 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
export function scheduleUpdateOnFiber(
  fiber: Fiber,
  lane: Lane,
  eventTime: number,
): FiberRoot | null {
  // 1. 检查嵌套更新深度
  checkForNestedUpdates();
  
  // 2. 从当前fiber向上标记到根节点
  const root = markUpdateLaneFromFiberToRoot(fiber, lane);
  if (root === null) {
    return null;
  }
  
  // 3. 标记根节点有更新
  markRootUpdated(root, lane, eventTime);
  
  // 4. 确保根节点被调度
  ensureRootIsScheduled(root, eventTime);
  
  // 5. 处理同步更新特殊情况
  if (
    lane === SyncLane ||
    enableLegacySyncScheduling ||
    (executionContext & (RenderContext | CommitContext)) !== NoContext
  ) {
    if (root.tag === LegacyRoot) {
      flushSyncCallbacks();
    }
  }
  
  return root;
}
```

---

### 4.2 checkForNestedUpdates 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function checkForNestedUpdates() {
  if (nestedUpdateCount > NESTED_UPDATE_LIMIT) {
    nestedUpdateCount = 0;
    rootWithNestedUpdates = null;
    throw new Error(
      'Maximum update depth exceeded. This can happen when a component ' +
      'repeatedly calls setState inside componentWillUpdate or ' +
      'componentDidUpdate. React limits the number of nested updates to ' +
      'prevent infinite loops.',
    );
  }
  
  if (__DEV__) {
    if (nestedPassiveUpdateCount > NESTED_PASSIVE_UPDATE_LIMIT) {
      nestedPassiveUpdateCount = 0;
      console.error(
        'Maximum update depth exceeded. This can happen when a component ' +
        'calls setState inside useEffect, but useEffect either doesn\'t ' +
        'have a dependency array, or one of the dependencies changes on ' +
        'every render.',
      );
    }
  }
}
```

**功能**：
- 防止无限循环更新
- 限制嵌套更新深度（默认50层）
- 提供有用的错误信息

---

### 4.3 markUpdateLaneFromFiberToRoot 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function markUpdateLaneFromFiberToRoot(
  sourceFiber: Fiber,
  lane: Lane,
): FiberRoot | null {
  // 从当前fiber向上遍历到根节点
  let node = sourceFiber;
  let parent = node.return;
  
  while (parent !== null) {
    const isFiberSuspense = parent.tag === SuspenseComponent;
    if (!isFiberSuspense || parent.memoizedState === null) {
      // 更新子树优先级
      parent.childLanes = mergeLanes(parent.childLanes, lane);
    }
    node = parent;
    parent = node.return;
  }
  
  // 返回FiberRoot
  if (node.tag === HostRoot) {
    const root: FiberRoot = node.stateNode;
    return root;
  } else {
    return null;
  }
}
```

**功能**：
- 向上遍历Fiber树到根节点
- 为路径上的每个Fiber更新childLanes
- 处理Suspense边界
- 返回FiberRoot对象

---

### 4.4 markRootUpdated 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function markRootUpdated(
  root: FiberRoot,
  updateLane: Lane,
  eventTime: number,
) {
  // 1. 将更新标记到根节点
  root.pendingLanes = mergeLanes(root.pendingLanes, updateLane);
  
  // 2. 更新事件时间
  root.eventTimes = updateEventTimes(root.eventTimes, updateLane, eventTime);
  
  // 3. 标记过期的更新
  markStarvedLanesAsExpired(root, eventTime);
}
```

**功能**：
- 合并新的更新优先级到pendingLanes
- 记录事件时间戳
- 处理过期更新

---

### 4.5 ensureRootIsScheduled 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function ensureRootIsScheduled(root: FiberRoot, currentTime: number) {
  // 1. 取消已存在的回调
  const existingCallbackNode = root.callbackNode;
  if (existingCallbackNode !== null) {
    cancelCallback(existingCallbackNode);
  }
  
  // 2. 计算下一个要处理的优先级
  let nextLanes = getNextLanes(
    root,
    root === workInProgressRoot ? workInProgressRootRenderLanes : NoLanes,
  );
  
  // 3. 无更新需要处理
  if (nextLanes === NoLanes) {
    root.callbackNode = null;
    root.callbackPriority = NoLane;
    return;
  }
  
  // 4. 获取最高优先级
  let newCallbackPriority = getHighestPriorityLane(nextLanes);
  
  // 5. 将React优先级映射到Scheduler优先级
  let schedulerPriorityLevel;
  const eventPriority = lanesToEventPriority(nextLanes);
  
  switch (eventPriority) {
    case DiscreteEventPriority:
      schedulerPriorityLevel = ImmediatePriority;
      break;
    case ContinuousEventPriority:
      schedulerPriorityLevel = UserBlockingPriority;
      break;
    case DefaultEventPriority:
      schedulerPriorityLevel = NormalPriority;
      break;
    case IdleEventPriority:
      schedulerPriorityLevel = IdlePriority;
      break;
    default:
      schedulerPriorityLevel = NormalPriority;
  }
  
  // 6. 创建调度回调
  let newCallbackNode;
  if (newCallbackPriority === SyncLane) {
    // 同步更新
    newCallbackNode = scheduleSyncCallback(
      performSyncWorkOnRoot.bind(null, root),
    );
  } else {
    // 并发更新（首次渲染走这里）
    newCallbackNode = scheduleCallback(
      schedulerPriorityLevel,
      performConcurrentWorkOnRoot.bind(null, root),
    );
  }
  
  // 7. 更新根节点调度状态
  root.callbackPriority = newCallbackPriority;
  root.callbackNode = newCallbackNode;
}
```

**功能**：
- 取消已存在的调度回调
- 计算下一个要处理的更新优先级
- 将React优先级映射到Scheduler优先级
- 创建并注册新的调度回调
- 更新根节点调度状态

---

### 4.6 getNextLanes 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
export function getNextLanes(root: FiberRoot, wipLanes: Lanes): Lanes {
  const pendingLanes = root.pendingLanes;
  
  if (pendingLanes === NoLanes) {
    return NoLanes;
  }
  
  let nextLanes = NoLanes;
  
  // 1. 优先处理过期的更新
  const expiredLanes = root.expiredLanes;
  if (expiredLanes !== NoLanes) {
    nextLanes = expiredLanes;
    return nextLanes;
  }
  
  // 2. 获取最高优先级的pending lanes
  nextLanes = getHighestPriorityLanes(pendingLanes);
  
  return nextLanes;
}
```

**返回值**：`DefaultLane` (0b100)

---

### 4.7 scheduleCallback 函数

**源码位置**：`react-reconciler/src/Scheduler.js`

```javascript
export function scheduleCallback(
  reactPriorityLevel: ReactPriorityLevel,
  callback: SchedulerCallback,
  options?: SchedulerCallbackOptions,
) {
  // 1. 将React优先级转换为Scheduler优先级
  const priorityLevel = reactPriorityToSchedulerPriority(reactPriorityLevel);
  
  // 2. 通过Scheduler包调度回调
  return Scheduler_scheduleCallback(priorityLevel, callback, options);
}
```

**功能**：
- 优先级映射转换
- 代理到Scheduler包
- 为React与Scheduler提供适配层

---

## 5. 阶段四：执行渲染工作

### 5.1 performConcurrentWorkOnRoot 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function performConcurrentWorkOnRoot(root: FiberRoot, didTimeout: boolean) {
  // 1. 确保执行上下文正确
  const originalCallbackNode = root.callbackNode;
  
  // 2. 获取下一个要处理的优先级
  const lanes = getNextLanes(
    root,
    root === workInProgressRoot ? workInProgressRootRenderLanes : NoLanes,
  );
  
  // 3. 无更新需要处理
  if (lanes === NoLanes) {
    return null;
  }
  
  // 4. 准备渲染上下文
  const exitStatus = renderRootConcurrent(root, lanes);
  
  // 5. 处理不同渲染结果
  if (exitStatus !== RootInProgress) {
    if (exitStatus === RootErrored) {
      // 错误处理
      return handleRootError(root, lanes);
    } else if (exitStatus === RootSuspended) {
      // Suspense挂起处理
      return handleRootSuspended(root, lanes);
    } else if (exitStatus === RootCompleted) {
      // 渲染完成，准备提交
      const finishedWork = root.current.alternate;
      root.finishedWork = finishedWork;
      root.finishedLanes = lanes;
      
      // 提交阶段
      commitRoot(root);
      
      // 确保根节点被重新调度（如果有剩余工作）
      ensureRootIsScheduled(root, now());
      
      return null;
    }
  }
  
  // 6. 需要更多时间，返回自身继续调度
  return performConcurrentWorkOnRoot.bind(null, root);
}
```

**功能**：
- 执行并发模式下的渲染工作
- 处理渲染中断和恢复
- 管理Suspense边界
- 决定是否需要提交或继续调度

---

### 5.2 renderRootConcurrent 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function renderRootConcurrent(root: FiberRoot, lanes: Lanes) {
  // 1. 检查是否需要重新开始
  if (workInProgressRoot !== root || workInProgressRootRenderLanes !== lanes) {
    // 初始化或重新开始渲染
    prepareFreshStack(root, lanes);
  }
  
  // 2. 全局上下文设置
  const prevExecutionContext = executionContext;
  executionContext |= RenderContext;
  const prevDispatcher = pushDispatcher();
  
  // 3. 执行工作循环（可中断）
  workLoopConcurrent();
  
  // 4. 恢复上下文
  popDispatcher(prevDispatcher);
  executionContext = prevExecutionContext;
  
  // 5. 检查完成状态
  if (workInProgress !== null) {
    // 被中断，未完成
    return RootInProgress;
  } else {
    // 完成渲染
    return workInProgressRootExitStatus;
  }
}
```

**功能**：
- 初始化或重置渲染环境
- 设置执行上下文和调度器Dispatcher
- 启动可中断的工作循环
- 处理上下文恢复
- 返回渲染状态

---

### 5.3 prepareFreshStack 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function prepareFreshStack(root: FiberRoot, lanes: Lanes) {
  // 1. 重置全局变量
  workInProgress = root.current.alternate;
  if (workInProgress === null) {
    // 初次渲染，创建workInProgress树
    workInProgress = createWorkInProgress(root.current, null);
  }
  
  // 2. 重置工作循环状态
  workInProgressRoot = root;
  workInProgressRootRenderLanes = lanes;
  
  // 3. 重置副作用列表
  workInProgressRootExitStatus = RootInProgress;
  workInProgressRootSkippedLanes = NoLanes;
  workInProgressRootPingedLanes = NoLanes;
  workInProgressRootConcurrentErrors = null;
  workInProgressRootRecoverableErrors = null;
  
  // 4. 重置全局指针
  subtreeRenderLanes = NoLanes;
  renderLanes = lanes;
  
  // 5. 重置effect链表
  workInProgressRootIncludedLanes = NoLanes;
  
  // 6. 初始化调度状态
  ensureRootIsScheduled(root, now());
}
```

**功能**：
- 创建或重用workInProgress树
- 重置全局工作状态
- 初始化副作用收集
- 设置渲染优先级

---

### 5.4 createWorkInProgress 函数

**源码位置**：`react-reconciler/src/ReactFiber.new.js`

```javascript
export function createWorkInProgress(current: Fiber, pendingProps: any): Fiber {
  let workInProgress = current.alternate;
  
  if (workInProgress === null) {
    // 创建新的workInProgress
    workInProgress = createFiber(
      current.tag,
      pendingProps !== null ? pendingProps : current.pendingProps,
      current.key,
      current.mode,
    );
    
    workInProgress.elementType = current.elementType;
    workInProgress.type = current.type;
    workInProgress.stateNode = current.stateNode;
    
    // 建立双缓冲引用
    workInProgress.alternate = current;
    current.alternate = workInProgress;
  } else {
    // 复用已存在的workInProgress
    workInProgress.pendingProps = pendingProps !== null ? pendingProps : current.pendingProps;
    workInProgress.childLanes = current.childLanes;
    workInProgress.lanes = current.lanes;
  }
  
  // 复制其他属性
  workInProgress.flags = NoFlags;
  workInProgress.subtreeFlags = NoFlags;
  workInProgress.deletions = null;
  workInProgress.child = current.child;
  workInProgress.memoizedProps = current.memoizedProps;
  workInProgress.memoizedState = current.memoizedState;
  workInProgress.updateQueue = current.updateQueue;
  workInProgress.dependencies = current.dependencies;
  
  return workInProgress;
}
```

**功能**：
- 创建或复用workInProgress Fiber
- 建立双缓冲架构的alternate引用
- 复制必要的属性
- 重置副作用标记

---

### 5.5 workLoopConcurrent 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function workLoopConcurrent() {
  // 可中断的工作循环
  while (workInProgress !== null && !shouldYield()) {
    performUnitOfWork(workInProgress);
  }
}
```

**功能**：
- 实现协作式调度的核心循环
- 检查`shouldYield()`决定是否让出主线程
- 持续处理工作单元直到完成或需要中断

---

### 5.6 shouldYield 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function shouldYield() {
  return Scheduler_shouldYield();
}
```

**功能**：
- 委托给Scheduler判断是否应该让出
- 基于时间片（通常5ms）决定
- 允许浏览器处理高优先级任务

---

## 6. 阶段五：处理工作单元（Render阶段）

### 6.1 performUnitOfWork 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function performUnitOfWork(unitOfWork: Fiber): void {
  // 1. 获取当前Fiber的alternate
  const current = unitOfWork.alternate;
  let next;
  
  // 2. 处理Fiber节点（调用beginWork）
  next = beginWork(current, unitOfWork, subtreeRenderLanes);
  
  // 3. 清除过期的props
  unitOfWork.memoizedProps = unitOfWork.pendingProps;
  
  // 4. 检查是否需要继续处理子节点
  if (next === null) {
    // 没有子节点，完成当前单元
    completeUnitOfWork(unitOfWork);
  } else {
    // 有子节点，继续处理
    workInProgress = next;
  }
}
```

**功能**：
- 处理单个工作单元(Fiber节点)
- 调用`beginWork`处理当前节点
- 根据结果决定是继续向下还是向上完成
- 管理Fiber树的遍历流程

---

### 6.2 beginWork 函数

**源码位置**：`react-reconciler/src/ReactFiberBeginWork.new.js`

```javascript
function beginWork(
  current: Fiber | null,
  workInProgress: Fiber,
  renderLanes: Lanes,
): Fiber | null {
  // 1. 检查是否需要更新
  if (current !== null) {
    const oldProps = current.memoizedProps;
    const newProps = workInProgress.pendingProps;
    
    if (
      oldProps !== newProps ||
      hasLegacyContextChanged() ||
      (__DEV__ ? workInProgress.type !== current.type : false)
    ) {
      // props或context变化，需要更新
      didReceiveUpdate = true;
    } else if (!includesSomeLane(renderLanes, workInProgress.lanes)) {
      // 无需更新，尝试bailout优化
      didReceiveUpdate = false;
      return bailoutOnAlreadyFinishedWork(current, workInProgress, renderLanes);
    } else {
      didReceiveUpdate = false;
    }
  } else {
    // 初次渲染，需要更新
    didReceiveUpdate = true;
  }
  
  // 2. 根据Fiber类型分发处理
  switch (workInProgress.tag) {
    case IndeterminateComponent:
      return mountIndeterminateComponent(current, workInProgress, workInProgress.type, renderLanes);
    case FunctionComponent:
      return updateFunctionComponent(current, workInProgress, workInProgress.type, workInProgress.pendingProps, renderLanes);
    case ClassComponent:
      return updateClassComponent(current, workInProgress, workInProgress.type, workInProgress.pendingProps, renderLanes);
    case HostRoot:
      return updateHostRoot(current, workInProgress, renderLanes);
    case HostComponent:
      return updateHostComponent(current, workInProgress, renderLanes);
    case HostText:
      return updateHostText(current, workInProgress);
    case SuspenseComponent:
      return updateSuspenseComponent(current, workInProgress, renderLanes);
    // ...其他类型
    default:
      throw new Error('Unknown unit of work tag');
  }
}
```

**功能**：
- 决定是否需要更新当前Fiber
- 处理bailout（复用已完成工作的优化）
- 根据Fiber类型分发到具体更新函数
- 管理组件更新生命周期

---

### 6.3 updateHostRoot 函数（首次渲染的关键）

**源码位置**：`react-reconciler/src/ReactFiberBeginWork.new.js`

```javascript
function updateHostRoot(
  current: Fiber | null,
  workInProgress: Fiber,
  renderLanes: Lanes,
) {
  pushHostRootContext(workInProgress);
  
  // 1. 处理更新队列
  const updateQueue = workInProgress.updateQueue;
  const nextProps = workInProgress.pendingProps;
  const prevState = workInProgress.memoizedState;
  
  // 2. 处理初始状态
  if (updateQueue !== null) {
    // 3. 计算新状态
    const nextState = processUpdateQueue(
      workInProgress,
      nextProps,
      null,
      renderLanes,
    );
    
    // 4. 检查状态是否变化
    if (prevState === nextState) {
      // 状态未变，尝试bailout
      resetHydrationState();
      return bailoutOnAlreadyFinishedWork(current, workInProgress, renderLanes);
    }
    
    // 5. 更新memoizedState
    workInProgress.memoizedState = nextState;
  }
  
  // 6. 获取下一个子节点（首次渲染：{ element: <App/> }）
  const nextChildren = workInProgress.memoizedState.element;
  const root: FiberRoot = workInProgress.stateNode;
  
  // 7. 处理水合
  if (root.hydrate) {
    // 服务端渲染水合
    attemptHydrationAtCurrentPriority();
  }
  
  // 8. 协调子节点（首次渲染：挂载<App/>）
  reconcileChildren(current, workInProgress, nextChildren, renderLanes);
  
  return workInProgress.child;
}
```

**功能**：
- 处理根节点特殊上下文
- 从更新队列中提取初始元素(`<App/>`)
- 协调子节点，启动整个应用的协调

---

### 6.4 processUpdateQueue 函数

**源码位置**：`react-reconciler/src/ReactUpdateQueue.new.js`

```javascript
export function processUpdateQueue<State>(
  workInProgress: Fiber,
  props: any,
  instance: any,
  renderLanes: Lanes,
): void {
  // 1. 获取更新队列
  const queue: UpdateQueue<State> = (workInProgress.updateQueue: any);
  
  // 2. 获取pending更新
  let pendingQueue = queue.shared.pending;
  
  if (pendingQueue !== null) {
    // 3. 清空pending队列
    queue.shared.pending = null;
    
    // 4. 合并环形链表
    const lastPendingUpdate = pendingQueue;
    const firstPendingUpdate = lastPendingUpdate.next;
    lastPendingUpdate.next = null;
    
    // 5. 处理每个更新
    let update = firstPendingUpdate;
    let newState = workInProgress.memoizedState;
    
    while (update !== null) {
      const updateLane = update.lane;
      
      if (!isSubsetOfLanes(renderLanes, updateLane)) {
        // 优先级不匹配，跳过
        const clone = {
          eventTime: update.eventTime,
          lane: updateLane,
          tag: update.tag,
          payload: update.payload,
          callback: update.callback,
          next: null,
        };
        // 保存到baseQueue
      } else {
        // 处理更新
        newState = getStateFromUpdate(
          workInProgress,
          queue,
          update,
          newState,
          props,
          instance,
        );
      }
      
      update = update.next;
    }
    
    // 6. 更新memoizedState
    workInProgress.memoizedState = newState;
  }
}
```

**功能**：
- 处理更新队列中的所有更新
- 根据优先级过滤更新
- 计算新的状态
- 更新memoizedState

---

### 6.5 getStateFromUpdate 函数

**源码位置**：`react-reconciler/src/ReactUpdateQueue.new.js`

```javascript
function getStateFromUpdate<State>(
  workInProgress: Fiber,
  queue: UpdateQueue<State>,
  update: Update<State>,
  prevState: State,
  nextProps: any,
  instance: any,
): any {
  switch (update.tag) {
    case UpdateState: {
      const payload = update.payload;
      let partialState;
      
      if (typeof payload === 'function') {
        // 函数式更新
        partialState = payload.call(instance, prevState, nextProps);
      } else {
        // 直接值更新（首次渲染走这里）
        partialState = payload;
      }
      
      if (partialState === null || partialState === undefined) {
        return prevState;
      }
      
      // 合并状态
      return Object.assign({}, prevState, partialState);
    }
    // ...其他tag处理
  }
  
  return prevState;
}
```

**首次渲染返回**：
```javascript
{
  element: <App />
}
```

---

### 6.6 reconcileChildren 函数

**源码位置**：`react-reconciler/src/ReactChildFiber.new.js`

```javascript
export function reconcileChildren(
  current: Fiber | null,
  workInProgress: Fiber,
  nextChildren: any,
  renderLanes: Lanes,
) {
  if (current === null) {
    // 初次渲染，挂载子节点
    workInProgress.child = mountChildFibers(
      workInProgress,
      null,
      nextChildren,
      renderLanes,
    );
  } else {
    // 更新，协调子节点
    workInProgress.child = reconcileChildFibers(
      workInProgress,
      current.child,
      nextChildren,
      renderLanes,
    );
  }
}
```

**功能**：
- 决定使用mount还是update逻辑
- 调用diff算法
- 构建子Fiber链表

---

### 6.7 mountChildFibers 函数

**源码位置**：`react-reconciler/src/ReactChildFiber.new.js`

```javascript
const mountChildFibers = ChildReconciler(false);

function ChildReconciler(shouldTrackSideEffects) {
  function reconcileChildFibers(
    returnFiber: Fiber,
    currentFirstChild: Fiber | null,
    newChild: any,
    lanes: Lanes,
  ): Fiber | null {
    // 1. 处理文本节点
    if (typeof newChild === 'string' || typeof newChild === 'number') {
      return reconcileSingleTextNode(
        returnFiber,
        currentFirstChild,
        '' + newChild,
        lanes,
      );
    }
    
    // 2. 处理单个React元素
    if (typeof newChild === 'object' && newChild !== null) {
      switch (newChild.$$typeof) {
        case REACT_ELEMENT_TYPE:
          return reconcileSingleElement(
            returnFiber,
            currentFirstChild,
            newChild,
            lanes,
          );
        case REACT_PORTAL_TYPE:
          return reconcileSinglePortal(
            returnFiber,
            currentFirstChild,
            newChild,
            lanes,
          );
      }
    }
    
    // 3. 处理多个子节点（数组或迭代器）
    if (isArray(newChild)) {
      return reconcileChildrenArray(
        returnFiber,
        currentFirstChild,
        newChild,
        lanes,
      );
    }
    
    // 4. 无效子节点，清空
    return deleteRemainingChildren(returnFiber, currentFirstChild);
  }
  
  return reconcileChildFibers;
}
```

**功能**：
- 根据子节点类型选择不同策略
- 处理单个元素、文本、门户等
- 调用不同mount算法

---

### 6.8 reconcileSingleElement 函数

**源码位置**：`react-reconciler/src/ReactChildFiber.new.js`

```javascript
function reconcileSingleElement(
  returnFiber: Fiber,
  currentFirstChild: Fiber | null,
  element: ReactElement,
  lanes: Lanes,
): Fiber {
  const key = element.key;
  let child = currentFirstChild;
  
  // 1. 复用现有子节点（首次渲染无）
  while (child !== null) {
    if (child.key === key) {
      // 类型匹配，复用
      if (child.elementType === element.type) {
        deleteRemainingChildren(returnFiber, child.sibling);
        const existing = useFiber(child, element.props);
        existing.ref = coerceRef(returnFiber, child, element);
        existing.return = returnFiber;
        return existing;
      } else {
        // 类型不匹配，删除
        deleteRemainingChildren(returnFiber, child);
        break;
      }
    } else {
      // key不匹配，删除
      deleteChild(returnFiber, child);
    }
    child = child.sibling;
  }
  
  // 2. 创建新Fiber（首次渲染走这里）
  const created = createFiberFromElement(element, returnFiber.mode, lanes);
  created.ref = coerceRef(returnFiber, currentFirstChild, element);
  created.return = returnFiber;
  return created;
}
```

**功能**：
- 尝试复用现有子节点
- 创建新的Fiber节点
- 设置ref和return指针

---

### 6.9 createFiberFromElement 函数

**源码位置**：`react-reconciler/src/ReactFiber.new.js`

```javascript
export function createFiberFromElement(
  element: ReactElement,
  mode: TypeOfMode,
  lanes: Lanes,
): Fiber {
  let owner = null;
  let type = element.type;
  let key = element.key;
  let pendingProps = element.props;
  
  let fiberTag = IndeterminateComponent;
  
  // 1. 确定Fiber类型
  if (typeof type === 'function') {
    if (shouldConstruct(type)) {
      fiberTag = ClassComponent;
    } else {
      fiberTag = FunctionComponent;
    }
  } else if (typeof type === 'string') {
    fiberTag = HostComponent;
  } else {
    // 处理其他类型（Fragment, Context, Suspense等）
    getTagFromTypeAndProps(type, pendingProps);
  }
  
  // 2. 创建Fiber
  const fiber = createFiber(fiberTag, pendingProps, key, mode);
  fiber.elementType = type;
  fiber.type = type;
  fiber.lanes = lanes;
  
  return fiber;
}
```

**功能**：
- 根据元素类型确定Fiber tag
- 创建对应的Fiber节点
- 设置elementType和type
- 初始化lanes

---

## 7. 阶段六：完成工作单元

### 7.1 completeUnitOfWork 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function completeUnitOfWork(unitOfWork: Fiber): void {
  let completedWork = unitOfWork;
  
  do {
    // 1. 获取兄弟节点
    const current = completedWork.alternate;
    const returnFiber = completedWork.return;
    
    // 2. 完成当前工作
    completeWork(current, completedWork, subtreeRenderLanes);
    
    // 3. 收集副作用
    collectEffectList(current, completedWork);
    
    // 4. 获取兄弟节点
    const siblingFiber = completedWork.sibling;
    if (siblingFiber !== null) {
      // 有兄弟节点，处理兄弟
      workInProgress = siblingFiber;
      return;
    }
    
    // 5. 返回父节点
    completedWork = returnFiber;
    workInProgress = completedWork;
  } while (completedWork !== null);
  
  // 6. 根节点完成
  if (workInProgressRootExitStatus === RootInProgress) {
    workInProgressRootExitStatus = RootCompleted;
  }
}
```

**功能**：
- 迭代完成当前子树
- 调用completeWork处理具体节点
- 收集副作用列表
- 处理兄弟节点
- 向上返回到父节点

---

### 7.2 completeWork 函数

**源码位置**：`react-reconciler/src/ReactFiberCompleteWork.new.js`

```javascript
function completeWork(
  current: Fiber | null,
  workInProgress: Fiber,
  renderLanes: Lanes,
): Fiber | null {
  const newProps = workInProgress.pendingProps;
  
  switch (workInProgress.tag) {
    case IndeterminateComponent:
    case LazyComponent:
    case SimpleMemoComponent:
    case FunctionComponent:
    case ForwardRef:
    case Fragment:
      return null;
    
    case ClassComponent: {
      // 类组件完成工作
      const Component = workInProgress.type;
      
      if (current === null && workInProgress.flags & Update) {
        workInProgress.flags |= Update;
      }
      
      return null;
    }
    
    case HostRoot: {
      // 根节点完成
      popHostContainer(workInProgress);
      updateHostContainer(current, workInProgress);
      return null;
    }
    
    case HostComponent: {
      // 原生DOM节点完成
      popHostContext(workInProgress);
      const rootContainerInstance = getRootHostContainer();
      const type = workInProgress.type;
      
      if (current !== null && workInProgress.stateNode != null) {
        // 更新现有节点
        updateHostComponent(current, workInProgress, type, newProps, rootContainerInstance);
      } else {
        // 创建新节点（首次渲染走这里）
        const currentHostContext = getHostContext();
        
        // 1. 创建DOM实例
        const instance = createInstance(
          type,
          newProps,
          rootContainerInstance,
          currentHostContext,
          workInProgress,
        );
        
        // 2. 添加子节点
        appendAllChildren(instance, workInProgress, false, false);
        
        // 3. 完成节点初始化
        finalizeInitialChildren(instance, type, newProps, rootContainerInstance);
        
        // 4. 保存到stateNode
        workInProgress.stateNode = instance;
      }
      
      // 5. 标记ref
      if (workInProgress.ref !== null) {
        markRef(workInProgress);
      }
      
      return null;
    }
    
    case HostText: {
      // 文本节点完成
      const newText = newProps;
      
      if (current && workInProgress.stateNode != null) {
        // 更新文本
      } else {
        // 创建文本节点（首次渲染走这里）
        const rootContainerInstance = getRootHostContainer();
        const currentHostContext = getHostContext();
        workInProgress.stateNode = createTextInstance(
          newText,
          rootContainerInstance,
          currentHostContext,
          workInProgress,
        );
      }
      
      return null;
    }
    
    default:
      throw new Error('Unknown unit of work tag');
  }
}
```

**功能**：
- 根据Fiber类型处理完成逻辑
- 创建/更新真实DOM节点
- 设置DOM属性和事件
- 处理引用(ref)
- 收集副作用

---

### 7.3 createInstance 函数

**源码位置**：`react-dom/src/client/ReactDOMHostConfig.js`

```javascript
export function createInstance(
  type: string,
  props: Props,
  rootContainerInstance: Container,
  hostContext: HostContext,
  internalInstanceHandle: Object,
): Instance {
  // 1. 创建DOM元素
  let parentNamespace: string;
  if (__DEV__) {
    parentNamespace = hostContext;
  } else {
    parentNamespace = hostContext;
  }
  
  const domElement: Instance = createElement(
    type,
    props,
    rootContainerInstance,
    parentNamespace,
  );
  
  // 2. 准备初始属性
  prepareForCommit();
  
  return domElement;
}
```

**功能**：
- 创建真实DOM元素
- 处理命名空间（SVG等）
- 调用平台特定的创建逻辑

---

### 7.4 createElement 函数

**源码位置**：`react-dom/src/client/ReactDOMHostConfig.js`

```javascript
export function createElement(
  type: string,
  props: Props,
  rootContainerElement: Element | Document,
  parentNamespace: string,
): Element {
  let isCustomComponentTag;
  
  // 1. 处理命名空间
  let namespaceURI = parentNamespace;
  if (type === 'svg') {
    namespaceURI = SVG_NAMESPACE;
  } else if (type === 'math') {
    namespaceURI = MATH_NAMESPACE;
  }
  
  // 2. 创建DOM元素
  const element = namespaceURI === HTML_NAMESPACE
    ? document.createElement(type)
    : document.createElementNS(namespaceURI, type);
  
  return element;
}
```

**功能**：
- 创建DOM元素
- 处理HTML、SVG、MathML命名空间
- 返回原生DOM节点

---

### 7.5 appendAllChildren 函数

**源码位置**：`react-dom/src/client/ReactDOMHostConfig.js`

```javascript
export function appendAllChildren(
  parent: Instance,
  workInProgress: Fiber,
  needsVisibilityToggle: boolean,
  isHidden: boolean,
) {
  let node = workInProgress.child;
  
  while (node !== null) {
    if (node.tag === HostComponent || node.tag === HostText) {
      // 1. 找到DOM节点
      const instance = node.stateNode;
      
      if (instance !== null) {
        // 2. 添加到父节点
        parent.appendChild(instance);
      }
    } else if (node.tag === HostPortal) {
      // 跳过Portal
    } else if (node.child !== null) {
      // 3. 递归处理子节点
      node.child.return = node;
      node = node.child;
      continue;
    }
    
    // 4. 寻找下一个节点
    while (node.sibling === null) {
      if (node.return === null || node.return === workInProgress) {
        return;
      }
      node = node.return;
    }
    node.sibling.return = node.return;
    node = node.sibling;
  }
}
```

**功能**：
- 递归遍历子Fiber树
- 找到所有HostComponent和HostText节点
- 将DOM节点添加到父节点
- 处理嵌套结构

---

### 7.6 finalizeInitialChildren 函数

**源码位置**：`react-dom/src/client/ReactDOMHostConfig.js`

```javascript
export function finalizeInitialChildren(
  domElement: Instance,
  type: string,
  props: Props,
  rootContainerInstance: Container,
): boolean {
  // 1. 设置初始属性
  setInitialProperties(domElement, type, props, rootContainerInstance);
  
  // 2. 检查是否需要自动聚焦
  return shouldAutoFocusHostComponent(type, props);
}
```

**功能**：
- 设置DOM节点初始属性
- 处理特殊属性（如value、checked）
- 配置事件监听器
- 处理自动聚焦

---

### 7.7 setInitialProperties 函数

**源码位置**：`react-dom/src/client/ReactDOMComponent.js`

```javascript
export function setInitialProperties(
  domElement: Element,
  tag: string,
  rawProps: Object,
  rootContainerElement: Element | Document,
): void {
  const isCustomComponentTag = isCustomComponent(tag, rawProps);
  
  // 1. 处理特殊属性
  if (tag === 'input') {
    initWrapperState(domElement, rawProps);
  } else if (tag === 'option') {
    validateProps(domElement, rawProps);
  } else if (tag === 'select') {
    initWrapperState$1(domElement, rawProps);
  } else if (tag === 'textarea') {
    initWrapperState$2(domElement, rawProps);
  }
  
  // 2. 设置通用属性
  setInitialDOMProperties(
    tag,
    domElement,
    rootContainerElement,
    rawProps,
    isCustomComponentTag,
  );
}
```

**功能**：
- 处理表单元素特殊状态
- 设置通用DOM属性
- 配置事件监听器

---

## 8. 阶段七：提交阶段

### 8.1 commitRoot 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function commitRoot(root) {
  // 以最高优先级执行提交
  const renderPriorityLevel = getCurrentPriorityLevel();
  runWithPriority(ImmediatePriority, () => {
    commitRootImpl(root, renderPriorityLevel);
  });
  
  return null;
}
```

**功能**：
- 以最高优先级执行提交
- 确保提交过程不会被中断
- 调用核心实现

---

### 8.2 commitRootImpl 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function commitRootImpl(root, renderPriorityLevel) {
  // 1. 保存当前上下文
  const prevExecutionContext = executionContext;
  executionContext |= CommitContext;
  
  // 2. 获取已完成的Fiber树
  const finishedWork = root.finishedWork;
  const lanes = root.finishedLanes;
  
  // 3. 重置根节点状态
  root.finishedWork = null;
  root.finishedLanes = NoLanes;
  
  // 4. 检查是否需要提交
  if (finishedWork === null) {
    return null;
  }
  
  // 5. 初始化effect链表指针
  let firstEffect = finishedWork.firstEffect;
  
  // 6. 三个提交子阶段
  if (firstEffect !== null) {
    // 6.1 before mutation阶段
    commitBeforeMutationEffects(root, finishedWork);
    
    // 6.2 mutation阶段
    commitMutationEffects(root, finishedWork, lanes);
    
    // 6.3 切换current树
    root.current = finishedWork;
    
    // 6.4 layout阶段
    commitLayoutEffects(finishedWork, root, lanes);
  }
  
  // 7. 重置effect标记
  recursivelyTraverseLayoutEffects(finishedWork);
  
  // 8. 触发提交完成回调
  root.callbackNode = null;
  root.callbackPriority = NoLane;
  
  // 9. 调度useEffect
  if ((finishedWork.subtreeFlags & PassiveMask) !== NoFlags ||
      (finishedWork.flags & PassiveMask) !== NoFlags) {
    root.effectRemainingLanes = mergeLanes(root.effectRemainingLanes, lanes);
  }
  
  // 10. 调度被动效果
  if (root.effectRemainingLanes !== NoLanes) {
    scheduleCallback(NormalSchedulerPriority, () => {
      flushPassiveEffects();
      return null;
    });
  }
  
  return null;
}
```

**功能**：
- 管理提交阶段的整体流程
- 按顺序执行三个子阶段
- 切换current树指针
- 调度useEffect
- 触发提交回调

---

### 8.3 commitBeforeMutationEffects 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitBeforeMutationEffects(root, firstChild) {
  nextEffect = firstChild;
  while (nextEffect !== null) {
    const current = nextEffect.alternate;
    const flags = nextEffect.flags;
    
    // 处理快照效果
    if ((flags & Snapshot) !== NoFlags) {
      commitBeforeMutationEffectOnFiber(current, nextEffect);
    }
    
    // 标记存在被动效果
    if ((flags & Passive) !== NoFlags) {
      rootDoesHavePassiveEffects = true;
    }
    
    nextEffect = nextEffect.nextEffect;
  }
}
```

**功能**：
- 遍历所有带有副作用的Fiber节点
- 处理Snapshot标记
- 标记存在被动效果

---

### 8.4 commitMutationEffects 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitMutationEffects(root, finishedWork, committedLanes) {
  nextEffect = finishedWork;
  while (nextEffect !== null) {
    const primaryFlags = nextEffect.flags & (Placement | Update | Deletion);
    
    if (primaryFlags !== NoFlags) {
      commitMutationEffectsOnFiber(nextEffect, root);
    }
    
    nextEffect = nextEffect.nextEffect;
  }
}
```

**功能**：
- 遍历effect链表
- 识别需要处理的主要副作用
- 调用commitMutationEffectsOnFiber

---

### 8.5 commitMutationEffectsOnFiber 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitMutationEffectsOnFiber(finishedWork, root) {
  const flags = finishedWork.flags;
  
  // 1. 处理文本节点
  if (finishedWork.tag === HostText) {
    if (flags & Update) {
      const textInstance = finishedWork.stateNode;
      const newText = finishedWork.memoizedProps;
      commitTextUpdate(textInstance, newText);
    }
    return;
  }
  
  // 2. 处理原生组件
  if (finishedWork.tag === HostComponent) {
    // 2.1 处理插入（首次渲染走这里）
    if (flags & Placement) {
      commitPlacement(finishedWork);
      finishedWork.flags &= ~Placement;
    }
    
    // 2.2 处理更新
    const current = finishedWork.alternate;
    if (flags & Update && current !== null) {
      const oldProps = current.memoizedProps;
      const newProps = finishedWork.memoizedProps;
      const instance = finishedWork.stateNode;
      
      commitUpdate(
        instance,
        oldProps,
        newProps,
        finishedWork.type,
        finishedWork,
      );
    }
    
    return;
  }
  
  // 3. 处理类组件ref
  if (flags & Ref) {
    commitAttachRef(finishedWork);
  }
}
```

**功能**：
- 按Fiber类型分派处理
- 处理文本节点更新
- 处理原生DOM节点的插入、更新
- 处理ref附加

---

### 8.6 commitPlacement 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitPlacement(finishedWork) {
  // 1. 查找父容器
  const parentFiber = getHostParentFiber(finishedWork);
  const parentStateNode = parentFiber.stateNode;
  
  // 2. 查找插入位置
  const before = getHostSibling(finishedWork);
  
  // 3. 插入节点
  insertOrAppendPlacementNode(finishedWork, before, parentStateNode);
}
```

**功能**：
- 确定DOM节点的父容器
- 查找插入位置
- 调用平台特定的插入方法

---

### 8.7 insertOrAppendPlacementNode 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function insertOrAppendPlacementNode(node, before, parent) {
  const { tag } = node;
  
  if (tag === HostComponent || tag === HostText) {
    // 1. 找到DOM节点
    const stateNode = node.stateNode;
    
    // 2. 插入或追加
    if (before) {
      insertBefore(parent, stateNode, before);
    } else {
      appendChild(parent, stateNode);
    }
  } else {
    // 3. 递归处理子节点
    const child = node.child;
    if (child !== null) {
      insertOrAppendPlacementNode(child, before, parent);
      let sibling = child.sibling;
      while (sibling !== null) {
        insertOrAppendPlacementNode(sibling, before, parent);
        sibling = sibling.sibling;
      }
    }
  }
}
```

**功能**：
- 处理HostComponent和HostText的插入
- 递归处理子节点
- 调用appendChild或insertBefore

---

### 8.8 appendChild 函数

**源码位置**：`react-dom/src/client/ReactDOMHostConfig.js`

```javascript
export function appendChild(
  parentInstance: Instance,
  child: Instance,
): void {
  parentInstance.appendChild(child);
}
```

**功能**：
- 调用原生DOM API
- 将子节点添加到父节点

---

### 8.9 commitLayoutEffects 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitLayoutEffects(finishedWork, root, committedLanes) {
  nextEffect = finishedWork;
  while (nextEffect !== null) {
    const flags = nextEffect.flags;
    
    // 1. 处理插入和更新
    if ((flags & (Update | Callback)) !== NoFlags) {
      commitLayoutEffectOnFiber(root, current, nextEffect, committedLanes);
    }
    
    // 2. 处理ref
    if ((flags & Ref) !== NoFlags) {
      commitAttachRef(nextEffect);
    }
    
    nextEffect = nextEffect.nextEffect;
  }
}
```

**功能**：
- 遍历effect链表
- 处理Update和Callback标记
- 处理ref附加
- 调用commitLayoutEffectOnFiber

---

### 8.10 commitLayoutEffectOnFiber 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitLayoutEffectOnFiber(
  finishedRoot,
  current,
  finishedWork,
  committedLanes,
) {
  switch (finishedWork.tag) {
    case FunctionComponent:
    case ForwardRef:
    case SimpleMemoComponent: {
      // 1. 递归处理子树
      recursivelyTraverseLayoutEffects(finishedWork);
      
      // 2. 提交当前节点effects
      if (flags & Update) {
        commitHookEffectListMount(HookLayout | HookHasEffect, finishedWork);
      }
      
      return;
    }
    
    case ClassComponent: {
      // 1. 递归处理子树
      recursivelyTraverseLayoutEffects(finishedWork);
      
      // 2. 处理组件生命周期
      const instance = finishedWork.stateNode;
      
      if (finishedWork.flags & Update) {
        if (current === null) {
          // 组件挂载（首次渲染走这里）
          instance.componentDidMount();
        } else {
          // 组件更新
          const prevProps = current.memoizedProps;
          const prevState = current.memoizedState;
          instance.componentDidUpdate(prevProps, prevState, instance.__reactInternalSnapshotBeforeUpdate);
        }
      }
      
      return;
    }
    
    case HostRoot:
    case HostComponent:
    case HostText:
    case HostPortal: {
      // 原生节点不需要特殊layout效果
      recursivelyTraverseLayoutEffects(finishedWork);
      return;
    }
  }
}
```

**功能**：
- 按Fiber类型分派处理
- 执行useLayoutEffect
- 调用类组件生命周期
- 递归处理子树

---

### 8.11 commitHookEffectListMount 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitHookEffectListMount(flags: HookFlags, finishedWork: Fiber) {
  const updateQueue: FunctionComponentUpdateQueue | null = (finishedWork.updateQueue: any);
  const lastEffect = updateQueue !== null ? updateQueue.lastEffect : null;
  
  if (lastEffect !== null) {
    const firstEffect = lastEffect.next;
    let effect = firstEffect;
    
    do {
      if ((effect.tag & flags) === flags) {
        // 1. 创建ref
        const create = effect.create;
        effect.destroy = create();
      }
      effect = effect.next;
    } while (effect !== firstEffect);
  }
}
```

**功能**：
- 遍历hooks的effect链表
- 执行useLayoutEffect的create函数
- 保存destroy函数
- 处理错误边界

---

### 8.12 commitAttachRef 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitAttachRef(finishedWork: Fiber) {
  const ref = finishedWork.ref;
  if (ref !== null) {
    const instance = finishedWork.stateNode;
    
    if (typeof ref === 'function') {
      // 函数ref
      ref(instance);
    } else {
      // 对象ref
      ref.current = instance;
    }
  }
}
```

**功能**：
- 处理函数ref
- 处理对象ref
- 将DOM实例或组件实例赋值给ref

---

## 9. 阶段八：渲染后处理

### 9.1 scheduleCallback 调度useEffect

在`commitRootImpl`末尾：

```javascript
if (root.effectRemainingLanes !== NoLanes) {
  scheduleCallback(NormalSchedulerPriority, () => {
    flushPassiveEffects();
    return null;
  });
}
```

**功能**：
- 以Normal优先级调度flushPassiveEffects
- 作为微任务在下一个事件循环执行
- 避免阻塞关键渲染路径

---

### 9.2 flushPassiveEffects 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
export function flushPassiveEffects(): boolean {
  if (rootWithPendingPassiveEffects === null) {
    return false;
  }
  
  const renderPriority = lanesToEventPriority(pendingPassiveEffectsLanes);
  const priority = lowerEventPriority(DefaultEventPriority, renderPriority);
  
  const prevTransition = ReactCurrentBatchConfig.transition;
  const previousPriority = getCurrentUpdatePriority();
  
  try {
    ReactCurrentBatchConfig.transition = null;
    setCurrentUpdatePriority(priority);
    return flushPassiveEffectsImpl();
  } finally {
    setCurrentUpdatePriority(previousPriority);
    ReactCurrentBatchConfig.transition = prevTransition;
  }
}
```

**功能**：
- 验证是否存在挂起的被动效果
- 确定执行优先级
- 设置执行上下文
- 调用核心实现

---

### 9.3 flushPassiveEffectsImpl 函数

**源码位置**：`react-reconciler/src/ReactFiberWorkLoop.new.js`

```javascript
function flushPassiveEffectsImpl() {
  if (rootWithPendingPassiveEffects === null) {
    return false;
  }
  
  const root = rootWithPendingPassiveEffects;
  const lanes = pendingPassiveEffectsLanes;
  rootWithPendingPassiveEffects = null;
  pendingPassiveEffectsLanes = NoLanes;
  
  const prevExecutionContext = executionContext;
  executionContext |= CommitContext;
  
  // 1. 提交挂载效果
  commitPassiveMountEffects(root, finishedWork);
  
  // 2. 提交卸载效果
  commitPassiveUnmountEffects(finishedWork);
  
  executionContext = prevExecutionContext;
  
  return true;
}
```

**功能**：
- 获取根节点和待处理车道
- 设置Commit上下文
- 执行挂载效果（useEffect创建函数）
- 执行卸载效果（useEffect清理函数）

---

### 9.4 commitPassiveMountEffects 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitPassiveMountEffects(root, firstChild) {
  let node = firstChild;
  while (node !== null) {
    const child = node.child;
    if ((node.subtreeFlags & PassiveMask) !== NoFlags && child !== null) {
      child.return = node;
      node = child;
      continue;
    }
    
    if ((node.flags & PassiveMask) !== NoFlags) {
      commitPassiveMountEffectOnFiber(root, node);
    }
    
    while (node.sibling === null) {
      if (node.return === null || node.return === root) {
        return;
      }
      node = node.return;
    }
    node.sibling.return = node.return;
    node = node.sibling;
  }
}
```

**功能**：
- 深度优先遍历Fiber树
- 识别带有Passive标记的节点
- 调用commitPassiveMountEffectOnFiber

---

### 9.5 commitPassiveMountEffectOnFiber 函数

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitPassiveMountEffectOnFiber(finishedRoot, finishedWork) {
  switch (finishedWork.tag) {
    case FunctionComponent:
    case ForwardRef:
    case SimpleMemoComponent: {
      // 提交Hook的挂载效果
      commitHookEffectListMount(HookPassive | HookHasEffect, finishedWork);
      break;
    }
    // ...其他类型
  }
}
```

**功能**：
- 按Fiber类型分派处理
- 为函数组件执行useEffect
- 处理根节点特殊回调

---

### 9.6 commitHookEffectListMount (Passive)

**源码位置**：`react-reconciler/src/ReactFiberCommitWork.new.js`

```javascript
function commitHookEffectListMount(flags: HookFlags, finishedWork: Fiber) {
  const updateQueue: FunctionComponentUpdateQueue | null = (finishedWork.updateQueue: any);
  const lastEffect = updateQueue !== null ? updateQueue.lastEffect : null;
  
  if (lastEffect !== null) {
    const firstEffect = lastEffect.next;
    let effect = firstEffect;
    
    do {
      if ((effect.tag & flags) === flags) {
        // 执行useEffect的create函数
        const create = effect.create;
        const destroy = create();
        
        // 保存销毁函数
        effect.destroy = typeof destroy === 'function' ? destroy : undefined;
      }
      effect = effect.next;
    } while (effect !== firstEffect);
  }
}
```

**功能**：
- 获取函数组件的updateQueue
- 遍历effect链表
- 执行useEffect的create函数
- 保存返回的destroy函数

---

## 10. 首次渲染完整调用流程总结

### 10.1 调用流程时序图

```
时间轴 →
┌─────────────────────────────────────────────────────────────────┐
│ 阶段一：创建根节点                                               │
│ createRoot() → createRootImpl() → createFiberRoot()             │
│   → FiberRootNode() → createHostRootFiber() → FiberNode()       │
│   → initializeUpdateQueue() → ReactDOMRoot()                    │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段二：触发渲染                                                 │
│ root.render(<App/>) → updateContainer()                         │
│   → requestEventTime() → requestUpdateLane()                    │
│   → createUpdate() → enqueueUpdate()                            │
│   → scheduleUpdateOnFiber()                                     │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段三：调度更新                                                 │
│ scheduleUpdateOnFiber() → markUpdateLaneFromFiberToRoot()       │
│   → markRootUpdated() → ensureRootIsScheduled()                 │
│   → getNextLanes() → scheduleCallback()                         │
│   → performConcurrentWorkOnRoot()                               │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段四：准备渲染                                                 │
│ renderRootConcurrent() → prepareFreshStack()                    │
│   → createWorkInProgress() → workLoopConcurrent()               │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段五：Render阶段（协调）                                       │
│ workLoopConcurrent() → performUnitOfWork()                      │
│   → beginWork() → updateHostRoot()                              │
│     → processUpdateQueue() → reconcileChildren()                │
│     → mountChildFibers() → createFiberFromElement()             │
│   → completeUnitOfWork() → completeWork()                       │
│     → createInstance() → appendAllChildren()                    │
│     → finalizeInitialChildren()                                 │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段六：Commit阶段（提交）                                       │
│ commitRoot() → commitRootImpl()                                 │
│   → commitBeforeMutationEffects()                               │
│   → commitMutationEffects() → commitPlacement()                 │
│     → appendChild()                                             │
│   → root.current = finishedWork                                 │
│   → commitLayoutEffects() → commitHookEffectListMount(Layout)   │
│     → commitAttachRef()                                         │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段七：渲染后处理                                               │
│ scheduleCallback(NormalPriority, flushPassiveEffects)           │
│   → flushPassiveEffects() → commitPassiveMountEffects()         │
│     → commitHookEffectListMount(Passive)                        │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ 阶段八：渲染完成                                                 │
│ 应用首次呈现在用户面前                                           │
└─────────────────────────────────────────────────────────────────┘
```

### 10.2 关键数据结构变化

#### 初始状态
```
FiberRootNode
  └── current: HostRoot Fiber (stateNode: FiberRootNode)
        └── updateQueue: { shared: { pending: null } }
```

#### updateContainer后
```
FiberRootNode
  └── current: HostRoot Fiber
        └── updateQueue: { shared: { pending: Update } }
              Update: { payload: { element: <App/> } }
```

#### render阶段后
```
FiberRootNode
  ├── current: HostRoot Fiber (旧树)
  └── finishedWork: HostRoot Fiber (新树)
        └── child: App Fiber
              └── child: div Fiber
                    └── child: text Fiber
```

#### commit阶段后
```
FiberRootNode
  └── current: HostRoot Fiber (新树，指向finishedWork)
        └── child: App Fiber
              └── child: div Fiber (stateNode: <div>)
                    └── child: text Fiber (stateNode: #text)
```

### 10.3 关键函数调用次数（简单应用）

假设应用结构：`<App><Header/><Main><Content/></Main></App>`

| 函数                                   | 调用次数 | 说明          |
| ------------------------------------ | ---- | ----------- |
| `beginWork`                          | 5    | 每个Fiber节点一次 |
| `completeWork`                       | 5    | 每个Fiber节点一次 |
| `createInstance`                     | 3    | 3个DOM节点     |
| `appendChild`                        | 4    | 4次DOM插入     |
| `commitHookEffectListMount(Layout)`  | 2    | 2个函数组件      |
| `commitHookEffectListMount(Passive)` | 2    | 2个useEffect |

### 10.4 性能关键点

1. **可中断渲染**：workLoopConcurrent支持中断和恢复
2. **优先级调度**：车道模型确保高优先级更新优先
3. **双缓冲架构**：避免破坏当前UI状态
4. **bailout优化**：跳过无需更新的子树
5. **effect链表**：高效收集和处理副作用
6. **批量DOM操作**：在mutation阶段集中处理

### 10.5 调试技巧

```javascript
// 查看Fiber树
const container = document.getElementById('root');
const fiber = container.__reactContainer$__.current;

// 查看更新队列
console.log(fiber.updateQueue);

// 查看effect链表
console.log(fiber.firstEffect);

// 查看子树
console.log(fiber.child);
```

React 18.2.0的首次渲染流程是一个精心设计的复杂系统，通过分阶段处理、优先级调度和可中断机制，在保证性能的同时提供了丰富的功能。理解这个流程对于掌握React的核心原理、性能优化和调试技巧至关重要。