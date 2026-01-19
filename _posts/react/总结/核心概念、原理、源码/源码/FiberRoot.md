# React 18 FiberRoot 属性详解

`FiberRoot`是React 18应用的核心数据结构，***作为整个Fiber树的根节点***，它**存储了应用的*全局状态、调度信息、渲染元数据***。不同于普通的Fiber节点，**FiberRoot专为管理整个应用而设计，是连接React协调引擎与宿主环境的桥梁**。

## 1. 基础属性

### containerInfo
```javascript
this.containerInfo = containerInfo; // DOM容器元素
```
- **类型**: `any`（DOM元素或宿主环境特定容器）
- **功能**: *存储React应用挂载的宿主容器*
- **使用场景**: 
  - 在commit阶段访问DOM容器
  - 卸载组件时清理容器
  - 服务端渲染时提供文档对象
- **重要性**: *作为React与宿主环境的连接点*

### current
```javascript
this.current = null; // 指向当前Fiber树
```
- **类型**: `Fiber | null`
- **功能**: 指向*当前显示在屏幕上的Fiber树（current树）*
- **关键机制**:
  - 双缓冲架构的核心：*当前树与workInProgress树交替*
  - *提交阶段完成后，workInProgress树成为新的current树*
- **访问模式**: 通过`fiberRoot.current`获取当前UI状态

### finishedWork
```javascript
this.finishedWork = null; // 已完成的workInProgress树
```
- **类型**: `Fiber | null`
- **功能**: 存储已完成渲染但尚未提交的Fiber树
- **生命周期**:
  - 在render阶段结束时设置
  - 在commit阶段被应用到DOM
  - 提交完成后重置为null
- **优化**: 使React能将渲染与提交分离，支持并发模式

### pendingChildren
```javascript
this.pendingChildren = null; // 挂起的子节点
```
- **类型**: `any`
- **功能**: *暂存待处理的子节点数据*
- **使用场景**:
  - 服务端渲染水合过程
  - 暂停渲染时缓存子节点
- **React 18改进**: 更高效地处理Suspense边界

## 2. 调度与优先级系统
Lane源码：packages/react-reconciler/src/ReactFiberLane.new.js
### pendingLanes
```javascript
this.pendingLanes = NoLanes; // 待处理的更新优先级车道
```
- **类型**: `Lanes`（31位bitmask）
- **功能**: *标记所有待处理的更新的优先级*
- **计算方式**: 
  ```javascript
  root.pendingLanes = mergeLanes(root.pendingLanes, updateLane);
  ```
- **使用场景**:
  - 调度器决定下一个处理的优先级
  - 确定工作是否需要继续
- **示例**: `0b101` 表示同时有DefaultLane和SyncLane的更新

### suspendedLanes
```javascript
this.suspendedLanes = NoLanes; // 挂起的车道
```
- **类型**: `Lanes`
- **功能**: 标记*因Suspense而挂起的更新*
- **工作流程**:
  - 组件抛出Promise时标记对应lane
  - 数据解决后清除标记
  - 决定是否显示fallback UI
- **关键方法**: `markRootSuspended(root, suspendedLanes)`

### pingedLanes
```javascript
this.pingedLanes = NoLanes; // 唤醒的车道
```
- **类型**: `Lanes`
- **功能**: 标记*已被唤醒（数据已解决）但尚未处理的更新*
- **触发时机**: 
  - *Suspense数据Promise解决时*
  - 调用`root.pingTransition()`
- **处理流程**: 
  ```javascript
  root.pingedLanes = mergeLanes(root.pingedLanes, pingedLane);
  ensureRootIsScheduled(root, eventTime);
  ```

### expiredLanes
```javascript
this.expiredLanes = NoLanes; // 过期的车道
```
- **类型**: `Lanes`
- **功能**: 标记*已过期需要同步处理的低优先级更新*
- **过期机制**:
  - 低优先级更新超过5秒未处理
  - 防止用户体验卡顿
- **计算**: `markStarvedLanesAsExpired(root, currentTime)`

### eventTimes 与 expirationTimes
```javascript
this.eventTimes = createLaneMap(NoLanes);
this.expirationTimes = createLaneMap(NoTimestamp);
```
- **类型**: `LaneMap<number>`
- **功能**:
  - `eventTimes`: 存储*每个lane的最后事件时间*
  - `expirationTimes`: 存储*每个lane的过期时间*
- **数据结构**: 
  ```javascript
  function createLaneMap(initial: T): LaneMap<T> {
    return [initial, initial, initial, ..., initial]; // 31个槽位
  }
  ```
