

本视频讲述了React中Fiber节点的核心属性与结构设计，包括type、data node、单链表子节点关系、index下标作用、update队列机制、memoized状态存储、二进制flags标记及老Fiber对比等关键概念，并解析了其在协调过程中的实际应用。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第7章 React渲染机制：原始渲染VDOM与性能改革Fiber的源码剖析/media/61109a327cdc86c9a1d4ae2273a7d6b4_MD5.webp]]

- 组件类型与element type [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=0)
    
    - Fiber节点中的`type`和`element type`本质是同一个值，均来源于React元素的`type`。
    - 在React源码中生成Fiber时，会将`element.type`赋值给Fiber的`type`。
    - 协调阶段使用`element type`，但二者值相同，仅在特定阶段临时保留一份。
- data node的取值差异 [00:43](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=43)
    
    - `data node`字段根据组件类型存储不同内容：
        - 原生标签组件：指向对应的DOM节点。
        - 类组件：指向该组件的实例。
        - 函数组件：无实例，值为`null`。
    - `type`字段本身也因组件类型而异：
        - 原生标签：字符串（如`"div"`）。
        - 函数组件：函数本身。
        - 类组件：类构造器。
        - 取值类型为`any`。
- Fiber的单链表结构 [01:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=77)
    
    - return指向父Fiber [01:27](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=87)
        - `return`指针指向当前Fiber的父节点。
        - 示例中`box border`组件的`return`指向根节点。
        - 根节点需进一步拆解才能看到完整结构。
    - child指向第一个子Fiber [02:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=125)
        - `child`指向第一个子Fiber节点，即“老大”。
        - 与Vue中`children`数组不同，React采用单链表结构。
    - sibling指向下一个兄弟节点 [03:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=181)
        - 同级后续节点通过`sibling`连接，形成链表。
        - 如`HE`标签是`box border`的子节点，其`sibling`可能指向下一个同级节点。
        - 链表末尾的`sibling`为`null`。
- index记录节点位置 [03:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=230)
    
    - `index`字段为数字类型，用于记录节点在其父节点下的位置索引。
    - 因React使用单链表而非数组存储子节点，链表无天然下标，故需手动维护`index`。
    - Vue源码中无此字段，因其使用数组存储，自带下标。
    - `index`主要用于diff算法判断节点是否移动。
- props与pendingProps [05:13](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=313)
    
    - `props`存储当前生效的属性值。
    - `pendingProps`存储待更新的新属性值。
    - `memoizedProps`缓存上一次已完成更新的`props`，用于比较变化。
- update queue更新队列机制 [05:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=349)
    
    - 更新操作（如`setState`、`render`）不会立即执行，而是加入`update queue`批量处理。
    - 批量更新提升性能，避免频繁渲染。
    - 类比钓鱼：钓到鱼后不立刻售卖，而是积累一定数量再统一出售，节省往返成本。
    - `update queue`专门存储待处理的更新对象，包含回调函数和更新数据。
- memoizedState状态存储 [07:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=445)
    
    - 不同组件类型下`memoizedState`存储内容不同：
        - 函数组件：保存Hook链表的头节点（第零个Hook）。
        - 类组件：保存组件的当前状态（`state`）。
- dependencies依赖收集 [07:53](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=473)
    
    - 存储当前组件所依赖的Context等动态数据。
    - 用于检测依赖变化以决定是否重新渲染。
- mode模式标识 [08:03](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=483)
    
    - 记录组件所处的React模式，如：
        - `profile`模式：用于性能分析。
        - `strict`模式：严格模式，帮助发现潜在问题。
        - 遗留模式等。
- flags标记操作类型 [08:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=502)
    
    - `flags`表示当前Fiber节点需要执行的操作。
    - 初次渲染时为`Placement`（插入）。
    - 更新阶段为`Update`。
    - 使用二进制值进行标记，优势在于：
        - 底层运算速度快。
        - 支持位运算组合多个操作，具有唯一性和高效性。
    - 例如一个节点既需插入又需更新，可通过位或运算组合`Placement | Update`。
    - 若使用字符串或十进制则难以高效组合与判断。
    - 具体运算将在手写Diff算法时详细讲解。
- deletions删除列表 [10:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=622)
    
    - `deletions`存储该Fiber节点下需要被删除的子节点数组。
    - 删除节点不能直接从新V DOM树中移除，因为旧树仍存在。
    - 必须标记并暂存于父节点上，以便后续统一清理。
    - 被删除的节点无对应的新Fiber，故无法挂载到自身，只能由父节点持有。
- alternate指向旧Fiber [11:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=682)
    
    - `alternate`指向该节点在上一次渲染时对应的Fiber（即“老Fiber”）。
    - Diff过程中需对比新旧Fiber的变化。
    - 初次挂载的节点无历史版本，`alternate`为`null`。
- 其他调试相关字段 [11:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=709)
    
    - 如`duration`、开始时间戳等字段仅在开发环境（DEV）或性能测试（Profile）模式下启用。
    - 正式环境中不启用，不影响主干逻辑。
    - 属于辅助诊断用途，课程中不再展开。
- Fiber指向关系图示与辨析 [13:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=789)
    
    - 图中展示`return`、`child`、`sibling`、`index`等指针关系。
    - 强调：`child`指向第一个子Fiber，但某Fiber的`child`不一定总是另一个Fiber的父节点。
    - 父子关系是单向的，必须通过`return`反向验证。
- 协调阶段与tag类型标识 [14:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=849)
    
    - 协调阶段即对比新旧Fiber树，找出变化部分。
    - `tag`字段标识组件类型，如函数组件、类组件、原生标签、Fragment等。
    - 当前共有0到27共28种类型。
    - tag值可能随React版本更新而变化，建议查看最新源码确认。
- 总结与回顾 [15:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=922)
    
    - Fiber是React的协调引擎核心实现。
    - 代码层面，Fiber是一个普通的JavaScript对象（fiber node），包含多种属性用于描述组件状态、关系和更新意图。
    - 本节课介绍了Fiber的主要属性及其作用，后续将通过手写实现加深理解。