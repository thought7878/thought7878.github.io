

本视频讲述了React源码中**createContainer及FiberRoot创建过程**，*解析了container、fiber root与host root fiber的类型定义、参数传递与实例化流程*，并详细说明了*二者通过循环引用相互关联*的构造机制。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/2aac2c497d3fe6861fbe0240c48562db_MD5.webp]]

## createContainer源码
新旧两个版本：
`packages/react-reconciler/src/ReactFiberReconciler.new.js`
`packages/react-reconciler/src/ReactFiberReconciler.old.js`

### 核心功能概述 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)
    
- `createContainer` 函数用于创建 `fiber root`，是*React渲染流程的起点之一*。
- *该函数主要作为入口，实际逻辑委托给其他函数处理*。


### 入参containerInfo与命名不一致问题 
[00:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=17)
    
- 参数 container 指代DOM容器节点，在示例中为ID为root的`<div>`元素。
- 注意：*在不同文件中该参数被命名为 `container` 或 `containerInfo`，存在命名不统一现象*。
- 类型上，container 应为 `Element | Document | DocumentFragment`。
- *从历史兼容路径（如 `legacy`）进入时，containerInfo 可能包含额外类型如 `ReactRootContainer`*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/1188f0ad339ecc6a7bdaeca1d2007175_MD5.webp]]

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/53b711d4eaad9b61cac59f61b6b5d2c6_MD5.webp]]

### tag入参与模式设置 
[03:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=206)
    
- `tag` 参数默认使用 `ConcurrentRoot`，表示当前为*并发模式*。
- 此值来自调用方传入，*决定后续创建的 `fiber root` 和 `fiber` 的行为模式*。

### 函数结构与历史代码组织方式 
[04:02](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=242)
    
- `createContainer` 函数本身不做实质操作，仅封装并*调用 `createFiberRoot`*。
- React源码中*常见此类多层函数嵌套结构*，源于**长期演进形成的历史包袱**。
- **虽有利于复用，但增加了阅读难度**。

### 返回值命名差异：FiberRoot vs. OpaqueRoot 
[04:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=292)
    
- 函数最终返回一个 `FiberRoot` 对象，但在局部变量中被命名为 `OpaqueRoot`。
- 实际两者指向同一对象，*命名差异仅为局部变量命名习惯所致*。
- 已有PR提议统一为 `FiberRoot`，尚未合并。

```ts
type OpaqueRoot = FiberRoot;
```

## createFiberRoot源码
### 定位与文件路径 
[06:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=365)
    
- *真正创建逻辑*位于 `createFiberRoot` 函数，定义于独立文件 `packages/react-reconciler/src/ReactFiberRoot.new.js` 中。

### FiberRoot类型
[06:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=406)
    
- `FiberRoot` 是**一个特殊对象，非普通 fiber 节点，代表整个应用的根管理器**。
- 其基础属性包括：
	- `tag`: RootTag，根类型，取值为 LegacyRoot或 ConcurrentRoot。
	- `containerInfo`: 指向宿主容器（即DOM节点）。
	- `current`: **指向当前工作/活跃的 fiber树根节点**。
	- 支持双缓存机制，包含 `finishedWork`, `pendingLanes`等调度相关字段。

### FiberRoot实例化
[08:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=489)
    
- 通过 `new FiberRootNode(containerInfo, tag)` 构造实例。
- 初始化时对 containerInfo、tag 等关键字段赋值。
- 其余字段如 pendingLanes, suspendedLanes, eventTimes 等均*初始化为默认值*（多数为 NoLanes 或 null）。

### createHostRootFiber：创建根 fiber 节点
[10:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=601)
    
- 调用 `createHostRootFiber(tag)` **创建与 FiberRoot 关联的根 fiber 节点**。
- **此 fiber 类型为 HostRoot，属于原生宿主环境下的根fiber**。
- 注意区分：
	- `FiberRoot`: ***管理对象，非fiber树一部分***。
	- `HostRoot Fiber`: ***fiber树的真正根节点，类型为 Fiber***。

#### tag值转换逻辑 
[11:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=678)
    
- `FiberRoot.tag` 使用 `RootTag`（如 `ConcurrentRoot`）。
- `HostRoot Fiber.tag` 使用 `WorkTag`，需进行映射：
	- 若 `FiberRoot.tag === ConcurrentRoot`，则 `fiber.tag = HostRoot`（值为3）。
	- 否则根据模式设置对应 `WorkTag`。

#### mode模式确定 
[11:27](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=687)
    
- 根据 `RootTag` 确定 `fiber.mode`：
	- `ConcurrentRoot` → `ConcurrentMode | StrictLegacyMode | StrictEffectsMode`
	- `LegacyRoot` → `NoMode`

### createFiber函数调用 
[12:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=757)
    
- 实际通过 `createFiber(HostRoot, null, null, mode)` **创建fiber节点**。
- 参数说明：
	- `tag`: HostRoot（值为3）
	- `pendingProps`: null
	- `key`: null
	- `mode`: 根据上述逻辑计算得出

### new FiberNode()
[13:21](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=801)
    
- `new FiberNode(tag, pendingProps, key, mode)` 完成创建。
- 所有子指针初始化为 `null`：
	- `return` = `null`
	- `child` = `null`
	- `sibling` = `null`
- 其他字段如 `alternate`, `memoizedState`, `updateQueue` 均初始化为 `null`。
- 效果相关字段如 `flags`, `subtreeFlags`, `deletions` 初始为0。

## 循环引用构造 
[16:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1019)
    
    - 创建完成后建立双向引用关系：
        - `FiberRoot.current = hostFiber`
        - `hostFiber.stateNode = FiberRoot`
    - 形成循环引用结构，便于后续从任一节点访问全局状态。

## 调试验证与截图说明 
[17:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1042)
    
    - 可通过在代码中添加 `console.log(FiberRoot)` 验证结构。
    - 输出显示：
        - `FiberRoot.current` 指向一个 `Fiber` 实例。
        - 该 `Fiber` 的 `tag` 值为 33，对应 `HostRoot`。
        - 其 `stateNode` 指回 `FiberRoot`，确认循环构造完成。

## 课件与源码展示规范 
[15:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=948)
    
    - 教学材料中标注源码路径以方便定位。
    - 删除开发环境（DEV）、Profiler等非核心代码以简化篇幅。
    - 保留主干逻辑，确保理解完整性。