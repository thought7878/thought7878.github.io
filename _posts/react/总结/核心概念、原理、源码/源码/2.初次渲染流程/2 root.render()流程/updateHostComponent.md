更新宿主组件（Host Component）：
- 获取子ReactElement
- **协调生成子 Fiber 节点**：基于新的 ReactElement和现有的current Fiber，创建或更新对应的 Fiber 节点，构建新的 work-in-progress Fiber 树
- 对于直接文本子节点进行特殊处理
- 返回生成的workInProgress.child子 Fiber 节点，继续下一个循环，构建fiber树


## 源码

```ts
/**
 * 更新宿主组件（Host Component），如 DOM 元素
 * 这个函数处理原生 UI 组件（在 Web 环境中是 DOM 节点）的更新过程
 *
 * @param {Fiber | null} current - 当前已挂载的 fiber 节点，如果首次渲染则为 null
 * @param {Fiber} workInProgress - 正在处理的工作中 fiber 节点
 * @param {Lanes} renderLanes - 当前渲染任务的优先级通道
 * @returns {Fiber | null} 返回处理后的子 fiber 节点
 */
function updateHostComponent(
  current: Fiber | null,
  workInProgress: Fiber,
  renderLanes: Lanes,
) {
  pushHostContext(workInProgress);

  // 在初始渲染时，尝试声明下一个可水合实例
  if (current === null) {
    tryToClaimNextHydratableInstance(workInProgress);
  }

  const type = workInProgress.type;
  const nextProps = workInProgress.pendingProps;
  const prevProps = current !== null ? current.memoizedProps : null;

  let nextChildren = nextProps.children;
  const isDirectTextChild = shouldSetTextContent(type, nextProps);

  if (isDirectTextChild) {
    // 对于宿主节点的直接文本子节点进行特殊处理。这是一个常见情况。
    // 我们不会将其作为具体化的子节点处理。而是在同样可以访问此属性的宿主环境中处理。
    // 这样可以避免分配另一个HostText fiber并遍历它。
    nextChildren = null;
  } else if (prevProps !== null && shouldSetTextContent(type, prevProps)) {
    // 如果我们从直接文本子节点切换到普通子节点，或切换为空，
    // 我们需要安排重置文本内容。
    workInProgress.flags |= ContentReset;
  }

  // 标记ref的变化
  markRef(current, workInProgress);
  // ！！！协调子节点，构建或更新子节点的fiber树
  reconcileChildren(current, workInProgress, nextChildren, renderLanes);
  // ！！！返回生成的子 Fiber 节点，继续下一个循环，构建fiber树
  return workInProgress.child;
}

```