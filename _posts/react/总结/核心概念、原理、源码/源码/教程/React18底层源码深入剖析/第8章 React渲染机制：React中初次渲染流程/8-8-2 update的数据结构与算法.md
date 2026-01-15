
本视频讲述了**React中更新队列（update queue）的数据结构与处理算法**，重点解析了*分类式函数的执行流程、单向循环链表的构建与转移、状态计算过程、在begin work阶段对fiber上更新队列的处理机制*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第8章 React渲染机制：React中初次渲染流程/media/eb346bfbe2a95ec83fa9d0409ccce625_MD5.webp]]

## finishQueueingConcurrentUpdates
参考：[[finishQueueingConcurrentUpdates]]

### 分类式函数与更新队列初始化 
[00:03](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=3)

- 代码存储与取出逻辑 [00:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=8)  
    所有更新操作已存入数组，需将其取出并添加到fiber的`update queue`（即`q`）中，用于后续提交。
- 遍历取值方式 [00:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=32)  
    从索引0开始循环遍历数组，按`fire QE up deal`顺序存取。每次取出后将原位置置空，以便下次更新复用全局变量。
- 初始化必要性 [00:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=56)  
    多次渲染共用同一全局变量，因此每次更新前需进行初始化，防止旧数据干扰。

### 单向循环链表的构建 
[02:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=120)

- 链表结构定义 [02:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=120)  
    `update queue`被构建成单向循环链表，通过头节点和尾节点维护环形结构。
- 头节点创建 [02:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=127)  
    若当前`update`为空，则将其作为头节点，并设置`update.next`指向自身形成闭环。
- 新节点插入 [02:21](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=141)  
    后续新增`update`时：
    - 将新节点接在尾节点之后。
    - 更新尾节点的`next`指针重新指向头节点，维持循环结构。
- 尾节点标记 [02:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=157)  
    `q.pending`始终指向尾节点，便于后续追加操作。

## markUpdateLaneFromFiberToRoot：向上遍历更新childLanes 
[02:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=170)

参考：[[markUpdateLaneFromFiberToRoot]]

- max函数作用 [02:44](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=164)  
    从当前fiber节点向上追溯至根节点，目的是更新路径上所有父节点的`childLanes`。
- 遍历逻辑 [03:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=205)  
    利用`return`指针逐层向上查找父节点，直到根节点为止。
- childLanes更新 [03:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=228)  
    每个父节点的`childLanes`需与当前子fiber的`lanes`进行位运算合并（`|=`），确保优先级传播。
- alternate链同步 [04:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=240)  
    对应的`alternate`树上的节点也需同步更新`childLanes`。


### finishQueueingConcurrentUpdates执行时机与次数 
[04:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=256)

- 执行场景分析 [04:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=256)  
    `finish`函数在`render`阶段开始和结束时各执行一次。
- 批量更新处理 [04:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=272)  
    即使存在多个`create`或`schedulerUpdateOnFiber`调用，最终只触发一次`finish`，因处理的是整个更新队列而非单个update。
- 队列 vs 单项处理区别 [05:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=314)
    - 前期如`create`、`NQ`等处理单项update。
    - `finish`处理的是已整合的`update queue`。


## processUpdateQueue：处理更新队列
[05:27](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=327)

**前一阶段的管理更新队列：update从全局暂存变量到fiber.updateQueue**
- 队列转移目标 [05:27](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=327)  
    将全局暂存的update转移到对应fiber节点的`update queue`中。
- 管理更新队列，完成队列构建 [05:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=340)  
    在进入render阶段前，确保所有更新已挂载至fiber结构。

### 处理更新队列：begin work阶段 
[05:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=354)

- 处理入口函数 [05:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=354)  
    `beginWork`是每个fiber节点处理更新的核心函数，会被递归调用。
- 协调流程启动 [06:53](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=413)  
    `workLoopSync`中通过`performUnitOfWork`逐一处理fiber节点，先处理当前节点的`update queue`，再协调其子节点。
- 适用节点类型 [07:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=436)  
    主要应用于`HostRoot`和`ClassComponent`，函数组件因无实例暂不涉及。

