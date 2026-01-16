

本视频讲述了**调度更新函数 `scheduleUpdateOnFiber`的作用与核心逻辑**，包括*其在页面初次渲染和组件更新中的应用*，并重点*解析了标记根节点更新*（`markRootUpdated`）及后续调度流程。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/b22240950692036d1f85a791a382e78d_MD5.webp]]

### 主要作用、调用场景
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)

- **主要作用**  
    用于**调度 React 中的更新操作**。
- **常见调用场景**
    - 页面*初次渲染*时触发的更新。
    - 类组件中，通过 `setState` 触发更新。
    - 函数组件中，使用 `useState` 或 `useReducer` 返回的更新函数（如 `setData`）。

### 源码定位与参数说明 
[01:02](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=62)

- 定义文件位置  
    `workLoop` 文件中，位于 `react-fiber-workloop` 模块。
- 接收参数说明
    - `root`: Fiber 根节点（`FiberRoot`），表示整个 React 应用的根。
    - `fiber`: 当前要更新的 Fiber 节点。
    - `lane`: 优先级相关参数，用于标识此次更新的优先级。

### 核心逻辑概述 
[02:11](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=131)

- 两个关键函数
    - `markRootUpdated`：标记该根节点有更新待处理。
    - `ensureRootIsScheduled`：确保该根节点的更新被安排进调度器。

### markRootUpdated 函数详解 
[02:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=145)

- 函数作用  
    标记指定的 `root` 正在进行更新（即存在 pending update）。
- 参数传递  
    接收 `root` 和当前更新的优先级 `lane`。
- 实现机制
    - 调用 `_markRootUpdated` 函数。
    - 使用位运算将当前 `lane` 添加到 `root.pendingLanes` 上，而非直接赋值。
    - 避免覆盖已有的其他优先级更新。

### 关于 pending update 的术语说明 
[03:21](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=201)

- pending update  
    表示正在处理但尚未完成的更新，对应英文术语为 "in progress"。
- 已完成的更新  
    对应英文术语为 "completed"。
- React 源码中状态区分  
    经常会用 “ing” 形式表示正在进行的状态。

### 循环次数限制与错误提示 
[03:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=232)

- 类组件最大更新次数限制  
    最多执行 50 次更新，超过则抛出错误。
- 函数组件最大更新次数限制  
    最多执行 25 次更新。
- 防止死循环机制  
    在 `componentDidUpdate` 中直接调用 `setState` 可能导致无限更新，此限制可避免浏览器卡死。

### 二进制掩码运算说明 
[05:15](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=315)

- 用途  
    多个优先级（lanes）合并处理，避免相互覆盖。
- 建议学习内容
    - 二进制掩码（mask）概念。
    - 按位或（`|`）、按位与（`&`）等位运算。
    - lane 优先级系统如何通过位运算高效管理多个并发更新。

### idle 状态更新类型 
[06:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=377)

- 术语解释  
    idle update 表示低优先级的闲置更新。
- 处理时机  
    在所有高优先级更新完成后才会被执行。
- 适用场景  
    如非紧急 UI 更新、后台数据加载等对响应速度要求不高的任务。

### 下阶段预告 
[06:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=406)

- 下一部分讲解内容  
    `ensureRootIsScheduled` 函数的具体实现及其在调度系统中的作用。