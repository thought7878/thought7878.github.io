

本视频讲述了**`虚拟DOM`的概念、作用、在React中的应用及其实现原理**，包括**JSX解析、React Element生成、Fiber结构的构建过程**。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/9becfab142306de8643d7157715ecd9b_MD5.webp]]

## 一、虚拟DOM概述 
[00:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=23)

- 什么是虚拟DOM  
    `虚拟DOM（Virtual DOM、vDOM）`是一种编程概念，是**内存中的UI数据**（用于*将UI以理想化形式存入内存中*），并通过库如React与真实DOM同步。
    
- 虚拟DOM的作用  
    vDOM是**中间过程产物，最终目标是生成真实DOM**。**页面动态变化时，先更新vDOM，再根据新vDOM更新真实DOM，实现高效更新**。
    
- 虚拟DOM的历史  
    最早由React提出，后被其他框架采用，如Vue早期未使用，后来引入。
    
- 虚拟DOM与Diff算法的关系  
    `Diff算法`**对比的是新旧vDOM**，而非直接对比真实DOM，提高了性能效率。
    

## 二、虚拟DOM的微观实现 
[03:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=194)

- **数据结构**  
    `虚拟DOM`***本质上是一个JavaScript对象***，用于***描述DOM的信息（属性）、结构（子节点）***。
    
- **状态变更处理流程**
    - *状态变化时，先更新vDOM（JS对象）。*
    - *再根据新的JS对象更新真实DOM。*
- **为何不直接Diff真实DOM？**  
    真实DOM节点属性繁多，操作成本高；而vDOM只保留必要属性，简化了Diff过程，提高效率。
    

## 三、React中虚拟DOM的应用 
[09:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=559)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/5a8a01c71b7744c56a93544123a9e69e_MD5.webp]]

### JSX是什么
`JSX`是React中用于**描述UI的语法扩展**，本质是语法糖，背后**会转换为`React.createElement()`或`jsx()`函数调用**。

### 为什么使用JSX


### JSX到ReactElement的转换
JSX --编译时的Babel-> jsx() --运行时-> ReactElement

- 在React 17之前，*JSX通过Babel转译成`createElement()`*。
- React 17之后，*使用`jsx()`函数替代，无需手动导入React*。


### ReactElement
 ReactElement是React中用来描述UI的对象，它是构建React组件树的基本单元。

#### 源码路径 
源码位于`packages/react/src/jsx/ReactJSXElement.js`，定义了`jsx()`函数、其开发环境版本`jsxDEV()`。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/452a2291b10b3533522703540110323d_MD5.webp]]


#### 结构
包含以下关键字段：
- `type`：表示元素类型（字符串表示*原生标签*，函数表示*组件*）。
- `key` 和 `ref`：特殊属性。
- `props`：*传递给组件的属性*。
- `$$typeof`：*标识该对象为ReactElement*，防止JSON注入攻击。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/4111290932139c8da8108fa89100844e_MD5.webp]]

## 四、Fiber架构与虚拟DOM更新 
[20:28](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1228)

### Fiber简介
- Fiber简介  
    `Fiber`是**React内部用于管理组件树的数据结构，存储在内存中，用于协调更新过程**。
- Fiber源码位置  
    *定义*在`packages/react-reconciler/src/ReactInternalTypes.js`中。

### ReactElement到Fiber的转换
    
- **新的ReactElement用于构建新的Fiber**。
- **老Fiber与新Fiber进行Diff比较，确定最小更新范围**。

### Fiber关键属性
    
- `key`：与React Element一致。
- `type`：元素类型。
- `stateNode`：指向真实DOM节点（HostElement/原生标签）或组件实例（类组件）。


## 五、总结与常见问题 
[24:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1444)

- 虚拟DOM的核心价值  
    提升页面更新性能，减少不必要的DOM重排重绘，优化用户体验。
    
- React中虚拟DOM的表现形式
    - JSX：语法扩展。
    - React Element：描述UI的对象。
    - Fiber：协调过程中使用的内存结构。
- Diff算法的本质  
    对比新旧vDOM（即新旧Fiber），找出差异部分并局部更新真实DOM。
    
- 面试题准备要点  
    需掌握：
    - 虚拟DOM定义与作用。
    - 与真实DOM的差异。
    - 在React中的实现路径（JSX → Element → Fiber）。
    - Diff算法的基本思想。
- 后续学习方向  
    手写实现React的Diff算法，深入理解协调机制。