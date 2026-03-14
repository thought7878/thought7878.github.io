
该函数是 React 内部用于*处理 useState 和 useReducer 钩子状态更新*的核心函数。**触发/分发状态更新的函数**，用于实现useState和useReducer的更新机制

**算法**/主要功能：

- 参数验证：开发环境下检查是否传入了不支持的回调参数
- 获取更新的优先级：获取/请求一个更新优先级
- **创建更新对象**：生成包含优先级、动作（新的状态值、或状态更新函数）等信息的更新对象
- **处理渲染阶段、正常阶段的更新**：区分渲染阶段和正常阶段的更新
	- 渲染阶段的更新：
		- 将新的更新对象，加入渲染阶段的更新队列（*queue.pending*），[[enqueueRenderPhaseUpdate]]
	- 正常阶段的更新：
		- 检查当前 fiber 和其 alternate 是否都没有待处理的更新；是，都没有，意味着，更新队列当前为空：
			- **状态预计算优化**：尝试预先计算新状态，如无变化则跳过渲染，return终止（实现提前状态计算优化，如果新旧状态相同则跳过渲染），`enqueueConcurrentHookUpdateAndEagerlyBailout`
		- **队列管理**：将更新添加到并发队列（*queue.interleaved*），[[enqueueConcurrentHookUpdate]]
		- **调度渲染**：调度 fiber 上的更新，[[scheduleUpdateOnFiber]]
- 开发者工具集成：标记更新供调试使用

## 源码
`packages/react-reconciler/src/ReactFiberHooks.old.js`

```ts
/**
 * 触发/分发状态更新的函数，用于实现useState和useReducer的更新机制
 * @param {Fiber} fiber - 当前组件的fiber节点
 * @param {UpdateQueue} queue - 状态更新队列
 * @param {A} action - 要执行的动作（新的状态值或状态更新函数）
 */
function dispatchSetState<S, A>(
  fiber: Fiber, // 当前组件的 fiber 节点
  queue: UpdateQueue<S, A>, // 状态更新队列
  action: A, // 要执行的动作（新的状态值、或状态更新函数）
) {
  // 开发环境下检查是否传递了第二个回调参数
  if (__DEV__) {
    if (typeof arguments[3] === 'function') {
      console.error(
        "State updates from the useState() and useReducer() Hooks don't support the " +
          'second callback argument. To execute a side effect after ' +
          'rendering, declare it in the component body with useEffect().',
      );
    }
  }

  // 请求一个更新优先级
  const lane = requestUpdateLane(fiber);

  // ！！！创建一个新的更新对象
  const update: Update<S, A> = {
    lane, // 更新的优先级
    action, // 更新动作
    hasEagerState: false, // 是否有预先计算的状态
    eagerState: null, // 预先计算的状态
    next: (null: any), // 指向下一个更新
  };

  // ！！！区分渲染阶段、正常阶段的更新，检查是否在渲染阶段进行更新
  if (isRenderPhaseUpdate(fiber)) {
    // ！！！渲染阶段的更新
    // 将新的更新对象，加入渲染阶段的更新队列
    enqueueRenderPhaseUpdate(queue, update);
  } else {
    // ！！！正常阶段的更新
    // 不是渲染阶段更新，处理正常的异步更新
    const alternate = fiber.alternate;
    // ！！！状态预计算优化
    // 检查当前 fiber 和其 alternate 是否都没有待处理的更新；都没有，意味着，更新队列当前为空
    if (
      fiber.lanes === NoLanes &&
      (alternate === null || alternate.lanes === NoLanes)
    ) {
      // ！！！更新队列当前为空，这意味着我们可以在进入渲染阶段之前预先计算下一个状态
      // ！！！如果新状态与当前状态相同，可以完全跳过渲染

      const lastRenderedReducer = queue.lastRenderedReducer;
      // 最后一次渲染时使用的reducer函数（状态更新函数，如basicStateReducer），存在
      if (lastRenderedReducer !== null) {
        let prevDispatcher;
        if (__DEV__) {
          // 在开发环境下临时替换 dispatcher 以防止在 reducer 内部错误地调用其他 hooks
          prevDispatcher = ReactCurrentDispatcher.current;
          ReactCurrentDispatcher.current =
            InvalidNestedHooksDispatcherOnUpdateInDEV;
        }
        try {
          // 获取当前状态
          const currentState: S = (queue.lastRenderedState: any);
          // ！！！执行 reducer 函数计算新状态
          const eagerState = lastRenderedReducer(currentState, action);
          // 将预先计算的状态和使用的 reducer 存储在更新对象上
          // 如果在进入渲染阶段时 reducer 没有改变，则可以使用预计算的状态
          // 而无需再次调用 reducer
          update.hasEagerState = true;
          update.eagerState = eagerState;
          // 检查新状态是否与当前状态相同
          if (is(eagerState, currentState)) {
            // ！！！快速路径：如果状态没有变化，我们可以跳过重新渲染
            // 但仍有可能在其他原因导致组件重新渲染时需要重新应用此更新
            enqueueConcurrentHookUpdateAndEagerlyBailout(
              fiber,
              queue,
              update,
              lane,
            );
            return;
          }
        } catch (error) {
          // 抑制错误，它将在渲染阶段再次抛出
        } finally {
          if (__DEV__) {
            // 恢复之前的 dispatcher
            ReactCurrentDispatcher.current = prevDispatcher;
          }
        }
      }
    }

    // ！！！将更新添加到队列中
    const root = enqueueConcurrentHookUpdate(fiber, queue, update, lane);
    if (root !== null) {
      // 获取事件发生时间
      const eventTime = requestEventTime();
      // ！！！调度 fiber 上的更新
      scheduleUpdateOnFiber(root, fiber, lane, eventTime);
      // 将过渡更新纠缠在一起
      entangleTransitionUpdate(root, queue, lane);
    }
  }

  // 在开发工具中标记更新
  markUpdateInDevTools(fiber, lane, action);
}
```