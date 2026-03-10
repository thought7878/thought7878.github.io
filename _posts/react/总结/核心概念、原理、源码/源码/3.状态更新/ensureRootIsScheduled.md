这段JavaScript代码是React调度系统的核心函数，用于**为根节点调度任务**。主要功能包括：

- 检查并标记饥饿的优先级队列为过期
- 确定下一个要处理的优先级队列
- 如果没有待处理任务则取消现有回调
- 根据优先级决定是否复用现有任务或创建新任务
- 同步任务直接执行，异步任务通过调度器安排

## 源码
`packages/react-reconciler/src/ReactFiberWorkLoop.old.js`


```ts
// Use this function to schedule a task for a root. There's only one task per
// root; if a task was already scheduled, we'll check to make sure the priority
// of the existing task is the same as the priority of the next level that the
// root has work on. This function is called on every update, and right before
// exiting a task.
/**
 * 调度根节点的任务，确保它被安排在适当优先级执行
 * 每个根节点只允许存在一个任务；如果已有任务被调度，
 * 我们会检查现有任务的优先级是否与根节点下一个工作层级的优先级相同。
 * 此函数在每次更新时以及退出任务前调用。
 *
 * @param {FiberRoot} root - 需要调度的Fiber根节点
 * @param {number} currentTime - 当前时间戳，用于确定任务优先级
 * @returns {void}
 */
function ensureRootIsScheduled(root: FiberRoot, currentTime: number) {
  // 获取当前根节点已有的回调节点（如果存在）
  const existingCallbackNode = root.callbackNode;

  // Check if any lanes are being starved by other work. If so, mark them as
  // expired so we know to work on those next.
  // 检查是否有lane被其他工作阻塞。如果有，标记它们为过期，
  // 这样我们就能知道接下来需要处理这些过期的lane
  markStarvedLanesAsExpired(root, currentTime);

  // Determine the next lanes to work on, and their priority.
  // 获取要处理的lane及其优先级
  const nextLanes = getNextLanes(
    root,
    root === workInProgressRoot ? workInProgressRootRenderLanes : NoLanes,
  );

  if (nextLanes === NoLanes) {
    // Special case: There's nothing to work on.
    // 特殊情况：没有需要处理的工作
    if (existingCallbackNode !== null) {
      cancelCallback(existingCallbackNode);
    }
    root.callbackNode = null;
    root.callbackPriority = NoLane;
    return;
  }

  // We use the highest priority lane to represent the priority of the callback.
  // 获取/使用最高优先级lane，来表示回调的优先级
  const newCallbackPriority = getHighestPriorityLane(nextLanes);

  // Check if there's an existing task. We may be able to reuse it.
  // 检查是否存在现有任务，我们可能可以重用它
  const existingCallbackPriority = root.callbackPriority;
  if (
    existingCallbackPriority === newCallbackPriority &&
    // Special case related to `act`. If the currently scheduled task is a
    // Scheduler task, rather than an `act` task, cancel it and re-scheduled
    // on the `act` queue.
    // 与`act`相关的特殊情况。如果当前调度的任务是Scheduler任务而不是`act`任务，
    // 则取消它并在`act`队列上重新调度
    !(
      __DEV__ &&
      ReactCurrentActQueue.current !== null &&
      existingCallbackNode !== fakeActCallbackNode
    )
  ) {
    if (__DEV__) {
      // If we're going to re-use an existing task, it needs to exist.
      // Assume that discrete update microtasks are non-cancellable and null.
      // TODO: Temporary until we confirm this warning is not fired.
      // 如果我们要重用现有任务，它必须存在
      // 假设离散更新微任务是不可取消的且为空
      if (
        existingCallbackNode == null &&
        existingCallbackPriority !== SyncLane
      ) {
        console.error(
          'Expected scheduled callback to exist. This error is likely caused by a bug in React. Please file an issue.',
        );
      }
    }
    // The priority hasn't changed. We can reuse the existing task. Exit.
    // 优先级未发生更改，我们可以重用现有任务，直接退出
    return;
  }

  if (existingCallbackNode != null) {
    // Cancel the existing callback. We'll schedule a new one below.
    // 取消现有回调，稍后我们将调度一个新的回调
    cancelCallback(existingCallbackNode);
  }

  // Schedule a new callback.
  // 调度一个新的回调
  let newCallbackNode;
  if (newCallbackPriority === SyncLane) {
    // Special case: Sync React callbacks are scheduled on a special
    // internal queue
    // ！！！同步任务，同步优先级
    // 特殊情况：同步React回调，被调度到特殊的内部队列
    if (root.tag === LegacyRoot) {
      if (__DEV__ && ReactCurrentActQueue.isBatchingLegacy !== null) {
        ReactCurrentActQueue.didScheduleLegacyUpdate = true;
      }
      scheduleLegacySyncCallback(performSyncWorkOnRoot.bind(null, root));
    } else {
      // ！！！同步任务，调度performSyncWorkOnRoot
      scheduleSyncCallback(performSyncWorkOnRoot.bind(null, root));
    }
    if (supportsMicrotasks) {
      // Flush the queue in a microtask.
      // 在微任务中刷新队列
      if (__DEV__ && ReactCurrentActQueue.current !== null) {
        // Inside `act`, use our internal `act` queue so that these get flushed
        // at the end of the current scope even when using the sync version
        // of `act`.
        // 在`act`内部，使用我们的内部`act`队列，以便即使使用同步版本的`act`，
        // 这些也会在当前作用域结束时被刷新
        ReactCurrentActQueue.current.push(flushSyncCallbacks);
      } else {
        scheduleMicrotask(() => {
          // In Safari, appending an iframe forces microtasks to run.
          // https://github.com/facebook/react/issues/22459
          // We don't support running callbacks in the middle of render
          // or commit so we need to check against that.
          // 在Safari中，添加iframe会强制微任务运行
          // https://github.com/facebook/react/issues/22459
          // 我们不支持在渲染或提交过程中运行回调，因此需要检查
          if (
            (executionContext & (RenderContext | CommitContext)) ===
            NoContext
          ) {
            // Note that this would still prematurely flush the callbacks
            // if this happens outside render or commit phase (e.g. in an event).
            // 注意，如果这发生在渲染或提交阶段之外（例如在事件中），仍会提前刷新回调
            flushSyncCallbacks();
          }
        });
      }
    } else {
      // Flush the queue in an Immediate task.
      // 在立即任务中刷新队列
      scheduleCallback(ImmediateSchedulerPriority, flushSyncCallbacks);
    }
    newCallbackNode = null;
  } else {
    // ！！！对于异步任务，根据事件优先级映射到相应的调度器优先级
    let schedulerPriorityLevel;
    // 转换优先级：Update对象的lane--->EventPriority--->SchedulerPriority
    switch (lanesToEventPriority(nextLanes)) {
      case DiscreteEventPriority:
        schedulerPriorityLevel = ImmediateSchedulerPriority;
        break;
      case ContinuousEventPriority:
        schedulerPriorityLevel = UserBlockingSchedulerPriority;
        break;
      case DefaultEventPriority:
        schedulerPriorityLevel = NormalSchedulerPriority;
        break;
      case IdleEventPriority:
        schedulerPriorityLevel = IdleSchedulerPriority;
        break;
      default:
        schedulerPriorityLevel = NormalSchedulerPriority;
        break;
    }
    // 调度并发工作的回调
    // ！！！异步任务，调度performConcurrentWorkOnRoot
    newCallbackNode = scheduleCallback(
      schedulerPriorityLevel,
      performConcurrentWorkOnRoot.bind(null, root),
    );
  }

  // 更新根节点的回调优先级和回调节点
  root.callbackPriority = newCallbackPriority;
  root.callbackNode = newCallbackNode;
}
```