- **用途**: 优先级调度和过期计算的基础

## 3. 回调与任务管理

### callbackNode
```javascript
this.callbackNode = null; // 用于调度的回调节点
```
- **类型**: `any`（Scheduler回调句柄）
- **功能**: *存储当前调度的任务引用*
- **工作流程**:
  - 创建: `root.callbackNode = scheduleCallback(priority, callback)`
  - 取消: `cancelCallback(root.callbackNode)`
  - 重置: 提交完成后设为null
- **关键作用**: 支持任务中断和恢复

### callbackPriority
```javascript
this.callbackPriority = NoLane; // 当前回调的优先级
```
- **类型**: `Lane`
- **功能**: 跟踪*当前调度回调的优先级*
- **用途**:
  - 避免重复调度相同优先级任务
  - 决定是否需要取消现有回调
- **优化示例**:
  ```javascript
  if (existingCallbackPriority === newCallbackPriority) {
    return; // 无需重新调度
  }
  ```

## 4. 优先级缠绕系统

### entangledLanes 与 entanglements
```javascript
this.entangledLanes = NoLanes;
this.entanglements = createLaneMap(NoLanes);
```
- **类型**: 
  - `entangledLanes`: `Lanes`
  - `entanglements`: `LaneMap<Lanes>`
- **功能**: 管理优先级缠绕（防止低优先级更新饥饿）
- **工作原理**:
  - 当高优先级更新打断低优先级时，将它们"缠绕"
  - 确保被打断的低优先级更新最终会完成
- **示例**:
  ```javascript
  // 当SyncLane打断DefaultLane
  root.entangledLanes |= DefaultLane;
  root.entanglements[DefaultLaneIndex] |= SyncLane;
  ```
- **处理**: `markRootEntangled(root, entangledLanes)`

### mutableReadLanes
```javascript
this.mutableReadLanes = NoLanes; // 可变读取车道
```
- **类型**: `Lanes`
- **功能**: 跟踪依赖于可变源（如外部store）的更新
- **使用场景**:
  - 与useMutableSource hook集成
  - 确保外部数据变更触发重新渲染
- **React 18特性**: 支持外部数据源的细粒度订阅

## 5. 水合与服务端渲染

### hydrationCallbacks
```javascript
this.hydrationCallbacks = hydrationCallbacks; // 水合回调
```
- **类型**: `null | SuspenseHydrationCallbacks`
- **功能**: 提供水合过程的生命周期钩子
- **接口定义**:
  ```javascript
  type SuspenseHydrationCallbacks = {
    onHydrated?: (suspenseInstance: SuspenseInstance) => void,
    onDeleted?: (suspenseInstance: SuspenseInstance) => void,
  };
  ```
- **用途**: 服务端渲染时协调客户端水合

### identifierPrefix
```javascript
this.identifierPrefix = ''; // 标识符前缀
```
- **类型**: `string`
- **功能**: 为服务端渲染生成唯一ID添加前缀
- **典型值**: `R:`（React默认）或自定义前缀
- **使用场景**: 
  ```javascript
  const id = root.identifierPrefix + makeId();
  return <div id={id}>Content</div>;
  ```
- **目的**: 避免多个React应用在同一页面ID冲突

### onRecoverableError
```javascript
this.onRecoverableError = defaultOnRecoverableError;
```
- **类型**: `(error: mixed) => void`
- **功能**: 处理可恢复的渲染错误
- **默认实现**: 将错误记录到控制台
- **自定义**: 
  ```javascript
  createRoot(container, {
    onRecoverableError: (error, errorInfo) => {
      logErrorToService(error, errorInfo);
    }
  });
  ```
- **错误边界补充**: 处理边界之外的错误

## 6. React 18新特性支持

### formState
```javascript
this.formState = null; // 表单状态
this.formStateIsMounted = false;
```
- **类型**: `any`
- **功能**: 支持React 18的表单操作API
- **使用场景**:
  - 服务器组件中的表单提交
  - 跨组件边界保持表单状态
- **创新**: 使表单状态在服务器和客户端之间无缝流动

### incompleteTransitions
```javascript
this.incompleteTransitions = new Map();
```
- **类型**: `Map<Transition, Transitions>`
- **功能**: 跟踪未完成的Transition
- **键值对**:
  - key: 由startTransition创建的唯一标识
  - value: Transition对象（包含回调和优先级）
- **用途**: 管理并发模式下的过渡状态
- **清理**: 提交完成后清理已完成的过渡

