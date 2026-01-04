
# 夸克总结
本视频讲述了学习React源码的目的与意义，包括自研框架需求、面试题解析、项目性能优化及编程技巧提升等方面，并强调通过源码学习实现技术突破。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/8497e047e80ec7eed3ed2d74529a468b_MD5.jpeg]]

---
## 为了重新写一个React？
- 前端三大框架概述 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)
    - 前端主流三大框架为 React、Vue、Angular。
    - 国内主要使用 React 和 Vue。
- 学习React源码的首要目的 [00:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=32)
    - 学习React源码并非为了重写React。
    - 但部分大公司如阿里、华为*会基于React思想开发自研框架*。
- 基础架构组的作用 [01:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=79)
    - *负责构建公司内部前端基础设施*。
    - 可能*涉及开源或非开源框架开发*，如Ant Design、umi等。

## 解决常见面试题
- 学习源码对面试的帮助 [01:58](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=118)
    - 面试题常涉及*React底层机制，如Hooks原理*。
    - 可*深入理解useState、useReducer等状态管理API*的实现方式。
- Hooks的引入与状态存储 [02:38](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=158)
    - React 16.8引入Hooks前，函数组件无法使用状态。
    - Hooks出现后，函数组件可通过useState等API管理状态。
    - 状态具体存储位置需从源码中理解。
- React与Vue diff算法对比 [03:39](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=219)
    - React和Vue均采用虚拟DOM diff算法。
    - *React使用单链表结构*进行单向查找。
    - Vue使用数组结构支持双向查找，实现更灵活。
    - React*未采用双向查找原因*：*维护成本高、当前性能已足够*。

## 优化项目
- 优化项目的实际应用 [05:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=325)
    - 不同电商平台页面渲染速度差异源于优化水平。
    - 优化手段包括缓存、避免无效渲染、懒加载、稳定key值等。
    - *掌握源码可精准使用effect、状态管理等关键API*。
- 知其然更要知其所以然 [06:42](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=402)
    - *死记硬背可能导致性能问题却不知原因*。
    - *例如*错误使用index作为key，在某些场景下虽无明显bug，但影响组件复用效率。

## 学习编程技巧，提升个人代码水平
- *提升编程技巧的方式* [07:42](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=462)
    - 业务代码难以带来技术突破。
    - *学习高质量开源项目源码是有效方法*，如React、Vue、Node.js、Angular等。
- 源码中的二分查找实现 [08:21](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=501)
    - 普通开发者取中位数常用除法运算。
    - *开源框架多采用位运算*（如`start + ((end - start) >> 1)`）提高效率。
- *源码涉及的基础知识点* [09:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=557)
    - 包括但不限于：
        - 数据结构：链表、循环链表、Fiber树。
        - 算法：调度算法、diff算法。
        - 系统设计：调度器类似小型操作系统。