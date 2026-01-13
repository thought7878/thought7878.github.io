我将为您解释这个 React 调度器（Scheduler）的源代码。

## 代码概述

这是 React 的调度系统实现，用于**管理任务优先级和执行时机**。它**通过智能地安排不同优先级的任务在浏览器空闲时间执行，以优化用户体验并避免主线程阻塞**。

## 核心概念

### 优先级级别
```javascript
var IMMEDIATE_PRIORITY_TIMEOUT = -1;         // 立即执行
var USER_BLOCKING_PRIORITY_TIMEOUT = 250;    // 用户阻塞性任务，250ms后过期
var NORMAL_PRIORITY_TIMEOUT = 5000;          // 普通任务，5000ms后过期
var LOW_PRIORITY_TIMEOUT = 10000;            // 低优先级任务，10000ms后过期
var IDLE_PRIORITY_TIMEOUT = maxSigned31BitInt; // 空闲时执行
```

### 数据结构
调度器使用两个队列来管理任务：
- [taskQueue](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L58-L58)：当前可执行的任务队列，按过期时间最小堆排序
- [timerQueue](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L59-L59)：延迟任务队列，按开始时间最小堆排序

## 主要功能函数

### unstable_scheduleCallback：任务调度入口
scheduleCallback（安排、调度一个任务回调函数）

[unstable_scheduleCallback](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L425-L499) 是**主要的调度函数，对外接口函数，任务调度器与外界交互的核心函数**：
- **创建一个新任务**
- **将其添加到适当的队列中**
- **安排执行任务**

**伪代码：**
- *创建一个新任务：*
	- 任务的id：id: taskIdCounter++
	- 任务的callback：入参参数callback，将任务回调函数封装成任务的callback
	- 任务的优先级：入参参数priorityLevel
	- 任务的开始时间（不是执行时间）：
	    - 处理延迟选项，有delay，startTime = currentTime + delay
	    - 其他情况：startTime = currentTime
    - 任务的过期时间：expirationTime = startTime + timeout
	    - timeout，根据优先级设置超时时间
    - 任务的排序索引 sortIndex：
	    - 如果是延迟任务，newTask.sortIndex = startTime
	    - 如果是一个立即执行的任务，设置任务的排序索引为过期时间（最小二叉堆排序使用）
- *将新任务添加到适当的队列中*
	- 如果是延迟任务，放入 timerQueue；
    - 否则，是一个立即执行的任务，
	    - 放入 taskQueue
	    - *安排执行任务，requestHostCallback(flushWork);*

```javascript
function unstable_scheduleCallback(priorityLevel, callback, options) {
  // 获取当前时间
  var currentTime = getCurrentTime();

  // 处理延迟选项
  var startTime = currentTime;
  if (typeof options === 'object' && options !== null) {
    var delay = options.delay;
    if (typeof delay === 'number' && delay > 0) {
      startTime = currentTime + delay;
    }
  }

  // 根据优先级设置超时时间
  var timeout;
  switch (priorityLevel) {
    case ImmediatePriority: timeout = IMMEDIATE_PRIORITY_TIMEOUT; break;
    // ... 其他优先级
  }

  // 计算任务的过期时间（设置任务的排序索引为过期时间，最小二叉堆排序使用）
  var expirationTime = startTime + timeout;

  // 构建任务对象，创建新任务对象
  var newTask = {
    id: taskIdCounter++,
    callback,
    priorityLevel,
    startTime,
    expirationTime,
    sortIndex: -1,
  };

  // 如果是延迟任务，放入 timerQueue；否则放入 taskQueue
  if (startTime > currentTime) {
    newTask.sortIndex = startTime;
    push(timerQueue, newTask);
    // 设置超时处理
    if (peek(taskQueue) === null && newTask === peek(timerQueue)) {
      requestHostTimeout(handleTimeout, startTime - currentTime);
    }
  } else {
    // 否则放入 taskQueue
    newTask.sortIndex = expirationTime;
    push(taskQueue, newTask);
    // 安排执行
    if (!isHostCallbackScheduled && !isPerformingWork) {
      isHostCallbackScheduled = true;
      // 下一个
      requestHostCallback(flushWork);
    }
  }

  return newTask;
}
```

### requestHostCallback(flushWork)
如果消息循环（时间切片）未运行；触发、安排、启动，下一个时间切片（安排执行work、任务，直到截止时间）

```js

// 请求主回调
// scheduledHostCallback = callback = flushWork
function requestHostCallback(callback) {
  scheduledHostCallback = callback;  // 设置计划的主回调，即flushWork
  if (!isMessageLoopRunning) {      // 如果消息循环（时间切片）未运行
    isMessageLoopRunning = true;    // 启动消息循环
    schedulePerformWorkUntilDeadline();  // ！！！触发、安排、启动，下一个时间切片（安排执行work、任务，直到截止时间）
  }
}

```

### schedulePerformWorkUntilDeadline
触发、安排、启动，下一个时间切片（安排执行work、任务）

