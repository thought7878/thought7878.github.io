
这是一个关键的React内部函数，用于创建一个工作中的fiber副本，以便进行更新操作。React使用双缓冲技术，因此同一时间最多只需要两个版本的fiber树：一个当前渲染的树和一个正在构建的工作树。

函数的主要功能包括：

1. 首先检查是否已有备用fiber(alternate)，如果没有则创建一个新的
2. 如果有现有备用fiber，则复用它并重置相关属性
3. 复制必要的属性到工作中的fiber
4. 重置effect标记和其他状态信息
5. 在开发模式下处理热重载需求

注释中还特别解释了React的双缓冲池化技术、effect标志重置的原因以及为什么需要克隆依赖对象等重要概念。整个函数的目的是确保能够安全地执行fiber的更新工作，同时不影响当前渲染的UI。


## 源码


```ts
/**
 * 构建（新建或复用）一个current fiber的workInProgress/alternate fiber，
 * React使用双缓冲技术，同一时间最多只需要两个版本的fiber树：
 * 一个是当前渲染完成的树(current)，另一个是正在构建的工作树(workInProgress)
 * 
 * @param {Fiber} current - 当前已渲染的fiber节点
 * @param {any} pendingProps - 待处理的新属性
 * @returns {Fiber} 返回一个用于工作的fiber副本
 */
export function createWorkInProgress(current: Fiber, pendingProps: any): Fiber {
  let workInProgress = current.alternate;
  if (workInProgress === null) {
    // 使用双缓冲池化技术，因为最多只需要两棵树的版本
    // 池化未使用的备用节点以供重复利用
    // 这是懒创建的，以避免为从不更新的内容分配额外对象
    // 如果需要也可以回收额外内存
    workInProgress = createFiber(
      current.tag,
      pendingProps,
      current.key,
      current.mode,
    );
    // 复制必要的属性到新的fiber节点
    workInProgress.elementType = current.elementType;
    workInProgress.type = current.type;
    workInProgress.stateNode = current.stateNode;

    if (__DEV__) {
      // 开发模式下的调试信息
      workInProgress._debugSource = current._debugSource;
      workInProgress._debugOwner = current._debugOwner;
      workInProgress._debugHookTypes = current._debugHookTypes;
    }

    // 建立双向链接：当前fiber指向工作中的fiber，工作中的fiber也指向当前fiber
    workInProgress.alternate = current;
    current.alternate = workInProgress;
  } else {
    // 复用现有的备用fiber
    workInProgress.pendingProps = pendingProps;
    // 因为Blocks在type上存储数据，所以需要重置
    workInProgress.type = current.type;

    // 我们已经有一个备用fiber了
    // 重置effect标签
    workInProgress.flags = NoFlags;

    // 效果不再有效
    workInProgress.subtreeFlags = NoFlags;
    workInProgress.deletions = null;

    if (enableProfilerTimer) {
      // 我们有意重置actualDuration和actualStartTime，而不是复制它们
      // 这样可以防止时间在新提交中无限累积
      // 这样做的缺点是会重置不同优先级渲染的值
      // 但对让出时间片(常见情况)有效，并且应该支持恢复
      workInProgress.actualDuration = 0;
      workInProgress.actualStartTime = -1;
    }
  }

  // 重置除静态效果外的所有效果
  // 静态效果不是特定于某次渲染的
  workInProgress.flags = current.flags & StaticMask;
  // 继承子树的优先级车道
  workInProgress.childLanes = current.childLanes;
  // 继承当前的优先级车道
  workInProgress.lanes = current.lanes;

  // 复用现有的子节点、记忆化的props/state以及更新队列
  workInProgress.child = current.child;
  workInProgress.memoizedProps = current.memoizedProps;
  workInProgress.memoizedState = current.memoizedState;
  workInProgress.updateQueue = current.updateQueue;

  // 克隆依赖对象，因为它在渲染阶段会被修改
  // 所以不能与当前fiber共享
  const currentDependencies = current.dependencies;
  workInProgress.dependencies =
    currentDependencies === null
      ? null
      : {
          lanes: currentDependencies.lanes,
          firstContext: currentDependencies.firstContext,
        };

  // 这些将在父级的协调过程中被覆盖
  workInProgress.sibling = current.sibling;
  workInProgress.index = current.index;
  workInProgress.ref = current.ref;

  if (enableProfilerTimer) {
    // 继承基准持续时间，用于性能分析
    workInProgress.selfBaseDuration = current.selfBaseDuration;
    workInProgress.treeBaseDuration = current.treeBaseDuration;
  }

  if (__DEV__) {
    // 开发模式下检查是否需要重新挂载
    workInProgress._debugNeedsRemount = current._debugNeedsRemount;
    // 在开发模式下为热重载解析组件类型
    switch (workInProgress.tag) {
      case IndeterminateComponent:
      case FunctionComponent:
      case SimpleMemoComponent:
        workInProgress.type = resolveFunctionForHotReloading(current.type);
        break;
      case ClassComponent:
        workInProgress.type = resolveClassForHotReloading(current.type);
        break;
      case ForwardRef:
        workInProgress.type = resolveForwardRefForHotReloading(current.type);
        break;
      default:
        break;
    }
  }

  return workInProgress;
}

```