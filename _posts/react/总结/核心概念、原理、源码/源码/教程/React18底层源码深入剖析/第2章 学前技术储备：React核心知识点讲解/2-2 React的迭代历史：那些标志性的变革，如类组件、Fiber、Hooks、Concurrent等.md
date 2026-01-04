

# 夸克总结

本视频讲述了**React的版本迭代历史**，重点介绍了：
- `16.3`*引入Fiber架构*、
- `16.8`正式*推出Hooks*带来的函数式编程变革、
- `17`作为过渡版本的功能调整
- `18`引入*Concurrent模式*和支持*自动批量更新*、*Transition机制*等核心特性。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/210ca24c28c4b7d0fe304ba5cd40f5b3_MD5.webp]]

### React版本迭代概述 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)

- 背景介绍  
    React自2013年发布以来经历了多个版本迭代，最新版本为React 18.2。
- 查看方式  
    可访问React官网，在blog部分查看各版本发布历史。

### 重要版本及变更 
[01:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=79)

#### React 16.3：**引入Fiber架构** 
[01:42](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=102)

- Fiber架构简介
    - *为实现Concurrent模式奠定基础*。
    - 虽然不是API层面的大改，但*对后续版本性能优化至关重要*。

#### React 16.8：**正式引入Hooks** 
[01:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=119)

- Hooks的意义
    - *标志着React进入函数式编程时代*。
    - *提供useEffect、useState等核心API*，取代类组件生命周期和状态管理。
- 版本重要性
    - 是当前React开发的主要方式。
    - *必须掌握该版本引入的API及其使用场景*。

#### React 17：过渡版本 
[03:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=212)
[React 17 的那些变更](https://juejin.cn/post/7212529038875443259?searchId=2026010420403583E87791C3A64C0E83EE)

- *整体变化较小*
    - 官方称为 *“垫脚石”版本，为18铺路*。
- JSX**无需显式import React** [05:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=399)
    - JSX转换不再依赖`React.createElement`。
    - 新版自动从react包中调用入口函数。
- **事件委托机制变更** [08:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=647)
    - 从document层改为container层，避免与其他非React模块（如纯JS、Vue）冲突。
    - ![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/fdbf570622560466b0f88ff8f671495a_MD5.jpeg]]
- 其他变更
    - 副作用清理时间调整。
    - 错误信息翻译优化。
    - 算法改进。

#### React 18：**引入Concurrent模式**及相关新特性 
[11:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=736)
[React18正式版源码级剖析](https://juejin.cn/post/7080854114141208612)

- `Concurrent模式`概述
    - *不是具体API*，而是**支持异步可中断渲染**的理念。
- **主要新增API与功能**
    - `createRoot` API [12:33](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=753)  
        *替代ReactDOM.render，提供root变量复用能力*。
    - `自动批量更新（Automatic Batching）`[13:39](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=819)  
        *默认将多个状态更新合并处理，提升性能*。
        - 黑科技如*setTimeout失效*。
        - *同步更新*可通过flushSync API实现。
    - Suspense [15:40]

	- startTransition / useTransition [16:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1084)  
        *实现非紧急更新*，用于*区分高优先级和低优先级更新*。
        - 示例：输入框即时响应 vs 下拉列表延迟渲染。
    - useDeferredValue [18:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1111)  
        *参数化形式的transition，用于延迟渲染某些值*。
    - useId [18:53](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1133)  
        支持*服务端渲染（SSR）生成唯一ID*。
    - useSyncExternalStore [19:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1149)  
        用于外部状态管理库（如Redux）。
    - useInsertionEffect [19:53](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1193)  
        专为CSS-in-JS库设计的副作用钩子。

### 类组件与函数组件的发展趋势 
[21:38](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1298)

- 类组件现状
    - React官方将其归入“Legacy模式”，不推荐使用。
    - 仍可在旧项目中存在，但新项目应优先使用函数组件。
- 函数组件优势
    - 更简洁、更易组合。
    - 支持Hooks，便于逻辑复用与状态管理。

### 版本选择与项目迁移建议 
[15:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=908)

- 不同项目需关注不同版本
    - 新项目：直接使用React 18。
    - 老项目：根据版本判断是否支持Hooks、Transition等特性。
- 升级注意事项
    - 若使用了setTimeout等方式绕过批量更新，升级到18后需替换为flushSync。
    - 注意JSX不再需要import React，可减少冗余代码。

### 总结与学习建议 
[20:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=1209)

- 关键版本回顾
    - 16.3：Fiber架构。
    - 16.8：Hooks。
    - 17：小幅度优化。
    - 18：Concurrent模式相关API全面上线。
- 面试与实战重点
    - Hooks原理与使用。
    - Transition机制与优先级控制。
    - 自动批量更新与同步控制。
    - 函数组件替代类组件趋势。
- 推荐资源
    - React官网文档。
    - React官方博客。
    - 源码分析文章与社区PR实践案例。