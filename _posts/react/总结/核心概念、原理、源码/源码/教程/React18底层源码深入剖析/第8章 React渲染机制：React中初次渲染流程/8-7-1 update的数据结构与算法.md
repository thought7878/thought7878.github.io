本视频讲述了React中**类组件和createRoot(rootDOM).render()渲染产生的update的数据结构与算法**，详细解析了*update的类型定义、创建过程、入队机制及更新队列的管理流程*，并介绍了*其在Fiber架构下的链表存储与调度机制*。

## 课程前提与范围说明 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)

- 讲解范围限定  
    本节课聚焦于*类组件和 `createRoot` 函数产生的 update *数据结构。
- *函数组件的排除*  
    函数组件通过 Hooks 产生的 update 将在其他章节单独讲解，不包含在本次内容中。

## Update、SharedQueue、Update Queue类型定义
### Update 类型定义 
[00:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=40)

`packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js`

[[Update 类型定义]]

---

- Update 对象结构  
    update 是一个对象，包含多个属性用于描述更新的元信息。
- `lane` 属性[00:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=46)  
    *表示该 update 的优先级*，**决定了其执行时机**。
- `tag` 属性 [00:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=59)  
    *标记 update 的类型*，React 源码中定义了*四种：*
    - UpdateState（页面初次渲染）
    - ReplaceState
    - ForceUpdate
    - CaptureUpdate
- `payload` 属性 [01:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=85)  
    *携带的更新参数*。
    - 初次渲染时，payload 为子节点（如 React Element）。
    - 类组件调用 setState 时，payload 为状态修改值或函数。
    - ForceUpdate 类型无 payload。
- `callback` 属性 [01:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=114)  
    更新完成后的回调函数。
    - 旧版 render 支持 callback，但在 React 18 中已不再支持。
    - setState 等方法仍可携带 callback。
- next 属性 [02:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=136)  
    **指向下一个 update**，构成单链表结构，用于形成 update 队列。


### SharedQueue、UpdateQueue 类型定义
[02:30](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=150)

`packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js`

参考：[[SharedQueue、UpdateQueue 类型定义]]

---

- **为何需要 UpdateQueue？**
    *避免每个 update 单独提交带来的高开销，采用批量更新、合并提交策略*。
- 类比：打鱼与集市  
    多个 update 如同捕获的鱼，需暂存“池塘”（即 `UpdateQueue`），积累后统一处理。
- *UpdateQueue 结构* [05:03](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=303)  
    使用单向循环链表存储 update，关键字段包括：
    - firstBaseUpdate：指向第一个待处理的 update。
    - lastBaseUpdate：指向最后一个待处理的 update。
    - shared.pending：当前正在收集的 update 链表头。
        - shared.pending 构成单向循环链表：尾节点的 `next` 指向头节点。
- baseState 与 shared 状态 [05:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=323)
    - `baseState`：基础状态（如初始 state 或 element）。
    - `shared`：*新进入的 update 先暂存于此，后续合并至主队列*。



## initializeUpdateQueue：初始化 fiber.updateQueue  
[07:58](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=478)

参考：[[initializeUpdateQueue]]

---

**初次渲染页面**和**类组件初次挂载**的时候，**调用函数initializeUpdateQueue来初始化 fiber.updateQueue**。

- **初始化时机** [08:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=498)  
    *在 createRoot 执行*或*类组件初次挂载时*进行。
- 初始化函数  
    initializeUpdateQueue(fiber)，*为 Fiber 节点创建空的 updateQueue*。
- **初始化内容** [09:12](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=552)
    - baseState 赋初值：
        - **初次渲染**：赋值为传入的 element。
        - 类组件：赋值为初始 state。
    - shared.pending、firstBaseUpdate、lastBaseUpdate等均为 null。
    - callbacks 为空数组。

初次渲染页面（createRoot流程）：
`packages/react-reconciler/src/ReactFiberRoot.new.js`
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/127e2140bbc13f91dc014e8aa2b4e4a7_MD5.webp]]

类组件初次挂载：
`packages/react-reconciler/src/ReactFiberClassComponent.new.js`
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/cc8a7b8bb2ed5db48f24a5d9e43c7f27_MD5.webp]]

