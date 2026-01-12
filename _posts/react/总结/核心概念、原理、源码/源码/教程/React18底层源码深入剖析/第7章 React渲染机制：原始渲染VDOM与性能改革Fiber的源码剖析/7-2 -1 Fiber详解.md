

本视频讲述了**React中的Fiber概念及其作用**，包括*其作为协调引擎在React 16中引入的背景、增量式渲染机制、工作单元拆分与调度策略*，并介绍了*Fiber对象的结构定义和核心属性*如tag、key等的作用及使用注意事项。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/98f37336ccf996f9a73b1f7b8cf1d8e6_MD5.webp]]

### 引言：Fiber的基本概念 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)

- 什么是Fiber  
    `Fiber`是React 16中的协调引擎，用于**优化组件的更新与渲染**过程。
- Fiber并非React独有  
    它是一个常见的计算机术语，例如在PHP、Ruby等领域也有类似实现。
- **Fiber的核心特性**  
    支持`中断`、`挂起`、`恢复执行`等能力，这些特性在React中同样适用。

### Fiber在React中的作用 
[01:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=114)

- 协调引擎（Reconciliation Engine）[02:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=121)
    - React 16开始引入Fiber作为新的协调机制。
    - `协调`即**对比（diff）新旧虚拟DOM的过程**。
- Update（更新）的概念 [02:57](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=177)
    - Update表示*一次状态变更引发的重新渲染*。
    - React和Vue都*围绕高效的Update设计核心API*。

### 引入Fiber的原因 
[05:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=325)

- `增量式渲染（Incremental Rendering）`[05:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=334)
    - **将渲染任务拆分成多个小块，分散在多个帧（时间切片）上进行处理**。
    - **避免长时间阻塞主线程**，提升应用响应性。
- **支持暂停、终止、复用工作单元（work）** [06:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=379)
    - 可以*取消不必要的中间更新*。
    - 给不同类型的work赋予优先级，支持优先级调度，提高并发性能。
- 时间切片（Time Slicing）[07:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=438)
    - 在指定时间内执行任务，超时则暂停并让出主线程。
    - 下次调度时可从上次中断处继续执行。
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/99b6ca0ca8ba164644893a36864979dc_MD5.webp]]

### 源码中的Fiber：Fiber对象的结构与类型 
[08:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=509)

- Fiber是工作单元（Unit of Work）[08:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=509)
    - **每个Fiber代表一个将要执行或已完成的工作单元**。
    - **一个组件有一个或多个Fiber**（如current和workInProgress）。
    - work：工作单元
    - `workInProgress`：正在执行的工作单元，*源码中对于正在执行的fiber，命名为workInProgress*
    - `current`：源码中使用current命名*旧的fiber，即已经执行完成了的work*
    - ![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/8aa515916ad14980e881887710ddaf26_MD5.webp]]
- Fiber的类型tag [11:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=669)
    - tag用于标记Fiber对应的组件类型，如函数组件（0）、类组件（1）、Fragment、Portal等。
    - tag值定义在`react-work-tags.js`中，当前范围为0到27。
- key的作用 [12:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=736)
    - key用于在协调阶段唯一标识组件。
    - 复用组件需满足三个条件：相同层级、相同类型、相同key。
    - key应具备稳定性和唯一性，不建议使用index作为key值。

### 总结与注意事项 
[15:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=904)

- Fiber的重要性
    - 是React 16之后版本实现并发模式的基础。
    - 支持更灵活的任务调度与优先级管理。
- Fiber对象的核心属性
    - 包括tag、key、child、sibling等字段。
    - 理解这些字段有助于深入理解React的更新机制。
- 开发建议
    - 使用稳定的key值来确保组件正确复用。
    - 避免使用index作为key，防止因顺序变化导致不稳定问题。