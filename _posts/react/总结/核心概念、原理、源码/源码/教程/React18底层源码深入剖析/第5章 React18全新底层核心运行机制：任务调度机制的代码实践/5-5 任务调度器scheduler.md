

本视频讲述了**React中`任务调度器（schedule）`的实现原理与基本框架搭建**，包括*任务优先级划分、最小堆数据结构的应用、关键函数*如`scheduleCallback`和`cancelCallback`的设计思路。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/db68845054646a2485993bd78ff07a69_MD5.webp]]

### **问题**：任务调度的必要性 
[00:15](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=15)

- **多任务执行场景**  
    React项目运行过程中*会遇到多个更新任务*，*如*effect触发或用户交互导致的任务。
- **单线程执行限制**  
    *React每次只能执行一个任务*，**因此需要调度策略决定任务的执行顺序**。

### Scheduler调度器简介 
[00:38](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=38)

- 独立模块  
    Scheduler是一个独立的任务调度器，不直接依赖于React，*可应用于其他单线程任务调度场景*。
- **源码位置**  
    源码位于`react/packages/scheduler`文件夹下。
- **目标功能**  
    实现一个*基于优先级、到达时间*的*单线程任务调度机制*。

### 任务优先级设计 
[02:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=137)

- **优先级分类**
    - NoPriority: 0
    - Immediate: 1
    - UserBlocking: 2
    - Normal: 3
    - Low: 4
    - Idle: 5
- 导出定义  
    优先级常量在`schedulePractice.ts`中定义并导出供其他模块使用。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/bdc612234ea10d60cbc5ebcd05a596e6_MD5.webp]]

### 任务池与数据结构 
[05:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=335)

- 最小堆结构  
    **使用最小堆管理任务队列**，便于**高效获取当前优先级最高的任务**。
- 任务池变量命名  
    `taskQueue`用于**存储任务对象**。

### 核心函数定义 
[06:41](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=401)

- `scheduleCallback`函数  
    **某个任务进入调度器，等待调度**。*任务调度器的入口函数*，接收两个参数：
    - 回调函数（callback）
    - 优先级（priorityLevel）
    - 返回值类型为可选回调（用于未完成任务继续执行）
- `cancelCallback`函数：取消某个任务，由于最小堆没法直接删除，因此只能初步把task.callback设置为null
    取消指定任务的执行，通过将任务回调置为`null`标记为无效。
    
- getCurrentPriorityLevel函数：获取当前正在执行任务的优先级
    获取当前正在执行任务的优先级，需维护全局变量`currentPriorityLevel`。
    
- shouldYield函数（暂未实现）  
    用于判断是否让出主线程控制权，把控制权交还给主线程。
    

### 全局变量定义 
[10:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=623)

- currentTask  
    全局变量，记录当前正在执行的任务。
- currentPriorityLevel  
    当前任务的优先级，取值与`priorityLevel`一致。

### 测试用例说明 
[12:40](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=760)

- 同优先级任务处理  
    若两个任务优先级相同，则按到达顺序执行（FIFO）。
- 不同优先级任务处理  
    高优先级任务优先执行，若优先级相同则依据到达时间排序。
- 具体测试示例：
    - 示例一：两个Normal任务按顺序执行。
    - 示例二：Normal、UserBlocking、Immediate任务按优先级排序。
    - 示例三：四个不同优先级任务按优先级与到达顺序综合排序。

### 课程总结 
[15:44](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=944)

- 本节课介绍了React中任务调度的基本概念、优先级划分方式以及调度器的核心函数和数据结构。
- 下一步将实现`scheduleCallback`等核心逻辑，并完善任务调度流程。
- 建议学员理解测试用例并思考后续代码实现方式。