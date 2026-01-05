

本视频讲述了React中*状态管理的原理*及*表单状态管理的实现方式*，包括*内部状态与外部状态的区别、状态提升的优缺点、自定义第三方状态管理库的设计思路*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/8fbb70255183523ff4a9884d4ad3c782_MD5.webp]]

- 核心概念解释 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)
    - 视频围绕React中的状态管理展开，重点讲解组件状态的存储方式及其更新机制。
    - 提出问题：*如何实现类似ANT Design Form的状态管理？*
- 状态的类型分析 [00:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=26)
    - input框的状态*需要被多个组件访问*，因此不能仅作为其内部状态。
    - 组件间关系为“表兄弟”结构，无法通过后代通信获取状态。
- **状态提升方案**解析 [01:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=91)
    - 状态提升是将状态定义在公共父组件中，供多个子组件共享。
    - ANT Design *v3*采用此方法，*将状态定义在form层级*。
    - **缺点**是*当状态变化时，整个form组件都会重新渲染*，影响性能。
- **第三方状态管理方案**引入 [03:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=197)
    - 针对复杂表单场景，ANT Design *v4/v5改用外部状态管理*。
    - 类似Redux思想，*定义一个全局可读写且支持订阅的状态管理库*。
- **自定义FormStore类实现** [03:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=239)
    - 定义`class FormStore`用于统一管理表单状态。
    - 使用对象存储状态值，结构为name-value形式。
    - 实现get方法：
        - 可以获取全部状态或根据name获取特定项。
    - 实现set方法：
        - 接收新的状态对象进行合并更新。
    - 支持组件订阅状态变更事件：
        - 注册组件实例到`filterEntities`数组中。
        - 状态更新时仅触发相关组件的`forceUpdate`。
- 组件更新优化策略 [05:10](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=310)
    - 不再使用状态提升后，避免了全量更新的问题。
    - 每个组件只在自身关联的状态发生变化时才更新。
    - 利用name字段匹配机制精准触发对应组件更新。
- useForm自定义Hook的应用 [07:39](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=459)
    - `useForm`用于创建并维护`FormStore`实例。
    - 使用`useRef`保存状态管理库实例，确保跨渲染保持一致。
    - 实现组件更新时基于已有状态进行更新，而非重新初始化。
- 总结与归纳 [08:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=509)
    - 状态的本质是一个变量，其变化会触发组件更新。
    - 状态管理分为内部状态和外部状态：
        - 内部状态适用于组件自身使用。
        - 外部状态用于组件间共享。
    - 组件间状态共享可通过状态提升或第三方状态管理库实现。
    - 第三方状态管理库更适用于复杂组件结构和深层嵌套场景。