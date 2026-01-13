

本视频讲述了*React中createRoot API的基本用法及其在源码中的处理逻辑*，包括*参数类型检查、options配置项解析*及*根节点创建的初步流程*，并强调了*稳定与不稳定API的区别及使用注意事项*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/08ef969c372f7283880da6fae26e5958_MD5.webp]]

## 课程引入与createRoot API概述 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)

- 课程目标说明 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)  
    本节课*重点讲解 `createRoot` 这个在浏览器环境中用于创建React根节点的API*。
- 适用场景说明 [00:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=9)  
    在开发基于浏览器的React应用时，若需进行渲染操作，通常会使用 `createRoot`。
- 文档参考提示 [00:13](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=13)  
    提供了官方文档链接以供查阅 `createRoot` 的详细说明。

## createRoot API的基本用法 
[00:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=24)

- 函数签名与参数说明 [00:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=26)
    - 接受两个参数：`container` 和 `options`。
    - `container` 为必选参数，表示*DOM容器节点*。
    - `options` 为可选参数，用于配置额外行为。
- container 参数类型 [00:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=31)
    - 一般通过 `document.getElementById()`获取具体DOM元素（如DIV）。
    - 实际上支持多种类型：
        - DOM Element（如 `<div>`）
        - `document`
        - `DocumentFragment`
- options 参数说明 [00:53](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=53)
    - 非必填。
    - 官方文档提及的主要选项包括：
        - `onUncaughtError`: 处理*未捕获错误的回调函数*。
        - `identifierPrefix`: *生成组件ID时使用的字符串前缀*。
    - 源码中还存在其他以 `unstable_` 开头的选项，属于不稳定的实验性功能。

## 源码路径
[01:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=116)

- 源码文件位置 [01:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=116)
    - 文件路径：`packages/react-dom/src/client/ReactDOMRoot.js`。
- 学习建议 [02:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=139)
    - 建议安装代码导航工具（如Sourcegraph或VS Code插件），便于快速跳转至相关API定义处。

## createRoot 源码分析 
### 入参分析 
[02:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=166)

- *参数接收与类型定义* [03:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=180)
    - 函数接收 `container: Element | Document | DocumentFragment` 和 `options?: Options`。
    - 类型定义位于同一文件内，可直接查看。
- *options 中的 unstable 选项* [03:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=214)
    - 包含 `unstable_strictMode`, `unstable_concurrentUpdatesByDefault`, `unstable_transitionCallbacks` 等。
    - 这些API带有 `unstable_` 前缀，表明其为内部实验特性，不应在生产项目中使用。
    - 示例演示：通过设置 `unstable_strictMode: true` 可启用严格模式，导致首次渲染被销毁并重新执行一次。
- *unstable API 的风险说明* [04:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=275)
    - 不稳定API可能随时更改甚至移除。
    - 举例：过去存在的 `unstable_offscreen`已更名为 `activity`。
    - 因此，这类API仅适合了解开发趋势，不可用于正式项目。

### 返回值类型 RootType 
[06:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=360)

- 返回值结构 [06:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=360)
    - `createRoot` *返回一个符合 `RootType` 接口的对象*。
- rootType 接口定义 [06:06](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=366)
    - 包含以下方法：
        - `render(children: ReactNodeList)`: *渲染React子树*。
        - `unmount()`: *卸载整个React应用*。
    - 包含一个私有属性：
        - `\_internalRoot`: *内部使用的根对象*，开发者不应直接访问。

### 校验 container 参数合法性 
[07:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=451)

- 类型检查机制 [07:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=451)
    - 若传入的 `container` 不是合法的DOM节点类型，则会抛出错误。
- 错误提示示例 [07:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=476)
    - 错误信息：“Target container is not a DOM element”。
- 校验函数实现 [08:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=494)
    - 调用 `isValidContainer` 函数进行判断。
    - 依据 `node.nodeType` 的值来确认是否为有效容器。
- Node Type 常量定义 [08:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=532)
    - `ELEMENT_NODE = 1`（普通标签节点，如DIV）
    - `TEXT_NODE = 3`（文本节点）
    - `COMMENT_NODE = 8`（注释节点）
    - `DOCUMENT_NODE = 9`（document对象）
    - `DOCUMENT_FRAGMENT_NODE = 11`（DocumentFragment）

### 检验 options 参数
[10:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=605)

- 可选性判断 [10:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=605)
    - `options` 为可选参数，若未提供则跳过处理。
- 参数提取与存储 [10:20](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=620)
    - 若提供了 `options`，则从中提取如下字段：
        - `unstable_strictMode`: 控制是否启用严格模式。
        - `onUncaughtError`: 设置全局错误处理器。
        - `identifierPrefix`: 设置自动ID前缀。
    - 提取后的值将在后续创建根节点时使用。

## 创建根节点（第三步）
[10:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=652)

- 核心步骤说明 [10:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=652)
    - 调用 `createContainer` 函数创建React根实例。
- createContainer 函数来源 [11:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=669)
    - 定义于 `react-reconciler/src/ReactFiberReconciler.js`文件中。
- createContainer 功能简介 [11:12](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=672)
    - 创建一个Fiber根节点（fiberRoot），作为协调器的工作起点。
    - 该过程涉及多个子步骤（A/B/C/D），将在后续视频中继续讲解。
- 学习建议 [11:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=686)
    - 鼓励学生暂停视频后自行尝试查找 `createContainer` 函数定义，提升源码阅读能力。

## 课程总结与下节预告 
[11:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=708)

- 本节回顾 [11:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=708)
    - 讲解了 `createRoot` 的基本用法、参数类型、错误处理机制以及源码中的关键检查逻辑。
    - 强调了 `unstable_*` API 的实验性质和禁用建议。
- 下节预告 [11:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=708)
    - 下一节课将继续深入分析 `createContainer` 函数的具体实现逻辑。