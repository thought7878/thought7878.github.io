

本视频讲述了*React中的状态管理及状态管理库的使用方法*，包括*组件内部状态定义、状态提升机制、第三方状态管理库（如Redux）的工作原理*，并*通过简版源码解析了外部状态的定义与更新机制*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/1dd1511f36028e74c71db145c6437248_MD5.webp]]

### 1. 状态的基本概念 
[00:15](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=15)

- **状态的定义**  
    `状态`就是**一个变量，这个变量改变时组件会进行更新**。
- **常见的状态定义方式**
    - 使用 `useState`、`useReducer`。
    - 类组件中使用 `this.state`。
    - 第三方状态管理库。

### 2. 组件内部状态 
[00:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=54)

- **适用场景**
    - 变量*只与当前组件有关*。
    - *功能单一、影响范围小*。
- **实现方式**
    - 使用 `useState`、`useReducer` 或类组件的 `state`。
- **存储位置**
    - 状态值*存储在组件的 fiber 节点（VDOM）上*。每个组件都有一个对应的fiber节点。**VDOM=fiber节点=JS对象**。
    - 每个组件都有自己的 fiber 节点。

### 3. 组件之间共享状态 
[02:36](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=156)

- **问题描述**
    - 多个组件需要共享一个状态，例如两个 Panel 共享 `isActive`。
    - 希望状态改变时多个组件同步更新。
    - ![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/2a3467c8cdb33a3a3d41872bc1eb1383_MD5.webp]]
- **解决方案**：`状态提升` [03:30](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=210)
    - 将状态放到共同父组件中。
    - 通过 props 向下传递状态和更新函数。
- **局限性**
    - 若组件层级较深，props 传递复杂且容易出错。
    - 需要修改中间层级 props 的结构。

### 4. 在 React 外部定义状态 
[06:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=374)

- 基本思路
    - *在 React 组件外部*定义变量作为状态。
    - 手动*控制状态变化并触发组件更新*。
- **缺点**
    - 容易造成全局污染。
    - 控制状态更新逻辑复杂。
- 推荐方案：使用第三方状态管理库 [06:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=419)
    - 如 Redux、MobX、Zustand 等。

### 5. Redux 简版源码解析 
[07:45](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=465)

- 核心概念
    - `currentData`：状态值。
    - `getState()`：获取状态。
    - `dispatch(action)`：通过 reducer 修改状态。
    - `subscribe(listener)`：订阅状态变化事件。
- 状态修改流程
    1. 使用 `createStore` 创建 store。
    2. 定义 `currentData` 存储状态。
    3. `dispatch` 接收 action 和旧状态，返回新状态。
    4. 更新 `currentData`。
    5. 执行所有订阅的 listener 函数（如组件更新函数）。
- 监听器管理
    - 使用 `listenerIdCounter` 为每个 listener 分配唯一 ID。
    - 使用 map 记录所有 listener。
    - 支持添加和删除 listener。

### 6. 状态管理库的应用示例：ANTD 表单 
[13:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=797)

- 问题背景
    - 表单包含大量 input 字段。
    - 每个字段需绑定 value 和 onChange。
- 简化实践
    - 使用 ANTD 表单组件无需手动编写大量 onChange。
    - 内部自动处理状态管理和联动更新。
- 思考题
    - 如何实现类似 ANTD 表单的状态管理？
    - 提示：可参考 Redux 源码设计表单状态中心化管理机制。

### 7. 总结与预告 
[15:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=952)

- 重点回顾
    - 组件内状态 vs 组件间共享状态。
    - 状态提升机制及其限制。
    - 第三方状态管理库的核心工作原理。
- 下节课预告
    - 解析 ANTD 表单状态管理机制。
    - 实现一个简化的表单状态管理模块。