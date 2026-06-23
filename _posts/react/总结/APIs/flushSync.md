`ReactDOM.flushSync` 是 React 18 引入的一个重要 API（包含在 `react-dom` 包中），它的核心作用是**强制 React 同步刷新（flush）回调函数内的所有状态更新，并确保 DOM 立即更新** [[1]]。

在绝大多数日常开发中，你不需要用到它。但当你遇到某些需要打破 React 默认渲染机制的特殊场景时，它是一个必不可少的工具。以下是关于它的详细解析：

---

### 一、 为什么需要 `flushSync`？（背景：React 18 的自动批处理）

要理解 `flushSync`，首先要了解 React 18 的**自动批处理（Automatic Batching）** 机制。
*   **在 React 17 及以前**：React 只能在自身的事件处理函数（如 `onClick`）中将多个 `setState` 合并为一次渲染。如果在 `setTimeout`、`Promise.then` 或原生 DOM 事件中更新状态，React 不会进行批处理，这会导致组件发生多次无意义的重新渲染 [[14]]。
*   **在 React 18 及以后**：React 引入了自动批处理，无论状态更新发生在哪里（包括 `setTimeout`、网络请求回调、原生事件等），React 都会默认将它们合并为一次渲染，从而大幅提升性能 [[18]]。

虽然自动批处理对性能非常友好，但在极少数情况下，开发者**必须**在状态更新后立刻获取最新的 DOM。此时，自动批处理导致的“异步更新”反而成了阻碍。`flushSync` 就是为了让你能够**选择性地退出自动批处理**而设计的 API [[10]]。

---

### 二、 基本语法

```javascript
import { flushSync } from 'react-dom';

function handleClick() {
  // 如果不使用 flushSync，下面的两次 setState 会被批处理，只触发一次渲染
  
  // 使用 flushSync 后，DOM 会立刻同步更新
  flushSync(() => {
    setCounter(c => c + 1);
  });
  // 此时 DOM 已经是最新状态，可以立即进行测量或读取
  
  flushSync(() => {
    setFlag(f => !f);
  });
  // DOM 再次立刻同步更新
}
```

---

### 三、 典型应用场景

#### 1. 状态更新后立即测量或操作 DOM
这是 `flushSync` 最常见的场景。当你需要根据新的状态去读取 DOM 的尺寸（如 `getBoundingClientRect`）、计算滚动高度或自动滚动到列表底部时，由于 React 默认是异步渲染的，直接读取拿到的往往是旧的 DOM。使用 `flushSync` 可以保证在执行下一行代码时，DOM 已经挂载或更新完毕 [[4]]。

```javascript
import { useRef, useState } from 'react';
import { flushSync } from 'react-dom';

function TodoList() {
  const [todos, setTodos] = useState([]);
  const listRef = useRef(null);

  const addTodo = (text) => {
    // 强制同步更新 DOM
    flushSync(() => {
      setTodos([...todos, { id: Date.now(), text }]);
    });
    
    // 因为使用了 flushSync，此时 DOM 已经渲染出新的列表项
    // 我们可以安全地操作滚动条，将其滚动到底部
    listRef.current.scrollTop = listRef.current.scrollHeight;
  };
  // ...
}
```

#### 2. 在执行重度阻塞任务前强制显示 Loading UI
如果在设置一个 Loading 状态后，紧接着要执行一个非常耗时且阻塞主线程的同步计算，默认的异步渲染可能导致 Loading 界面来不及绘制（用户感知到的是界面卡死，看不到 Loading 动画）。使用 `flushSync` 可以确保 Loading UI 优先渲染到屏幕上。

```javascript
function handleHeavyTask() {
  flushSync(() => {
    setIsLoading(true); // 强制先渲染 Loading 状态
  });
  
  // 执行耗时且阻塞主线程的操作...
  doHeavySyncWork();
  
  setIsLoading(false);
}
```

#### 3. 与第三方非 React 库集成
当你使用依赖真实 DOM 节点的第三方库（如图表库 ECharts、地图库、视频播放器等）时，如果 React 状态更新后 DOM 还没挂载，第三方库可能会报错或渲染空白。`flushSync` 可以确保 DOM 准备就绪后再通知第三方库进行初始化或更新。

---

### 四、 注意事项与副作用（⚠️ 非常重要）

虽然 `flushSync` 能解决特定问题，但官方强烈建议**将其作为最后的手段**，在大多数情况下都应该尽量避免使用它 [[22]]。滥用它会带来以下负面影响：

1.  **严重损害性能**：`flushSync` 会强制刷新整个更新树，迫使那些本来可以合并的更新发生完整的重新渲染，从而抵消批处理带来的性能优势 [[15]]。
2.  **引发 Suspense Fallback 闪烁**：如果 `flushSync` 内部触发的状态更新涉及到处于挂起（Pending）状态的 `Suspense` 边界，React 可能会被迫立即显示其 `fallback` 内容（例如 Loading 骨架屏），这可能会导致令人不快的 UI 闪烁体验 [[1]]。
3.  **不能在渲染期间调用**：如果你在组件的渲染阶段（Render phase）或者生命周期方法内部调用 `flushSync`，React 会抛出错误，提示不能在 React 已经处于渲染状态时调用它 [[5]]。
4.  **不能用于服务端渲染 (SSR)**：在服务端渲染环境中调用 `flushSync` 会引发报错，因为服务端没有真实的浏览器 DOM 供其“同步刷新” [[6]]。

---

### 五、 总结

| 特性 | 说明 |
| :--- | :--- |
| **核心作用** | 强制退出 React 18 的自动批处理，同步执行状态更新并立即更新真实 DOM。 |
| **何时使用** | 急需在状态更新后的**下一行代码**中操作/测量最新 DOM，或在执行阻塞任务前强制显示 UI。 |
| **何时不使用** | 日常的状态切换、常规的数据请求后更新 UI（React 默认的异步渲染已经完全够用且性能更好）。 |
| **副作用** | 破坏性能优化、可能导致 Suspense 闪烁、无法在 SSR 或 Render 阶段使用。 |