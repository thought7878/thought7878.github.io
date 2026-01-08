

本视频讲述了**如何使用MessageChannel模拟实现requestIdleCallback以构建异步任务队列**，*实现高效的任务调度与批量更新，避免主线程长时间占用，提升页面性能*，并*对比了setTimeout等替代方案的局限性*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/eb9fc9c847158c6bbeada4a99fdb497e_MD5.webp]]


React使用MessageChannel创建宏任务，来实现异步任务队列，以实现异步更新，确保React*在执行更新时能够合并多个更新操作*，并在下一个宏任务中一次性更
新，以提高性能并减少不必要的重复渲染，从而提高页面性能和用户体验。

### 为何需要实现 requestIdleCallback 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=0)

- 背景介绍 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=0)  
    *浏览器原生提供 `requestIdleCallback` 函数*，用于**在浏览器空闲时期执行任务**。
- React 中的任务调度需求 [00:20](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=20)  
    React 希望实现任务调度器，避免高优先级任务被长期阻塞，但*无法直接控制浏览器级别的* `requestIdleCallback`。
- 自定义实现的必要性 [00:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=51)  
    直接使用 `requestIdleCallback` 控制粒度不足，因此 React 自行模拟其实现机制。

### 核心机制：使用 MessageChannel **创建宏任务** 
[01:12](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=72)

- ***目标*：实现异步任务队列** [01:20](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=80)  
    **通过创建宏任务实现异步更新，达到*批量处理状态变更、减少重复渲染*的目的**。
- **MessageChannel 的优势** [01:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=109)  
    *使用 `MessageChannel` 可创建无最小延迟的宏任务，相比 `setTimeout`（即使设为0也有4ms延迟）性能更优*。
- 选择 MessageChannel 的原因 [02:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=139)  
    React 最终选用 `MessageChannel` 而非 `setTimeout`，*因其时间精度更高，更适合高频调度场景*。

### 任务调度流程设计 
[02:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=160)

- work loop 的执行前提 [02:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=160)  
    **执行前需确保没有其他异步任务正在运行，防止重复创建宏任务**。
- 锁机制的引入：isMessageLoopRunning [02:53](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=173)  
    使用布尔变量 `isMessageLoopRunning` *作为锁，防止重复调度*。
- 调度入口函数 schedule [03:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=203)  
    若无任务运行，则设置 `isMessageLoopRunning = true`，并启动 `performWorkUntilDeadline`。

### MessageChannel 具体实现 
[04:13](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=253)

- 创建 MessageChannel 实例 [04:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=257)  
    初始化 `const channel = new MessageChannel()`，获取两个端口 `port1` 和 `port2`。
- 监听与触发机制 [04:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=269)
    - `port1.onmessage` 监听消息，触发 `performWorkUntilDeadline`。
    - `port2.postMessage()` **发送消息，激活监听，形成宏任务**。
- ***宏任务的创建过程*** [04:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=296)  
    *调用 `schedule` 时通过 `port2.postMessage()` 触发 `port1` 的 `onmessage` 回调，从而创建一个可执行的宏任务*。

### performWorkUntilDeadline 函数实现 
[05:02](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=302)

- 函数结构初始化 [05:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=318)  
    检查 `isMessageLoopRunning` 是否为真，决定是否继续执行。
- 记录起始时间戳 [05:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=335)  
    使用 `startTime = performance.now()` 记录当前时间戳，用于后续时间切片判断。
- 执行工作循环 flashWork [06:25](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=385)  
    调用 `flashWork(startTime)` 开始执行任务队列。
- 返回值处理：hasMoreWork [06:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=406)  
    获取 `flashWork` 返回值 `hasMoreWork`，判断是否还有待处理任务。
- 后续调度决策 [07:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=428)
    - 若 `hasMoreWork` 为真，再次调用 `schedule()` 继续调度。
    - 否则设置 `isMessageLoopRunning = false`，结束本轮调度。

### flashWork 函数实现 
[07:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=449)

- 参数接收：initialTime [07:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=455)  
    接收时间切片起始时间戳 `initialTime`。
- 开关设置：isPerformingWork [08:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=487)  
    设置 `isPerformingWork = true` 表示当前正在执行任务。
- 优先级管理 [08:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=506)  
    记录当前优先级 `previousPriorityLevel = currentPriorityLevel`，执行完后恢复。
- 执行 workLoop [08:11](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=491)  
    在 `flashWork` 内部调用 `workLoop(initialTime)` 执行具体任务。
- 清理操作 [08:20](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=500)  
    执行完成后将 `currentTask = null`，并还原优先级与执行状态。

### 时间切片与任务中断机制 
[13:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=817)

- shouldYieldToHost 函数作用 [13:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=817)  
    在 `workLoop` 中持续检查是否应中断当前任务。
- 时间判断逻辑 [13:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=830)  
    当前时间减去起始时间若超过时间切片阈值（通常约5ms），则返回 `true`，表示应中断。
- 任务分片执行 [14:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=847)  
    单个 `work` 内可执行多个任务，数量由任务耗时和剩余时间共同决定。

### 测试用例验证 
[09:28](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=568)

- 测试任务队列 setup [09:28](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=568)  
    初始化空数组 `eventTasks`，按不同优先级调度四个任务。
- 执行顺序分析 [09:44](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=584)
    - 最高优先级任务先执行（如 `immediate`）。
    - 相同优先级按入队顺序执行（FIFO）。
- 最终结果验证 [10:30](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=630)  
    数组输出为 `[task3, task2, task1, task4]`，符合预期调度顺序，测试通过。

### 总结与源码对比 
[10:36](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=636)

- 实现成果总结 [10:36](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=636)  
    成功模拟 `requestIdleCallback`，构建基于 `MessageChannel` 的异步任务调度系统。
- 与原生函数的区别 [10:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=651)  
    自实现版本可完全控制调度行为，支持取消、暂停等操作，而原生 `requestIdleCallback` 不可控。
- 关于 delay 参数的说明 [15:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=916)  
    React 源码中存在处理 `delay` 的逻辑，但在当前版本中并未实际使用。
- 后续章节预告 [16:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=974)  
    后续课程将补充实现带 `delay` 的任务调度逻辑，以保持与源码结构一致，尽管其未被实际启用。