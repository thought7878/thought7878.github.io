

本视频讲述了**React任务调度源码的核心逻辑**与自实现版本的对比分析，重点*解析了优先级管理、任务队列处理、时间切片执行机制*及兼容性方案，并*指出源码中未使用的冗余函数和历史遗留设计差异*。

React源码：https://github.dev/facebook/react/tree/v18.2.0

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第6章 React18全新底层核心运行机制：任务调度机制源码阅读，思维拔高/media/6870313bb835dfcb85e5808bf71bd795_MD5.webp]]

### 源码背景介绍 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=0)

- 课程目标说明  
    回顾此前手写实现的任务调度器主干逻辑，将其与React真实源码进行对比，验证一致性。
- Schedule源码位置与概况  
    源码位于`packages/scheduler/src/forks/Scheduler.js`下，共六百余行代码。
- 历史维护状态
    - `README.md`最后一次修改为六年前。
    - 曾计划将`schedule`做成通用包，但后续未持续维护。
    - 当前代码虽不够严谨，但无重大bug，仍可正常使用。

#### 代码量差异原因分析 
[01:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=84)

- 性能测试相关代码  
    源码包含大量`profiling`相关的函数，*用于性能监控，非主干逻辑*。
- *环境兼容性处理*  
    *支持多种运行环境：*
    - Node.js 环境
    - 老版本 IE 浏览器
    - 使用 `MessageChannel` 为主，不支持时降级使用 `setTimeout`
- MessageChannel 的兼容方案  
    若不支持 `MessageChannel`，则使用 `setTimeout` 作为后备方案（fallback）。

#### 入口函数与未使用函数说明 
[02:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=121)

- **主要对外接口函数**
    - `unstable_scheduleCallback`：任务调度器**与外界交互的核心函数**。
    - 手写实现也从该函数入手。
- 其他暴露函数的存在性分析
    - 如 `unstable_runWithPriority` 可能被用到。
    - 但如 `unstable_next` 等函数*在当前 React 中并未实际调用*。
- 验证方法
    - 方法一：在函数内添加 `console.log` 观察是否触发。
    - 方法二：全局搜索调用处，发现这些函数仅在测试或 mock 中出现，未在主流程中使用。
- 学习建议  
    学习应聚焦主干逻辑，忽略未使用的细枝末节。

### 优先级系统对比分析 
[03:33](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=213)


- 定义的**优先级类型**  packages/scheduler/src/SchedulerPriorities.js
    包括：
    - ImmediatePriority
    - UserBlockingPriority
    - NormalPriority
    - LowPriority
    - IdlePriority
- `当前优先级`全局变量
    - 变量名：`currentPriorityLevel`
    - 是一个**全局状态，记录正在执行任务的优先级**。
	- 初始值差异
	    - 手写实现中初始值为 NoPriority。
	    - 源码中初始值为 NormalPriority。
- getCurrentPriorityLevel 函数用途
    - *返回当前任务优先级*。
    - 在合成事件系统中被 getEventPriority(domEventName) 调用。
    - 根据不同 DOM 事件（如 click、message）返回对应优先级。
- noPriority 的缺失问题
    - 全局搜索 NoPriority，仅在 Fiber 相关代码中有使用，在 schedule 包内部未使用。
    - 即使无任务执行，getCurrentPriorityLevel() 仍返回 NormalPriority。
- 作者个人理解与 PR 尝试
    - 认为无任务时应为 NoPriority 更合理。
    - 提交过 PR 建议修改，但未被采纳，官方未给出详细解释。
    - 实际影响有限：无论返回 Normal 或 NoPriority，最终都会进入默认分支，不会导致 bug。

### 核心调度逻辑一致性验证 
[08:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=499)

- 关键全局变量
    - `taskQueue`：存放待执行任务的堆（heap）。
    - `timerQueue`：存放延迟任务的堆。
    - `startTime`：记录开始时间戳，用于时间切片计算。
- 时间切片机制
    - 利用时间片控制单次执行时长，避免主线程阻塞。
    - 每个时间片通常为 5ms 左右。

### 任务调度入口函数实现细节 
[08:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=509)

- 函数名称  
    `unstable_scheduleCallback`，至今仍标记为 unstable，表明其 API 不稳定。
- delay 参数处理
    - 支持传入 delay 控制延迟执行。
    - 手写实现保留此逻辑以保持与源码一致。
    - 但在 React 主体代码中实际未使用 delay 功能。
    - 所有任务均走 else 分支，直接进入 taskQueue。

### 新任务创建与插入逻辑 
[09:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=559)

- 等待时间（expirationTime）计算
    
    expirationTime=startTime+timeoutexpirationTime=startTime+timeout
    
- 不同优先级的 timeout 设置
    - 立即执行任务（Immediate）：timeout 为 `-1`，类比 VIP 用户负等待时间。
    - 其他任务根据优先级设定不同的超时时间。