### 函数解析
[07:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=466)

参考：[[processUpdateQueue]]

这个函数**用来处理更新队列**。`processUpdateQueue`**在beginWork阶段会被两个地方调用：**
- updateHostRoot()中，packages/react-reconciler/src/ReactFiberBeginWork.new.js
- updateClassInstance()中，packages/react-reconciler/src/ReactFiberClassComponent.new.js


源码：packages/react-reconciler/src/ReactFiberClassUpdateQueue.new.js


- 函数调用位置 [08:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=484)  
    被`updateHostRoot`和类组件的`updateClassComponent`调用，参数分别为：
    - 当前fiber
    - 新props
    - 实例（仅类组件有）
- pending到base queue的转移 [08:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=528)  
    正在处理的pending队列需转移到baseQueue，表示从“待处理”变为“正在计算”。
- 检查pending存在性 [09:13](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=553)  
    通过判断q.pending是否为空，**确定是否有更新需要处理**。
- 断开循环链表 [10:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=624)  
    将pending的单向循环链表断开，使其变为普通单链表：
    - last.next = null

### 构建base queue的逻辑分支 
[10:44](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=644)

- baseQueue为空的情况 [10:44](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=644)  
    若baseQueue为空，则直接将firstPendingUpdate赋值给baseQueue.first。
- 更新尾指针 [11:10](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=670)  
    无论何种情况，均需更新baseQueue.last指向最新尾节点。
- base queue非空追加逻辑 [11:15](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=675)  
    若已有baseQueue，则将新节点接在其尾部：
    - last.next = newUpdate
- current queue不同步时的转移 [11:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=686)  
    检查current与workInProgress的队列差异，若不同则同步转移。

### 遍历并计算新状态 
[11:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=714)

- 遍历目的 [11:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=714)  
    将update queue中的每一个update依次执行，合并得出最终的newState。
- 核心计算函数 [14:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=892)  
    `$getDerivedStateFromUpdate$`根据update类型（对象或函数）计算部分状态：
    - 函数型：执行函数获取返回值。
    - 对象型：直接合并到状态。
- 状态合并规则 [14:45](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=885)  
    使用浅合并策略，后一个update的状态会覆盖前一个同名字段。

### 回调函数的处理与存储 
[15:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=925)

- 回调暂存机制 [15:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=925)  
    callback不会立即执行，而是保存在update中，待commit阶段完成后统一调用。
- 遍历下一个update [15:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=937)  
    设置update = update.next继续处理队列中的下一项。
- 检测新插入的update [15:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=951)  
    若遍历到末尾发现新的update加入，则重新开始遍历，保证所有更新都被处理。

### 最终状态写回fiber 
[16:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=979)

- 更新memoizedState [16:30](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=990)  
    计算出的newState最终赋值给fiber.memoizedState。
- 更新队列清空与重置 [18:27](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1107)  
    处理完毕后，pending队列被清空，first与last重置为初始状态。
- V DOM层面操作 [16:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=984)  
    整个过程属于虚拟DOM更新，尚未涉及真实DOM操作。

## 调试与验证方法 
[16:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1011)

- 打印调试技巧 [16:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1011)  
    可在关键节点插入console.log查看memoizedState、baseQueue等值的变化。
- 避免异步延迟影响 [17:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1038)  
    使用断点调试或拦截器确保打印的是实时值，而非延迟输出的旧状态。
- 示例组件说明 [20:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1252)  
    推荐使用类组件的ExamplePage进行测试，重点关注多次setState后的状态合并结果。

## 课程总结与重点回顾 
[21:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1260)

- 核心知识点归纳 [21:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1265)  
    详细讲解了类组件与createRoot产生的update数据结构及其处理算法。
- 关键流程强调 [21:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1279)
    - finish函数确保所有更新被处理。
    - processUpdateQueue实现状态合并与队列转移。
- 实践建议 [20:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ee07702ca0a74c808d527d89b526d87e#?seek_t=1205)  
    鼓励学生通过打日志方式跟踪执行流程，深入理解React更新机制。