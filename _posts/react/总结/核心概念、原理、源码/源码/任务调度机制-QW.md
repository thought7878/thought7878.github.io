# React源码中的任务调度机制

React的任务调度机制是其性能优化的核心，尤其在React 18中通过并发特性得到了进一步增强。这套机制确保了UI响应性，同时高效处理各种优先级的更新任务。

## 任务调度的演进

- **React 15及以前**：采用Stack Reconciler，递归遍历无法中断
- **React 16**：引入Fiber架构，将渲染拆分为可中断的小任务
- **React 18**：完善并发特性，通过精细的优先级管理优化用户体验

## 核心概念

### 1. 任务优先级系统 (Lane模型)
React 18使用"Lane"(车道)模型代替旧的"ExpirationTime"模型：
- **同步车道(Sync)**：如点击事件、表单输入等紧急更新
- **输入连续车道(Input Continuous)**：拖拽、滚动等
- **默认车道(Default)**：一般数据获取
- **过渡车道(Transition)**：通过startTransition标记的非紧急更新
- **空闲车道(Idle)**：预加载等可延迟操作

高优先级任务可以打断低优先级任务的执行，确保用户交互的流畅性。

### 2. Scheduler调度器
核心调度逻辑位于`scheduler`包中：
```javascript
// 简化的调度实现
const channel = new MessageChannel();
const port = channel.port2;
channel.port1.onmessage = flushWork; // 消息通道处理回调

function scheduleCallback(priorityLevel, callback) {
  // 创建任务并加入优先级队列
  // 通过port.postMessage安排执行
}
```

调度器使用MessageChannel实现微任务队列，比requestIdleCallback更可控，能够精确管理任务执行时机。

### 3. 工作循环与时间切片
在`ReactFiberWorkLoop.js`中实现：
```javascript
function workLoopConcurrent() {
  // 每次执行一个工作单元，然后检查是否需要让出主线程
  while (workInProgress !== null && !shouldYield()) {
    performUnitOfWork(workInProgress);
  }
}

// 检查是否需要让出主线程(约5ms执行时间)
function shouldYield() {
  return scheduler.unstable_shouldYield();
}
```

这种机制将大型更新拆分为多个5ms左右的小任务，确保主线程不会长时间阻塞。

## 调度流程

1. **任务创建**：根据触发源(用户交互、数据获取等)分配优先级
2. **任务入队**：更新被放入对应优先级的队列
3. **任务执行**：
   - 高优先级任务(如同步任务)立即执行
   - 低优先级任务通过Scheduler安排在空闲时间执行
   - 执行过程中定期检查是否需要让出主线程
4. **中断与恢复**：高优先级任务到来时可中断当前任务，完成后恢复执行
5. **提交阶段**：变更最终应用到DOM(此阶段不可中断)

## React 18的新特性

- **startTransition API**：明确区分紧急和非紧急更新
  ```javascript
  startTransition(() => {
    // 非紧急更新，会在空闲时间执行
    setSearchQuery(input);
  });
  ```

- **Suspense集成**：在数据加载时提供平滑的加载状态
- **批处理优化**：自动批处理多个状态更新，减少渲染次数

## 源码核心文件
- `react-reconciler/src/ReactFiberWorkLoop.js`：工作循环实现
- `scheduler/src/Scheduler.js`：调度器核心
- `react-reconciler/src/ReactFiberLane.js`：任务优先级系统

通过这套精密的调度机制，React能在复杂的UI场景下保持高响应性，为用户提供流畅的交互体验。在2026年的今天，这些机制依然是React性能优势的核心所在，并持续随着新版本不断优化。