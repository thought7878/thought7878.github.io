## 创建 Fiber 节点：根据已有 ReactElement

```js
// 创建ReactElement对应的Fiber节点
export function createFiberFromElement(element) {
  let tag = null;
  const { type, props, key } = element;
  if (typeof type === "string") {
    tag = HostComponent;
  } else if (typeof type === "function") {
    if (type.isReactComponent) {
      tag = ClassComponent;
    } else {
      tag = FunctionComponent;
    }
  }

  const fiberNode = createFiber(tag, props, key);
  fiberNode.type = element.type;
  return fiberNode;
}
```

## 创建 Fiber 节点：根据已有 Fiber 节点

[03:30]

`stateNode`属性：当前 Fiber 节点对应的真实 DOM 节点。

**根据已有 Fiber 节点，创建新的 Fiber 节点，应用场景**：_首次渲染后的第一次更新_，此时内存中只有一棵 Fiber 树。current Fiber 节点还没有 alternate 属性，就根据 current Fiber 节点创建一个新的副本 Fiber 节点。

***React每次更新时*，都会调用这个函数，根据老的Fiber节点，创建工作节点（新的节点）**

```js
/**
 * React每次更新时，都会调用这个函数，根据老的Fiber节点，创建工作节点
 *
 * 创建一个用于工作的 Fiber 节点（workInProgress Fiber）。
 * workInProgress Fiber 是当前正在构建的新 Fiber 树中的节点，与当前渲染的 Fiber 树（current Fiber 树）相对应。
 * @param {FiberNode} current - 当前渲染的 Fiber 树中的对应节点（老节点）。
 * @param {Object} pendingProps - 新的待处理属性。
 * @returns {FiberNode} - 返回用于工作的新的 Fiber 节点。
 */
export function createWorkInProgress(current, pendingProps) {
  // 尝试从 current Fiber 节点的 alternate 属性获取 workInProgress Fiber 节点
  let workInProgress = current.alternate;

  // 如果 alternate 不存在，说明还没有创建 workInProgress Fiber 节点
  if (workInProgress === null) {
    // 使用 createFiber 函数创建一个新的 Fiber 节点，继承 current 节点的 tag、key 和新的 pendingProps
    workInProgress = createFiber(current.tag, pendingProps, current.key);
    // 将 current 节点的 stateNode 赋值给 workInProgress 节点，stateNode 通常存储 DOM 节点或组件实例
    workInProgress.stateNode = current.stateNode;
    // 建立 workInProgress 节点和 current 节点的双向关联
    workInProgress.alternate = current;
    current.alternate = workInProgress;
  } else {
    // 如果 alternate 存在，说明已经有 workInProgress Fiber 节点，更新其 pendingProps
    workInProgress.pendingProps = pendingProps;
  }

  // 同步 current 节点的 type 到 workInProgress 节点，type 表示节点对应的组件类型或 DOM 标签名
  workInProgress.type = current.type;
  // 同步 current 节点的 child 到 workInProgress 节点，child 指向第一个子 Fiber 节点
  workInProgress.child = current.child;
  // 同步 current 节点的更新队列到 workInProgress 节点
  workInProgress.updateQueue = current.updateQueue;

  // 同步 current 节点的 lanes 和 childLanes 到 workInProgress 节点
  // lanes 用于表示任务的优先级
  workInProgress.lanes = current.lanes;
  workInProgress.childLanes = current.childLanes;

  // 返回创建好的 workInProgress Fiber 节点
  return workInProgress;
}
```

## 相关资料

- [创建一个 Fiber 节点](https://www.bilibili.com/video/BV1C9gDeNEzF/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)
