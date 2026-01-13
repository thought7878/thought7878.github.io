
本视频讲述了React 18中root对象的render与unmount函数实现原理，重点解析了updateContainer的调用流程及更新优先级（lane）的获取机制，包括外部更新默认优先级的确定过程。

![](https://img-view-c-sz.drive.quark.cn/PUflgQcF/3062111611/d9c6589cef294304b6beeee644cc7f076868eaf3/6868eaf38110279be1974a0d85168c20d38d9a8f/preview?auth_key=1768318891-1-10800-cd766cc9922db8f3b7acee5eb05730b4&sp=100&token=5-c958601b5eb547522c489024c079a75a-0-3-1024-5624f0a52ffb4c37adf861b5a86f92f1-0-0-0-0-1768308091831-03a72542fcc1947c39321ecf0345a87a&ork=k334Q1epk77455X5151VrPI4N6PqUd7nNbA9b1qWj&ud=24-0-3-N-N-N-11-N-1-N-0-N-12-N-N&dfi=152)

### root对象的属性结构 [00:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=19)

- root对象的三个属性  
    root对象包含三个属性：`render`函数、`unmount`函数以及`_internalRoot`属性。
- _internalRoot属性说明  
    `_internalRoot`是内部属性，以下划线开头命名，表示其为React内部使用，不对外暴露。

### render函数的赋值来源 [01:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=94)

- 通过prototype赋值  
    `render`函数通过`ReactDOM.prototype`进行赋值。
- 具体函数引用  
    实际赋值的是`ReactDOM.__renderer.render`函数，该函数在客户端环境下被统一使用。

### render函数的执行逻辑 [02:11](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=131)

- 接收参数  
    `render`函数接收一个`children`参数，即要渲染的React节点（如JSX元素）。
- 触发更新  
    调用`render`后会执行`updateContainer`函数，将`children`作为首次更新内容传入，启动初次渲染流程。

### unmount函数的功能与实现 [03:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=185)

- 功能定义  
    `unmount`函数用于手动卸载整个React应用。
- 历史演变  
    在React 18之前，对应的方法名为`unmountComponentAtNode`。
- 核心操作
    - 将`_internalRoot`置为`null`，清除内部根节点引用。
    - 调用`markContainerAsRoot`的逆向操作，取消容器的根节点标记。
    - 执行`flushSync`强制同步刷新更新队列。

### updateContainer函数的作用与参数 [04:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=280)

- 函数路径提示  
    `updateContainer`定义于React源码中的特定文件，可通过课程提供的路径查阅。
- 参数列表
    - `element`: 要渲染的子节点（首次渲染时为`children`，卸载时为`null`）。
    - `container`: 容器节点，即`fiberRoot`。
    - `parentComponent`: 父组件，在React 18中始终为`null`。
    - `callback`: 回调函数，在React 18中也始终为`null`。
- 遗留参数说明  
    后两个参数为兼容老版本（legacy mode）保留，当前模式下不使用。

### updateContainer的核心逻辑 [06:38](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=398)

- 更新的本质  
    渲染和卸载都属于“更新”（update），均通过`updateContainer`触发。
- fiber树关联  
    每个fiber节点拥有`updateQueue`属性，用于存储待处理的更新任务。
- 多更新合并策略  
    多个更新会被收集到`updateQueue`中，避免逐个执行带来的性能损耗。

### 获取fiber节点的方法 [08:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=505)

- 从fiberRoot获取current  
    `fiberRoot.current`指向当前的host root fiber，是构建更新的起点。

### 更新优先级（Lane）机制概述 [08:55](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=535)

- 优先级必要性类比  
    类比交通系统，普通更新如同普通车辆，紧急更新（如用户输入）如同救护车，需高优先级通行。
- lane的数据类型  
    `lane`为数字类型，实际以二进制位表示，支持单个或多个优先级组合。
- lane的分类
    - 同步/普通更新：如`SyncLane`（值为231−1231−1）、`DefaultLane`（值为32）。
    - 连续事件：如`InputContinuousLane`，用于处理连续输入事件。
    - 过渡更新（Transition）：非紧急更新，React 18中新引入的支持类型。

### requestUpdateLane函数的作用 [11:06](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=666)

- 功能描述  
    根据当前上下文和模式，为更新分配一个合适的`lane`。
- 接收参数  
    接收一个`fiber`节点作为输入。
- 返回值  
    返回一个代表优先级的`lane`数值。

### 模式（mode）的初始化 [11:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=719)

- createRoot时指定模式  
    在`createRoot`过程中，已通过`concurrentMode`标志设置为并发模式。
- fiber创建时携带模式  
    创建`HostRootFiber`时，将模式信息写入其`mode`字段。

### requestUpdateLane中的模式判断 [13:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=820)

- 跳过Legacy模式处理  
    当前为并发模式，因此跳过针对`legacyMode`的代码分支。
- 跳过Transition相关逻辑  
    Transition相关的优先级处理将在后续专题讲解，此处暂不展开。

### 普通更新的优先级获取 [14:47](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=887)

- 两类外部更新
    - React内部发起的更新：如`flushSync`、`setState`等，走`getConcurrentUpdatePriority`。
    - 外部发起的更新：如`root.render()`，走`getCurrentEventPriority`。
- 当前场景归属  
    初次渲染属于外部发起的更新，因此调用`getCurrentEventPriority`。

### getCurrentEventPriority函数分析 [16:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=976)

- 函数定位路径  
    该函数位于`ReactFiberLane`模块中，需通过多次跳转查找。
- 无事件时的返回值  
    若当前无用户事件触发，则返回`DefaultEventPriority`。
- DefaultEventPriority的值  
    其值为`DefaultLane`，对应数值为 3232。

### 内部更新的优先级设置机制 [18:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1129)

- currentUpdatePriority变量  
    是一个全局变量，记录当前更新的优先级。
- setCurrentUpdatePriority函数  
    用于设置`currentUpdatePriority`，通常在事件触发时调用。
- 事件示例：点击事件  
    触发click事件时，会调用`setCurrentUpdatePriority`，将其设为`DiscreteEventPriority`（对应值为 22）。

### 总结与下节预告 [21:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1268)

- 本节核心内容回顾  
    讲解了`render`与`unmount`函数的底层实现，并深入分析了`updateContainer`如何根据更新类型获取对应的优先级`lane`。
- 默认优先级结论  
    初次渲染作为外部更新，其优先级为`DefaultLane`（值为 3232）。
- 下节预告  
    下节课将继续讲解如何创建`update`对象并将其插入到`updateQueue`中。