## createUpdate：创建Update
[09:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=478)

调用：`packages/react-reconciler/src/ReactFiberReconciler.new.js`
实现：`packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js`

参考：[[createUpdate()]]、[[2.1 createUpdate]]

### 触发创建的场景
- `root.render()-->updateContainer()`：创建初次渲染的 update。
- 类组件调用 `this.setState()`、`forceUpdate()`：创建状态更新的 update。

初次渲染（root.render()-->updateContainer()）：
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/be6fb979131861b44c2c6667b0db3b1b_MD5.webp]]

类组件调用 `this.setState()`、`forceUpdate()`：
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/dcd4a1107cddc35d4dc471b1b1c87a97_MD5.webp]]

### 创建函数  
[10:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=654)

- `forceUpdate` 会将 `tag` 设为 `ForceUpdate`。
- `setState` 的参数赋给 `payload`。
    - 回调函数赋给 `callback`。

## enqueueUpdate：Update 入队
[12:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=746)

调用：`packages/react-reconciler/src/ReactFiberReconciler.new.js`的updateContainer()
实现：`packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js`

参考：[[2.2 enqueueUpdate]]

### 调用的场景
- `root.render()-->updateContainer()`：初次渲染，调用enqueueUpdate。
- 类组件调用 `this.setState()`、`forceUpdate()`：调用enqueueUpdate。

参考上面的截图


---

- **入队函数调用链**
    - 外层调用 enqueueUpdate(fiber, update)。
    - 最终执行 enqueueConcurrentClassUpdate(fiber, update, lane)。
- ***全局缓存结构 ConcurrentQueues*** [15:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=940)  
    *使用全局数组 concurrentQueues 暂存 update，避免过早操作 Fiber 树*。
    - 结构为 \[fiber, queue, update, ...\]。
    - 记录当前下标，便于批量处理。
- **入队核心逻辑** [16:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1010)
    - 将 update 添加到 shared.pending 循环链表中。
    - 若 shared.pending 为空，update.next = update 自循环。
    - 否则插入链表末尾，保持循环结构。
- 位运算合并优先级 [17:10](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1030)  
    使用按位或 (`|`) 运算合并所有 update 的 lane，得出整体优先级。


### enqueueConcurrentClassUpdate

参考：[[2.2.1 enqueueConcurrentClassUpdate]]

## finishQueueingConcurrentUpdates：管理更新队列
[18:02](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1082)
initializeUpdateQueue--->createUpdate--->enqueueUpdate--->scheduleUpdateOnFiber--->performConcurrentWorkOnRoot--->renderRootSync--->*finishQueueingConcurrentUpdates*--->workLoopSync--开始循环->performUnitOfWork--->beginWork--->processUpdateQueue--->reconcile--循环结束->*finishQueueingConcurrentUpdates*--->commitRootImpl

finishQueueingConcurrentUpdates 把 concurrentQueues的内容添加到fiber的updateQueue中。在render阶段，有两处调用 finishQueueingConcurrentUpdates，分别是：
- 1.render开始的时候，在 prepareFreshStack 函数中。packages/react-reconciler/src/ReactFiberWorkLoop.new.js：prepareFreshStack
- ~~2.在renderRootSync()结束的时候，最后再调用一遍。~~*18.2.0中只有上面的没有这个*

---

- 消费时机  
    在 `render` 阶段开始时调用 `finishQueueingConcurrentUpdates()`。
- 调用位置 [19:44](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1184)
    - `prepareFreshStack()`：`render` 开始前。
    - `renderRootSync()` 结束时再次调用，防止遗漏。
- 消费流程
    - 遍历 `concurrentQueues` 全局数组。
    - 将暂存的 update 从 `shared.pending`移至 `updateQueue` 的 `baseUpdate` 队列。
    - 建立 `firstBaseUpdate` 和 `lastBaseUpdate` 链表结构，供 `reconcile` 阶段使用。

参考：[[finishQueueingConcurrentUpdates]]

## 补充说明与跳过内容 
[07:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=427)

- hiddenCallbacks 与 Activity [07:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=427)
    - `hiddenCallbacks` 与 `Offscreen` 组件相关，现改名为 `Activity`。
    - 当前实现为 `unstable`，课程中跳过此部分逻辑。