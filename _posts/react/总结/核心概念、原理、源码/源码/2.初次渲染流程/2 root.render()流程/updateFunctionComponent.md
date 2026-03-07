主要功能：
- **执行组件函数体**：获取新的ReactElement，处理所有 hooks 调用
- **如果无需更新则提前退出(bailout)**：如果当前组件没有接收到任何更新（即 props/state 没有变化），可以跳过后续的协调过程，直接复用之前的渲染结果
- **协调生成子 Fiber 节点**：基于新生成的 ReactElement和现有的 Fiber 树 (current)，创建或更新对应的 Fiber 节点，构建新的 work-in-progress Fiber 树
- 返回生成的workInProgress.child子 Fiber 节点，继续下一个循环，构建fiber树


## 源码

```ts
/**
 * 更新函数式组件的核心处理函数
 *
 * 该函数负责完整地处理函数式组件的更新流程，包括：
 * 1. Props 验证（仅开发环境）
 * 2. 上下文(context)的获取与处理
 * 3. Hooks 的执行与管理
 * 4. 严格模式下的双重渲染检测
 * 5. hydration 过程中的特殊处理
 * 6. 子节点的协调(reconciliation)
 *
 * @param {Fiber | null} current - 当前已挂载的 Fiber 节点，null 表示首次挂载
 * @param {Fiber} workInProgress - 正在处理的 Fiber 节点，表示当前工作单元
 * @param {Function} Component - 要更新的函数式组件构造函数
 * @param {Object} nextProps - 组件接收的新属性对象
 * @param {Lanes} renderLanes - 表示本次渲染优先级的车道(lane)位图
 *
 * @returns {Fiber | null} 返回处理后的子 Fiber 节点，用于继续构建 Fiber 树
 *
 * @description
 * 函数式组件的更新遵循以下主要步骤：
 * 1. 在开发环境中验证组件的 propTypes
 * 2. 获取并处理 legacy context（如果启用）
 * 3. 准备上下文读取环境
 * 4. 执行组件函数体，处理所有 hooks 调用
 * 5. 在严格模式下进行二次渲染以检测潜在的副作用
 * 6. 如果无需更新则提前退出(bailout)
 * 7. 处理 hydration 场景下的 ID 相关逻辑
 * 8. 标记组件已完成工作
 * 9. 协调生成子 Fiber 节点
 *
 * @example
 * // 典型调用场景
 * const childFiber = updateFunctionComponent(
 *   currentFiber,
 *   workInProgressFiber,
 *   MyFunctionComponent,
 *   { name: 'example' },
 *   DefaultLane
 * );
 */
function updateFunctionComponent(
  current,
  workInProgress,
  Component,
  nextProps: any,
  renderLanes,
) {
  // ========== 开发环境：Props 类型验证 ==========
  // 对于动态解析的组件（如 lazy 加载的组件），需要在此处进行 props 验证
  // 因为在 createElement 阶段无法保证组件类型已被完全解析
  if (__DEV__) {
    if (workInProgress.type !== workInProgress.elementType) {
      const innerPropTypes = Component.propTypes;
      if (innerPropTypes) {
        checkPropTypes(
          innerPropTypes,
          nextProps, // 已解析的 props
          'prop',
          getComponentNameFromType(Component),
        );
      }
    }
  }

  // ========== 上下文(Context)处理 ==========
  // 处理传统的 legacy context 系统
  // 注意：现代 context 应使用 useContext Hook
  let context;
  if (!disableLegacyContext) {
    // 获取未屏蔽的完整 context
    const unmaskedContext = getUnmaskedContext(workInProgress, Component, true);
    // 根据组件需求获取屏蔽后的 context
    context = getMaskedContext(workInProgress, unmaskedContext);
  }

  // 将存储渲染结果ReactElement的变量
  let nextChildren;
  // 标记是否在组件中使用了 useId Hook
  let hasId;

  // ========== 渲染准备阶段 ==========
  // 准备读取 context，设置必要的内部状态
  prepareToReadContext(workInProgress, renderLanes);

  // 如果启用了调度性能分析器，标记组件渲染开始
  if (enableSchedulingProfiler) {
    markComponentRenderStarted(workInProgress);
  }

  // ========== 执行组件函数并处理 Hooks ==========
  if (__DEV__) {
    // 开发环境专用：设置当前正在渲染的组件，用于错误堆栈追踪
    ReactCurrentOwner.current = workInProgress;
    setIsRendering(true);

    // ！！！第一次执行函数组件，触发所有 Hook 调用
    nextChildren = renderWithHooks(
      current,
      workInProgress,
      Component,
      nextProps,
      context,
      renderLanes,
    );
    hasId = checkDidRenderIdHook();

    // 严格模式下的副作用检测
    // React 会故意进行二次渲染来帮助开发者发现意外的副作用
    if (
      debugRenderPhaseSideEffectsForStrictMode &&
      workInProgress.mode & StrictLegacyMode
    ) {
      // 通知开发工具进入严格模式
      setIsStrictModeForDevtools(true);
      try {
        // ！！！二次执行组件函数
        nextChildren = renderWithHooks(
          current,
          workInProgress,
          Component,
          nextProps,
          context,
          renderLanes,
        );
        hasId = checkDidRenderIdHook();
      } finally {
        // 恢复严格模式状态
        setIsStrictModeForDevtools(false);
      }
    }
    // 结束渲染状态
    setIsRendering(false);
  } else {
    // ！！！生产环境：直接执行组件函数
    // 这是应用正常运行时的路径，性能最优
    nextChildren = renderWithHooks(
      current,
      workInProgress,
      Component,
      nextProps,
      context,
      renderLanes,
    );
    hasId = checkDidRenderIdHook();
  }

  // 标记组件渲染结束（性能分析）
  if (enableSchedulingProfiler) {
    markComponentRenderStopped();
  }

  // ========== 更新优化：Bailout 检查 ==========
  // 如果当前组件没有接收到任何更新（即 props/state 没有变化）
  // 可以跳过后续的协调过程，直接复用之前的渲染结果
  if (current !== null && !didReceiveUpdate) {
    // 清理不需要的 hooks 状态
    bailoutHooks(current, workInProgress, renderLanes);
    // 返回已经完成的工作，避免重复工作
    return bailoutOnAlreadyFinishedWork(current, workInProgress, renderLanes);
  }

  // ========== Hydration 特殊处理 ==========
  // 在服务端渲染(SSR)的 hydration 过程中，如果组件使用了 useId Hook
  // 需要推送 materialized tree ID 来保证客户端和服务端的 ID 一致性
  if (getIsHydrating() && hasId) {
    pushMaterializedTreeId(workInProgress);
  }

  // ========== 完成工作标记 ==========
  // 设置 PerformedWork 标志位，告知 React DevTools
  // 这个组件在本次渲染周期中实际进行了工作
  workInProgress.flags |= PerformedWork;

  // ========== 子节点协调 ==========
  // 核心协调算法：
  // 基于新生成的 React Elements (nextChildren) 和现有的 Fiber 树 (current)
  // 创建或更新对应的 Fiber 节点，构建新的 work-in-progress Fiber 树
  reconcileChildren(current, workInProgress, nextChildren, renderLanes);

  // ！！！返回生成的子 Fiber 节点，继续下一个循环，构建fiber树
  return workInProgress.child;
}

```