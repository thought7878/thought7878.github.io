

本视频讲述了React中*Context API的核心作用、适用场景及使用方法*，重点解析其*解决跨层级组件通信问题*的机制，并通过具体代码*演示创建Context对象、Provider分发value值以及Consumer消费*三种方式的应用与限制。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/284d3433db79bcf65689b3497b33d9f9_MD5.webp]]

## context API的重要性 
[00:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=4)
    
- context在React项目中应用广泛，尤其*在封装较深的组件或第三方库（如状态管理、路由）中被频繁使用*。
- 尽管开发者可能未直接使用context，但其背后已被封装进常用API中。

## 为何引入context？
[01:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=100)
    
- context为了**解决props跨层级传递带来的维护成本问题**。
- *当组件层级过深时，props需要逐层传递，修改频繁且容易出错*。

## context与第三方状态管理库的区别 
[03:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=188)
    
    - context适用于祖先与后代组件之间的通信。
    - 第三方状态管理库（如Redux）适用于多个无直接父子关系组件之间共享状态。
    - Redux等库内部也常借助context实现状态分发。

- 常见应用场景 [05:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=346)
    
    - 主题切换（如暗黑/亮色模式）
    - 用户账户信息存储
    - 路由状态管理
    - 状态管理库（如react-redux）中的Provider实现
- context三步用法概述 [07:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=427)
    
    1. 创建context对象：使用`createContext`API
    2. 使用`Provider`组件分发value值
    3. 后代组件消费value值，可通过以下三种方式：
        - `useContext`（函数组件）
        - `contextType`（类组件）
        - `Consumer`组件（通用）
- 默认值的作用与使用 [10:03](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=603)
    
    - 若组件未匹配到任何Provider，则会取createContext时设置的默认值。
    - 推荐在创建context时设置合理默认值以避免undefined错误。
- 多层级Provider嵌套与就近匹配 [11:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=667)
    
    - Provider可嵌套使用，后代组件优先匹配最近的Provider。
    - 外层Provider不影响内层组件已定义的value值。
- 三种消费方式详解及局限性 [12:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=734)
    
    - useContext
        - 适用于函数组件
        - 可同时消费多个context
        - 语法简洁易用
    - contextType
        - 仅适用于类组件
        - 静态属性名必须为`contextType`
        - 一次只能消费一个context
    - Consumer
        - 适用于任意组件类型（函数/类组件）
        - 语法稍复杂，需使用render props模式
        - 可消费多个context但结构冗余
- 完整示例演示 [08:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=481)
    
    - 示例功能：主题颜色切换和计数器状态共享
    - 创建两个context：`countContext` 和 `myBGContext`
    - Provider嵌套使用并传入动态value
    - 多种消费方式混用展示实际效果
    - 演示默认值、就近匹配、状态更新同步等特性
- 注意事项总结 [13:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=839)
    
    - context适合祖先-后代通信，非任意组件间通信
    - 不推荐大量数据频繁更新，可能引发性能问题
    - 三种消费方式各有适用场景，注意其局限性
    - 实际开发建议结合业务需求选择是否使用context或使用状态管理库
- 后续学习建议 [14:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=889)
    
    - 建议掌握context基本使用后再深入学习其原理源码
    - 可尝试自行实现一个简易的Provider组件加深理解
    - 结合实际项目练习，巩固context在主题、用户信息等场景下的应用