### cache
```javascript
this.cache = null; // 缓存存储
```
- **类型**: `Cache | null`
- **功能**: 存储组件和数据的缓存
- **React 18特性**: 
  - 支持useCacheRefresh hook
  - 服务器组件缓存
- **结构**:
  ```javascript
  type Cache = {
    data: Map<string, mixed>,
    expiration: number,
  };
  ```

## 7. 调试与开发支持

### pingCache
```javascript
this.pingCache = null; // 挂起缓存
```
- **类型**: `WeakMap<Promise, Set<mixed>> | null`
- **功能**: 缓存Suspense Promise的响应
- **开发用途**:
  - 避免重复请求相同数据
  - 优化热重载体验
- **清理策略**: 在提交阶段后清理

### memoizedUpdaters 与 pendingUpdatersLaneMap
```javascript
this.memoizedUpdaters = new Set();
this.pendingUpdatersLaneMap = createLaneMap(null);
```
- **类型**:
  - `memoizedUpdaters`: `Set<Fiber>`
  - `pendingUpdatersLaneMap`: `LaneMap<Set<Fiber> | null>`
- **功能**: 跟踪哪些组件触发了更新
- **开发用途**:
  - DevTools显示更新来源
  - 性能分析
- **使用**: `markUpdateLaneFromFiberToRoot`中更新

## 8. 模式与标签

### tag
```javascript
this.tag = tag; // 根标签
```
- **类型**: `RootTag`
- **枚举值**:
  ```javascript
  export const LegacyRoot = 0; // 传统模式
  export const ConcurrentRoot = 1; // 并发模式
  ```
- **功能**: *标识根节点的渲染模式*
- **影响**:
  - `LegacyRoot`: 同步渲染，无并发特性
  - `ConcurrentRoot`: *支持所有React 18特性*
- **设置**: 由createRoot vs ReactDOM.render决定

## 9. 性能分析

### (隐式属性) 实际持续时间
```javascript
// 仅在enableProfilerTimer时存在
this.actualDuration = 0;
this.actualStartTime = -1;
this.selfBaseDuration = 0;
this.treeBaseDuration = 0;
```
- **功能**: 支持\<Profiler\>组件的性能分析
- **计算**:
  - `actualDuration`: 渲染实际耗时
  - `treeBaseDuration`: 子树基础渲染时间
- **用途**: 识别性能瓶颈

## 10. FiberRoot生命周期示例

```javascript
// 创建
const root = new FiberRootNode(container, ConcurrentRoot, null);

// 初始渲染
root.pendingLanes = DefaultLane;
root.current = initialFiberTree;

// 处理更新
root.pendingLanes = mergeLanes(root.pendingLanes, SyncLane);
root.callbackNode = scheduleCallback(ImmediatePriority, renderCallback);

// Suspense挂起
root.suspendedLanes = DefaultLane;
root.pingedLanes = NoLanes;

// 数据解决
root.suspendedLanes = NoLanes;
root.pingedLanes = DefaultLane;
root.callbackNode = scheduleCallback(NormalPriority, renderCallback);

// 提交完成
root.finishedWork = null;
root.current = workInProgressTree;
root.pendingLanes = NoLanes;

// 卸载
root.containerInfo = null;
root.current = null;
```

## 11. 关键设计原则

1. **优先级驱动**：所有属性围绕lane优先级系统设计
2. **不可变性**：完成的树在提交前保持不可变
3. **渐进式处理**：支持中断和恢复
4. **资源管理**：自动清理不再需要的资源
5. **模式隔离**：并发模式与传统模式并存
6. **可观察性**：提供丰富的调试和分析能力

## 12. 调试技巧

在开发环境，可以通过以下方式检查FiberRoot：
```javascript
// 获取根节点
const container = document.getElementById('root');
const rootKey = Object.keys(container).find(key => 
  key.startsWith('__reactContainer')
);
const fiberRoot = container[rootKey]._reactInternals;

// 检查当前状态
console.log('Pending lanes:', fiberRoot.pendingLanes.toString(2));
console.log('Current tree:', fiberRoot.current);
console.log('Finished work:', fiberRoot.finishedWork);
```

FiberRoot是React 18架构的核心，理解其属性和工作机制，对于掌握React的并发模式、优先级调度和新特性至关重要。它不仅是数据结构，更是React响应式更新系统的大脑，协调着整个应用的渲染生命周期。通过精心设计的属性系统，FiberRoot使React能够在复杂场景下保持高性能和流畅的用户体验。