- 延迟任务处理流程
    - 若任务有 delay，则先放入 `timerQueue`。
    - 到达指定时间后，由 `requestHostCallback` 触发转移至 `taskQueue`。
- 防止重复定时器设置
    - 使用标志位防止多个 delay 任务重复创建 `setTimeout`。
    - 只有当 `timerQueue` 非空且无活跃定时器时才设置。

### 调度触发条件 
[10:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=650)

- 调度前提
    - 当前没有其他任务正在调度（无 `requestHostCallback` 活跃）。
    - 没有其他 work 正在执行。
- requestHostCallback 实现
    - 手写实现已覆盖该逻辑。
    - 触发异步回调，启动 `performWorkUntilDeadline`。

### 异步执行机制兼容层 
[11:10](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=670)

- 执行函数选择顺序
    1. Node.js 环境：使用 `setImmediate`
    2. 浏览器环境：优先使用 `MessageChannel`
    3. 最终 fallback：使用 `setTimeout`
- MessageChannel 实现方式
    - 创建两个端口：`port1` 监听消息，`port2`发送消息。
    - 通过 `port2.postMessage()` 触发 `port1.onmessage`，实现微任务级异步调度。
- 主流浏览器支持情况  
    目前绝大多数现代浏览器均支持 `MessageChannel`。

### 工作循环主逻辑分析 
[12:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=743)

- performWorkUntilDeadline 函数作用  
    在时间切片内执行尽可能多的任务。
- 时间切片结束判断依据
    - 记录起始时间 `startTime`。
    - 当前时间减去 `startTime` 是否大于等于帧间隔（frame interval，约 5ms）。
    - 若超过，则退出当前循环，让出主线程控制权。

### 工作循环核心：workLoop 实现 
[12:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=776)

- flashWork 函数返回值含义
    - 返回 `true`：表示仍有任务需继续调度。
    - 返回 `false`：表示当前无更多任务。
- hasMoreWork 判断与后续调度
    - 若有更多任务，则再次调用 `requestHostCallback` 启动下一轮。
    - 否则本轮调度结束。

### 任务执行流程详解 
[13:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=804)

- timerQueue 有效性检查
    - 检查 `timerQueue` 中是否有到期任务。
    - 无效任务（如 `callback` 被设为 `null`）会被过滤。
- 任务获取顺序
    - 优先从 `taskQueue` 取任务。
    - 若 `taskQueue` 为空，则检查 `timerQueue` 并迁移到期任务。
- 任务有效性校验
    - 检查任务是否存在、`callback` 是否为函数。
    - 若无效，则从队列中移除并跳过。
- 过期时间检查
    - 只有到达或超过 `expirationTime` 的任务才可执行。
- 主线程让出条件
    - 当前运行时间 ≥ 时间切片长度时，停止执行，让出控制权。
    - 下一帧再由 `MessageChannel` 回调重新触发。

### 任务执行过程 
[15:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=909)

- 执行前准备
    - 将当前任务的 `callback` 置为 `null`，防止重复执行。
    - 更新 `currentPriorityLevel` 为当前任务优先级。
    - 供 `getCurrentPriorityLevel()` 等函数读取。
- callback 执行结果处理
    - 若返回一个函数，表示任务未完成，需在后续时间片继续执行。
    - 若返回非函数值，表示任务已完成。
- 任务完成后的清理
    - 从 `taskQueue` 中删除该任务。
    - 删除前需确认其仍是堆顶元素（防止并发修改）。
- timerQueue 定期检查
    - 调用 `advanceTimers` 检查 `timerQueue` 是否有任务到期。
    - 到期任务从 `timerQueue` 移至 `taskQueue`。
- 任务未完成时的处理
    - 保留 `callback` 引用。
    - `advanceTimers` 继续检查。
    - 返回 `true` 表示仍有工作，驱动下一次调度。

### 循环退出后状态处理 
[16:57](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=1017)

- workLoop 返回值决定调度行为
    - 返回 `true`：仍有任务，继续调度。
    - 返回 `false`：无任务，结束本轮。
- 无任务时对 timerQueue 的监听
    - 即使 `taskQueue` 为空，也要检查 `timerQueue`。
    - 若有延迟任务即将到期，设置 `setTimeout`等待唤醒。

### 总结与课程收尾 
[17:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=1071)

- 整体结论
    - 手写实现与 React 源码主干逻辑完全一致。
    - 差异仅在于：
        - profiling 性能检测代码
        - 多环境兼容处理
        - 语法层面（Flow vs TS）
- 算法部分说明
    - 任务队列使用的堆结构算法与源码一致。
    - 未展开讲解，因已在手写章节详述。
- 课程结束提示  
    提供一张图展示 `schedule` 与外部系统的联系，帮助理解整体架构。