```js
let schedulePerformWorkUntilDeadline;
if (typeof localSetImmediate === 'function') {
  // Node.js 和旧版 IE。
  // 有几个原因使我们更喜欢 setImmediate。
  //
  // 与 MessageChannel 不同，它不会阻止 Node.js 进程退出。
  // （即使这是 DOM 的调度器分支，你可能会在这里
  // 使用 Node.js 15+ 和 jsdom 的混合。）
  // https://github.com/facebook/react/issues/20756
  //
  // 但也是，它运行得更早，这是我们想要的语义。
  // 如果其他浏览器实现了它，最好使用它。
  // 尽管这些都比原生调度要差。
  schedulePerformWorkUntilDeadline = () => {
    localSetImmediate(performWorkUntilDeadline);
  };
} else if (typeof MessageChannel !== 'undefined') {
  // DOM 和 Worker 环境。
  // 我们更喜欢 MessageChannel 是因为 4ms 的 setTimeout 限制。
  const channel = new MessageChannel();
  const port = channel.port2;
  channel.port1.onmessage = performWorkUntilDeadline;
  // ！！！触发、安排、启动，下一个时间切片（安排执行work、任务）
  schedulePerformWorkUntilDeadline = () => {
    // 触发 performWorkUntilDeadline
    port.postMessage(null);
  };
} else {
  // 我们只应在非浏览器环境中回退到这里。
  schedulePerformWorkUntilDeadline = () => {
    localSetTimeout(performWorkUntilDeadline, 0);
  };
}
```

### performWorkUntilDeadline
新的时间切片开始执行
执行work（执行多个task）直到时间切片结束

```js

// 执行work（执行多个task）直到时间切片结束
// 直到截止时间的工作执行函数
const performWorkUntilDeadline = () => {
  if (scheduledHostCallback !== null) {
    const currentTime = getCurrentTime();
    // 时间切片的起始时间
    // 跟踪开始时间，以测量主线程被阻塞了多长时间
    startTime = currentTime;
    const hasTimeRemaining = true;

    // 如果调度程序任务抛出异常，退出当前浏览器任务，
    // 以便错误可以被观察到。
    //
    // 故意不使用 try-catch，因为这会使某些调试技术更困难。
    // 相反，如果 scheduledHostCallback 出错，则
    // hasMoreWork 将保持为 true，我们将继续工作循环。
    let hasMoreWork = true;
    try {
      // ！！！scheduledHostCallback = callback = flushWork; flushWork --> workLoop;
      // 开始 flushWork(hasTimeRemaining, initialTime)
      // hasMoreWork，来自flushWork的返回值，来自workLoop的放回值，true：currentTask !== null，任务队列中还有task；false：没有task了
      hasMoreWork = scheduledHostCallback(hasTimeRemaining, currentTime);
    } finally {
	  // 如果还有更多task
      if (hasMoreWork) {
        // ！！！当前的时间切片结束时，安排下一个时间切片，安排执行work、任务，直到截止时间
        // 在前一个（当前）消息事件结束时，安排下一个消息事件（安排下一个微任务）
        schedulePerformWorkUntilDeadline();
      } else {//任务队列中，没有任务了
        isMessageLoopRunning = false;  // 消息循环停止
        scheduledHostCallback = null;  // 清空计划的主机回调
      }
    }
  } else {
    isMessageLoopRunning = false;  // 消息循环停止
  }
  // 让出给浏览器将给它绘制的机会，因此我们可以重置这个值
  needsPaint = false;
};

```

### flushWork

```js

/**
 * 刷新工作的主函数
 * @param {*} hasTimeRemaining
 * @param {*} initialTime
 * @returns workLoop的返回值：true：currentTask !== null，仍有工作要做；false：没有更多工作
 */
function flushWork(hasTimeRemaining, initialTime) {
  if (enableProfiling) {
    markSchedulerUnsuspended(initialTime); // 标记调度器恢复
  }

  // 下次调度工作时需要主机回调
  isHostCallbackScheduled = false;
  if (isHostTimeoutScheduled) {
    // 我们计划了一个超时，但它不再需要了，取消它
    isHostTimeoutScheduled = false;
    cancelHostTimeout();
  }

  isPerformingWork = true;  // 标记正在进行工作
  const previousPriorityLevel = currentPriorityLevel; // 保存之前的优先级
  try {
    if (enableProfiling) {
      try {
        return workLoop(hasTimeRemaining, initialTime); // 执行工作循环
      } catch (error) {
        if (currentTask !== null) {
          const currentTime = getCurrentTime();
          markTaskErrored(currentTask, currentTime);  // 标记任务错误
          currentTask.isQueued = false;
        }
        throw error;
      }
    } else {
      // 生产环境中不捕获异常
      return workLoop(hasTimeRemaining, initialTime);
    }
  } finally {
    currentTask = null;  // 清除当前任务
    currentPriorityLevel = previousPriorityLevel; // 恢复之前的优先级
    isPerformingWork = false;  // 标记工作完成
    if (enableProfiling) {
      const currentTime = getCurrentTime();
      markSchedulerSuspended(currentTime);  // 标记调度器暂停
    }
  }
}

```

