
# 夸克总结

本视频讲述了**React框架的三大核心规则：** `组件化`、`状态管理`和`单向数据流`，并*通过与Vue的对比说明了React的设计理念和不可变数据的修改方式*，以及*相关工具库如Redux和MobX如何简化开发流程*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/be2355e92cae62ba491bb5ce98a2ce4e_MD5.webp]]

### React 三大核心规则概述 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)

- 三大规则：
    - 组件化
    - 状态管理
    - 单向数据流

### 与 Vue 的对比：响应式 vs 单向数据流 
[00:10](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=10)

- Vue 的响应式特性
    - 定义变量（如 `message`）后可直接修改。
    - 不需要额外事件处理机制。
- React 的限制
    - 不能直接修改状态值。
    - 必须使用 `setState` 或类似方法进行更新。

### React 的单向数据流模型 
[00:42](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=42)

- 不可变数据原则
    - 状态值是不可变的。
    - 修改必须通过指定方法（如 `setInput`）。
- Redux 的类比说明 [01:13](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=73)
    - Redux 是 React 常用的状态管理库。
    - 修改状态必须通过 reducer。
    - Reducer 是纯函数，不能直接修改旧值，而是返回新值。

### React 学习成本与改进方案 
[02:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=145)

- 常见吐槽点
    - 不允许直接修改状态，学习成本高。
    - 开发体验不如 Vue 直观。
- 解决方案与响应式支持 [02:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=170)
    - MobX：提供响应式编程模型，搭配 React 使用。
    - Redux Toolkit + Immer：
        - 允许用户以“可变”方式操作状态。
        - 底层仍基于 Redux 的不可变数据流。
        - 实际是封装了 Immer 来生成新的不可变状态。

### React 编程的核心规则总结 
[04:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=269)

- 基础模型
    - 默认遵循不可变数据 + 单向数据流。
- 现代工具支持
    - 使用 Immer 或 MobX 可实现“响应式”修改。
    - 但其底层逻辑未改变，只是提升了开发者体验。

### React 编写建议与三大规则回顾 
[04:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=296)

- 组件化设计要点
    - 组件颗粒度小。
    - 耦合性低。
    - 数据流通性强。
- 数据流机制
    - 包括 props、context 和 state。
- 状态定义判断标准
    - 判断一个值是否应作为状态。
    - 需结合具体场景和生命周期行为。
- 强调单向数据流的重要性
    - 与 Vue 的双向绑定形成鲜明对比。
    - React 强调不可变性和可控的数据流向。

### 课程总结与结束语 
[05:55](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=355)

- 强调要“用 React 的方式”编写代码。
- 推荐使用周边生态工具提升开发效率。
- 保持对 React 核心思想的理解与实践。