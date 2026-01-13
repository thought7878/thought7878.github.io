

本视频讲述了*React源码中`createRoot`函数的执行流程*，重点*解析了`markContainerAsRoot`的作用、为DOM节点添加内部属性的过程、事件监听的简要说明*，以及*最终返回`ReactDOMRoot`实例并关联`fiberRoot`的实现机制*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/6b04c242d9281687cf799e9c5235399e_MD5.webp]]

- 函数执行上下文回顾 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)
    
    - 继续分析`createRoot`函数后续执行逻辑。
    - 进入`markContainerAsRoot`函数调用阶段。
- markContainerAsRoot 函数作用解析 [00:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=5)
    
    - 该函数位于`createContainer`之后，用于标记当前container为根节点。
    - 所谓“根节点”指代的是与Fiber结构相关的根fiber（fiber root），其本质仍是一个fiber对象。
    - 可通过F12跳转至函数定义处查看实现细节。
- 函数文件定位方法 [00:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=34)
    
    - 可通过`Cmd + P`（Mac）或`Ctrl + P`（Windows）在编辑器中搜索函数名：`markContainerAsRoot`。
    - 定位到对应源码文件进行查阅。
- markContainerAsRoot 具体实现 [00:57](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=57)
    
    - 函数功能简单：为传入的DOM节点（即`node`）添加一个特殊属性。
    - 属性名为`__internalContainerInstanceKey`（原文表述为拼接生成的字符串）。
    - 该值由随机key与固定字符串拼接而成，确保唯一性。
    - 此操作目的在于标记该DOM节点关联了一个React根容器。
- 属性添加的目的与用途 [01:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=78)
    
    - 添加此属性后，后续其他函数可据此从DOM节点上读取对应的fiber root引用。
    - 典型使用场景包括：
        - `getClosestInstanceFromNode`
        - 其他需要根据DOM节点反查fiber实例的函数
    - 实现机制依赖于在DOM上挂载内部属性以建立映射关系。
- 属性的增删与检查逻辑 [01:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=110)
    
    - 标记（mark）操作即为添加属性，表示启用该根节点。
    - 当组件卸载时，可通过移除该属性完成清理。
    - 或通过判断属性值是否为`null`来检测是否已被取消标记。
    - 整个标记机制简洁且高效。
- 关于事件处理的说明 [02:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=145)
    
    - 后续涉及事件监听相关逻辑，但因复杂度较高，本节暂不展开。
    - 特别指出：`rootContainerElement`之前的逻辑并非事件处理主体。
- rootContainerElement 的作用与背景 [02:38](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=158)
    
    - 功能是判断当前node类型是否为注释节点（comment node）。
    - 若是注释节点，则取其父节点作为容器；否则直接使用container。
    - 此逻辑源于FB旧代码兼容需求。
    - 目前React已不再支持通过comment node创建根节点。
    - 仅保留用于向后兼容，对现代应用无实际影响。
- 合成事件系统简介 [03:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=188)
    
    - 后续listener和事件注册涉及React的合成事件（Synthetic Event）系统。
    - 合成事件机制较为复杂，包含大量浏览器兼容与事件代理逻辑。
    - 本课程将在后续专门章节详细讲解事件系统。
    - 当前只需理解：此处作用是在container层级监听所有支持的事件类型。
- 返回 ReactDOMRoot 实例 [03:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=212)
    
    - 函数最终返回一个新的`ReactDOMRoot`实例。
    - 回顾函数初衷：`createRoot`的目标是返回一个符合`RootType`类型的对象。
- RootType 类型结构说明 [03:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=229)
    
    - `RootType`包含以下关键字段：
        - `render`: 渲染入口
        - `unmount`: 卸载接口
        - `internalRoot`: 指向内部的`fiberRoot`
- ReactDOMRoot 构造过程 [03:57](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=237)
    
    - `ReactDOMRoot`构造函数逻辑简单。
    - 主要操作是将通过`createContainer`创建的`fiberRoot`赋值给实例的`_internalRoot`属性。
    - `_internalRoot`为私有内部字段，以下划线开头标识其非公开性。
- 总结与后续安排 [04:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=264)
    
    - 本节课重点讲解了从`createRoot`到`markContainerAsRoot`再到返回`ReactDOMRoot`的整体流程。
    - 后续课程将深入讲解`ReactDOMRoot`上的`render`、`unmount`等方法的具体实现。
    - 建议学员结合课件梳理本节内容，巩固理解。
    - 本次课程结束。