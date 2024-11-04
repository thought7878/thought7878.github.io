
## useTransition

### Why
标记某些状态更新为“过渡”状态，*告诉React当前任务比较耗时*（异步任务或耗时的大量计算的同步任务），从而允许 React 在后台*优先处理其他任务，使应用在等待数据加载时仍然保持响应*。提升应用的响应性和流畅性，优化用户体验。

### 基本用法

`useTransition` 返回一个包含两个元素的数组：一个布尔值 `isPending` 和一个 `startTransition` 函数。`isPending` 用于指示*当前是否有正在进行的过渡*，而 `startTransition` 用于启动一个过渡任务。

#### 语法
```javascript
const [isPending, startTransition] = useTransition();
```

1. **`isPending`**：一个布尔值，表示当前是否有正在进行的过渡。你可以*使用它来显示加载指示器或禁用按钮*。
2. **`startTransition`**：一个函数，用于启动一个过渡。传递给 `startTransition` 的回调函数中的状态更新会被标记为“过渡”，允许 React 在后台处理这些更新，优先处理其他任务。

#### 示例

假设你有一个应用，用户*点击按钮后会触发一个耗时的操作*（如从服务器获取数据），这个操作会导致界面的重新渲染。为了提升用户体验，你可以*使用 `useTransition` 来标记这个操作为过渡，让用户在等待数据加载时仍然可以与应用的其他部分进行互动*。

```jsx
import React, { useState, useTransition } from 'react';

function App() {
  const [data, setData] = useState(null);
  const [isPending, startTransition] = useTransition();

  const fetchData = () => {
    startTransition(() => {
      // 模拟异步数据获取
      setTimeout(() => {
        setData({ message: 'Data fetched successfully!' });
      }, 2000);
    });
  };

  return (
    <div>
      <button onClick={fetchData} disabled={isPending}>
        {isPending ? 'Loading...' : 'Fetch Data'}
      </button>
      {data && <p>{data.message}</p>}
    </div>
  );
}

export default App;
```

### 优点

1. **提升响应性**：通过*将耗时的任务标记为过渡*，React 可以在后台处理这些操作，*使应用在等待数据加载时仍然保持响应*。
2. **更好的用户体验**：用户可以在等待数据加载时继续与应用的其他部分进行互动，提升整体用户体验。

### 注意事项

1. **不要滥用**：虽然 `useTransition` 可以提升用户体验，但并不是所有的状态更新都需要标记为过渡。*只有那些耗时较长的操作才适合*使用 `useTransition`。
2. **同步更新**：如果你需要立即看到状态更新的效果（例如，用户输入的即时反馈），不应该使用 `useTransition`。

### 与其他 Hooks 的结合

`useTransition` 经常与其他 Hooks 结合使用，例如 `useState` 和 `useEffect`，以实现更复杂的逻辑。以下是一个更复杂的示例，展示了如何在多个组件之间使用 `useTransition`：

```jsx
import React, { useState, useTransition } from 'react';

function App() {
  const [data, setData] = useState(null);
  const [isPending, startTransition] = useTransition();

  const fetchData = () => {
    startTransition(() => {
      // 模拟异步数据获取
      setTimeout(() => {
        setData({ message: 'Data fetched successfully!' });
      }, 2000);
    });
  };

  return (
    <div>
      <Button onClick={fetchData} isPending={isPending} />
      {data && <DataDisplay data={data} />}
    </div>
  );
}

function Button({ onClick, isPending }) {
  return (
    <button onClick={onClick} disabled={isPending}>
      {isPending ? 'Loading...' : 'Fetch Data'}
    </button>
  );
}

function DataDisplay({ data }) {
  return <p>{data.message}</p>;
}

export default App;
```

在这个示例中，`Button` 组件使用 `isPending` 来控制按钮的状态，而 `DataDisplay` 组件负责显示数据。
