

本视频讲述了React源码中*Fiber节点的tag属性*如何标识不同类型组件，包括*函数组件、类组件、原生标签、文本节点、Fragment*等，并*通过debug React演示了各组件在Fiber树中的结构与tag值对应关系*，强调*利用tag进行类型判断*和调试的方法。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/de4d43fd0f2a820812127fe9c9b19229_MD5.webp]]

- 回顾Fiber的tag属性作用 [00:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=7)
    
    - Fiber上的`tag`属性用于标记组件的类型。
    - 不同类型的组件对应不同的tag值，便于后续更新时快速识别，避免重复复杂的类型判断。
- 查看workTag定义的位置 [00:33](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=33)
    
    - `workTag` 定义在 `react-work-tags.js`文件中。
    - 当前最新代码中共有28种取值（0到27，以及71）。
- 常见组件类型的tag值 [00:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=59)
    
    - 函数组件 [00:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=59)
        - tag值为 `0`。
    - 类组件 [01:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=61)
        - tag值为 `1`。
        - 判断方式：检查`type.prototype`上是否存在`isReactComponent`属性。
    - IndeterminateComponent [01:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=65)
        - tag值为 `2`。
        - 表示组件类型尚未确定，作为初始值使用。
- 与DOM相关联的Host组件类型 [02:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=139)
    
    - 这些组件均带有“host”前缀，表示与原生DOM节点关联。
    - HostComponent（原生标签） [02:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=139)
        - 如`div`、`p`等，tag值为 `5`。
    - HostText（文本节点） [05:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=319)
        - 纯文本内容节点，tag值为 `6`。
        - 示例：JSX中独立的字符串如`"123"`会被视为文本节点；而单一子文本如`<h1>OMG</h1>`中的`OMG`则作为`h1`的属性处理，不生成独立文本节点。
    - HostRoot [04:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=247)
        - 根Fiber节点，对应整个React应用挂载的根容器。
        - 与原生DOM根节点关联，确保渲染结果可被用户查看。
    - HostPortal [04:58](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=298)
        - Portal机制使用的节点，允许将子节点渲染到当前DOM层级之外的目标节点。
    - HostFragment [02:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=144)
        - Fragment对应的tag值为 `7`。
- 其他特殊组件类型 [12:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=772)
    
    - StrictMode [12:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=772)
        - 严格模式组件，具有特殊的开发期检查行为。
    - Context相关的Consumer和Provider [12:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=774)
        - `Consumer` 和 `Provider` 均为独立组件类型。
    - ForwardRef [13:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=806)
        - 使用`React.forwardRef`创建的组件。
    - Memo组件 [13:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=811)
        - 包括：
            - `MemoComponent`：常规的`React.memo`包装组件。
            - `SimpleMemoComponent`：简化版memo组件，无`compare`函数或默认props，路径更简单。
    - Suspense [13:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=809)
        - 异步加载边界组件。
    - Profiler [14:33](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=873)
        - 性能分析组件，仅在开发环境有效。
    - IncompleteComponent [14:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=857)
        - 类似类组件但未完成初始化的状态。
    - Scope 和 OffscreenComponent [14:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=857)
        - 实验性或高级用途组件。
- Fiber Root与普通Fiber的区别 [08:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=489)
    
    - Fiber Root [08:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=489)
        - 指向整个Fiber树的根对象，是内部实现的一部分。
        - 其`tag`字段使用的是`rootTag`而非`workTag`。
    - rootTag的取值 [09:03](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=543)
        - `0`：Legacy模式（传统同步渲染）。
        - `1`：Concurrent模式（并发渲染）。
        - 当前默认为`0`（Legacy模式）。
- 通过debug React查看实际Fiber树结构 [07:47](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=467)
    
    - 在项目中添加`console.log`打印根节点的Fiber对象。
    - 打印顺序从`fiberRootNode`开始，逐层展开子节点。
    - 实际观察到的节点顺序与组件树一致：
        - 根节点 → `HostRoot`
        - 子节点依次为：
            - `HostComponent (div)`tag=5
            - `HostComponent (h1)` tag=5
                - 其子文本`OMG`未生成独立`HostText`节点，而是作为属性存储
            - `HostText ("123")` tag=6
            - `FunctionComponent` tag=0
            - 外层`FunctionComponent`
            - `ClassComponent` tag=1
            - `Fragment` tag=7
            - `StrictMode`
            - `Consumer` / `Provider` 等context组件
            - `MemoComponent` / `SimpleMemoComponent`
- 如何在开发中调试组件类型 [15:39](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=939)
    
    - 可根据Fiber节点的`tag`值判断其组件类型。
    - 结合源码中的常量定义（如`workTags`文件）进行对照。
    - 推荐使用`debug-react`项目，在关键路径插入`console.log`辅助分析执行流程。
- 当前版本可用性说明 [14:47](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=887)
    
    - 并非所有定义的组件类型都已对开发者开放。
    - 例如`TracingSuspendedComponent`（the best list）在React 18.2版本中仍未正式启用，仅存在于源码中。
- 总结与后续学习建议 [16:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=977)
    
    - 掌握`tag`值是理解React协调过程的基础。
    - 后续课程将深入讲解`beginWork`、`workLoop`等核心文件的执行逻辑。
    - 建议自行编写多种组件组合的页面，结合debug工具观察其Fiber结构。