### workLoop：任务执行循环
[workLoop](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L208-L268) 函数**负责实际执行任务**：

- 获取任务队列（最小二叉堆）中的第一个任务，currentTask = peek(taskQueue);
- **进入执行任务的循环：**
	- 条件：还有当前任务、当前任务不为空，且不处于调试暂停状态
	- *检查是否需要让出主线程的控制权*（控制权给浏览器）：
		- 当前任务未过期，且（没有剩余时间或应该让出给主机），跳出循环。
		- 当前任务已过期，赶紧执行下面的逻辑
	- 执行任务的回调，或弹出任务：
		- 如果任务是有效的（回调函数存在）
			- 清空任务回调，防止重复执行
			- 设置当前优先级为任务优先级
			- *执行任务的回调函数*，参数为任务是否已过期
				- *如果任务还没完成*（返回了延续回调），将延续回调保存到任务，continuationCallback由scheduler的*使用者（外部使用者）控制*
				- *如果任务完成了*（没有延续回调），如果当前任务仍是队列中的第一个（任务队列是动态的，可能不是第一个），*从队列中弹出任务*
		- 如果任务是无效的（回调函数不存在）
			- 从任务队列，*弹出任务*，pop(taskQueue);
	- *获取下一个任务，继续下一个循环*，currentTask = peek(taskQueue);
- 跳出任务循环后，任务队列taskQueue中是否还有任务：
	- **还有任务，返回true，等下一个时间切片继续执行（flushWork-->workLoop）**
	- 没有任务了：
		- 返回false
		- 处理计时器队列timerQueue中的任务

```javascript
function workLoop(hasTimeRemaining, initialTime) {
  let currentTime = initialTime;
  advanceTimers(currentTime);
  currentTask = peek(taskQueue);
  
  while (
    currentTask !== null &&
    !(enableSchedulerDebugging && isSchedulerPaused)
  ) {
    // 检查是否需要让出主线程的控制权（控制权给浏览器）
    // 当前任务未过期，且（没有剩余时间或应该让出给主机），跳出循环
    if (
      currentTask.expirationTime > currentTime &&
      (!hasTimeRemaining || shouldYieldToHost())
    ) {
      break;
    }
    
    // 执行任务
    const callback = currentTask.callback;
    if (typeof callback === 'function') {
      currentTask.callback = null;
      currentPriorityLevel = currentTask.priorityLevel;
      
      const continuationCallback = callback(currentTask.expirationTime <= currentTime);
      currentTime = getCurrentTime();
      
      if (typeof continuationCallback === 'function') {
        // 任务未完成，放回队列继续执行
        currentTask.callback = continuationCallback;
      } else {
        // 任务完成，从队列中移除
        pop(taskQueue);
      }
    } else {
      pop(taskQueue);
    }
    
    advanceTimers(currentTime);
    
    // 获取下一个任务，继续下一个循环
    currentTask = peek(taskQueue);
  }
  
  return currentTask !== null;
}
```

### 时间切片机制
`调度器`使用`时间切片`技术**防止长时间占用主线程**。[shouldYieldToHost](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L564-L606) 函数**决定何时让出控制权**：

```javascript
function shouldYieldToHost() {
  const timeElapsed = getCurrentTime() - startTime;
  if (timeElapsed < frameInterval) {
    return false;  // 运行时间太短，不释放
  }

  // 检查是否有高优先级任务（如用户输入或绘制）
  if (enableIsInputPending) {
    if (needsPaint) {
      return true;  // 需要绘制，让出控制权
    }
    if (timeElapsed < continuousInputInterval) {
      // 只检查离散输入（如点击）
      if (isInputPending !== null) {
        return isInputPending();
      }
    } else if (timeElapsed < maxInterval) {
      // 检查所有输入类型
      if (isInputPending !== null) {
        return isInputPending(continuousOptions);
      }
    } else {
      // 已经阻塞太久，无论如何都要让出
      return true;
    }
  }
  
  return true;  // 默认让出控制权
}
```

## 特性说明

### 延迟任务处理
当任务设置了延迟执行，它会被放入 [timerQueue](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L59-L59)，直到延迟结束才移到 [taskQueue](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%99/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L58-L58) 中等待执行。

### 任务优先级管理
- [unstable_runWithPriority](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L323-L343)：临时改变当前优先级
- [unstable_wrapCallback](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%99/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L372-L386)：包装回调函数，保留其原始优先级

### 性能监控
当启用性能分析时，调度器会记录任务运行、暂停、完成等事件，并提供 API 用于性能分析。

### 浏览器兼容性
调度器支持多种异步执行方式：
1. [setImmediate](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/scheduler/src/forks/Scheduler.js#L67-L69)（Node.js 和旧版 IE）
2. [MessageChannel](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/shared/consoleWithStackDev.js#L30-L30)（DOM 和 Worker 环境）
3. setTimeout（非浏览器环境）

这个调度系统是 React 并发特性的基础，确保了高优先级任务（如用户交互）能够及时响应，同时允许低优先级任务（如页面更新）在后台进行。