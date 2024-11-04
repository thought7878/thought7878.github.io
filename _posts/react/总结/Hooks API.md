
## useTransition

### Why
标记某些状态更新为“过渡”状态，*告诉React当前任务比较耗时*（异步任务或耗时的大量计算的同步任务），从而允许 React 在后台*优先处理其他任务，使应用在等待数据加载时仍然保持响应*。提升应用的响应性和流畅性，优化用户体验。

### 基本用法

`useTransition` 返回一个包含两个元素的数组：一个布尔值 `isPending` 和一个 `startTransition` 函数。`isPending` 用于指示*当前是否有正在进行的过渡*，而 `startTransition` 用于启动一个过渡任务。

**紧急任务 & 过渡任务**
**紧急任务**，输入框、按钮等任务需要在视图上立即做出响应，让用户立马能够看到效果的任务。
但有时，更新任务不一定那么紧急，或者说需要去请求数据，导致新的状态不能够立马更新，需要一个 `loading...` 的状态，这类任务称为**过渡任务**。

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

## useDeferredValue
### Why
在一些场景中，渲染比较消耗性能。比如输入框，当用户连续输入的时候会不断地调取后端服务，其实很多的片段信息是无用的，这样会浪费服务资源， React 的响应式更新和 JS 单线程的特性*也会导致其他渲染任务的卡顿*。而 useDeferredValue 就是用来解决这个问题的。

通过 `useDeferredValue`，*可以延迟某些状态更新的渲染*，*从而允许 React 在后台优先处理其他任务*，提升应用的响应性和流畅性。

### 基本用法

#### 语法
```javascript
const deferredValue = useDeferredValue(value);
```

1. **`useDeferredValue`**：*接受一个状态值作为参数，返回一个延迟版本的该状态值*。
2. **延迟渲染**：当状态值发生变化时，React 会尝试*先渲染其他内容，然后再渲染使用延迟状态值的部分*。
3. **提升响应性**：通过延迟某些状态更新的渲染，可以提升应用的响应性和流畅性。
#### 示例

假设你有一个应用，用户在搜索框中输入内容时会触发一个耗时的操作（如从服务器获取数据），这个操作会导致界面的重新渲染。为了提升用户体验，你可以使用 `useDeferredValue` 来*延迟搜索结果的渲染，让用户在等待数据加载时仍然可以与应用的其他部分进行互动*。

```jsx
import React, { useState, useDeferredValue } from 'react';

function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  const [results, setResults] = useState([]);

  const handleSearch = (e) => {
    setQuery(e.target.value);
    // 模拟异步数据获取
    setTimeout(() => {
      setResults([
        { id: 1, title: `${e.target.value} 1` },
        { id: 2, title: `${e.target.value} 2` },
        { id: 3, title: `${e.target.value} 3` }
      ]);
    }, 1000);
  };

  return (
    <div>
      <input type="text" value={query} onChange={handleSearch} placeholder="Search..." />
      <hr />
      <div>
        {results.filter(item => item.title.includes(deferredQuery)).map(item => (
          <div key={item.id}>{item.title}</div>
        ))}
      </div>
    </div>
  );
}

export default App;
```


### 优点

1. **提升响应性**：通过延迟某些状态更新的渲染，可以让应用在处理复杂操作时*仍然保持响应*。
2. **更好的用户体验**：用户在等待数据加载时*仍然可以与应用的其他部分进行互动*，提升整体用户体验。

### 注意事项

1. **不要滥用**：虽然 `useDeferredValue` 可以提升用户体验，但并不是所有的状态更新都需要延迟。*只有那些耗时较长的操作才适合使用*`useDeferredValue`。
2. **同步更新**：如果你需要立即看到状态更新的效果（例如，用户输入的即时反馈），不应该使用 `useDeferredValue`。

### 与其他 Hooks 的结合

`useDeferredValue` 经常与其他 Hooks 结合使用，例如 `useState` 和 `useEffect`，以实现更复杂的逻辑。以下是一个更复杂的示例，展示了如何在多个组件之间使用 `useDeferredValue`：

```jsx
import React, { useState, useDeferredValue, useEffect } from 'react';

function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  const [results, setResults] = useState([]);

  useEffect(() => {
    if (query) {
      // 模拟异步数据获取
      setTimeout(() => {
        setResults([
          { id: 1, title: `${query} 1` },
          { id: 2, title: `${query} 2` },
          { id: 3, title: `${query} 3` }
        ]);
      }, 1000);
    } else {
      setResults([]);
    }
  }, [query]);

  return (
    <div>
      <input type="text" value={query} onChange={e => setQuery(e.target.value)} placeholder="Search..." />
      <hr />
      <div>
        {results.filter(item => item.title.includes(deferredQuery)).map(item => (
          <div key={item.id}>{item.title}</div>
        ))}
      </div>
    </div>
  );
}

export default App;
```

在这个示例中，`useDeferredValue` 用于延迟搜索结果的渲染，而 `useEffect` 用于处理异步数据获取。

## useDeferredValue 和 useTransition 的异同点

`useDeferredValue` 和 `useTransition` 都是 React 提供的用于优化用户体验的 Hooks，它们在某些方面确实有相似之处，但也有一些重要的不同点。下面详细解释它们的异同点：

### 相似点

1. **目的**：两者的主要目的是优化用户体验，特别是在处理复杂状态更新和界面渲染时，*通过延迟某些操作来提升应用的响应性和流畅性*。
2. **延迟渲染**：两者都允许 React 在处理某些状态更新时，*优先处理其他任务，从而提升应用的整体性能*。

### 不同点

#### 1. 用途和场景

- **`useDeferredValue`**：
  - **用途**：用于延迟某些状态值的渲染，但不阻止其他状态值的更新。
  - **场景**：适用于需要*延迟某些非关键状态值的渲染*，以便优先处理其他任务。例如，延迟搜索结果的显示，让用户在输入时仍能感受到应用的响应性。

- **`useTransition`**：
  - **用途**：用于标记某些状态更新为“过渡”状态，允许 React 在后台处理这些更新，优先处理其他任务。
  - **场景**：适用于需要标记某些耗时较长的操作为过渡，以便在这些操作进行时，用户仍能与应用的其他部分进行互动。例如，延迟数据加载的显示，让用户在等待数据时仍能滚动页面或其他操作。

#### 2. API 设计

- **`useDeferredValue`**：
  - **API**：`const deferredValue = useDeferredValue(value);`
  - **返回值**：返回一个延迟版本的值。
  - **使用方式**：直接使用返回的延迟值进行渲染。

- **`useTransition`**：
  - **API**：`const [isPending, startTransition] = useTransition();`
  - **返回值**：返回一个布尔值 `isPending` 和一个函数 `startTransition`。
  - **使用方式**：使用 `startTransition` 函数来启动一个过渡，`isPending` 用于指示当前是否有正在进行的过渡。

#### 3. 渲染行为

- **`useDeferredValue`**：
  - **渲染行为**：延迟状态值的渲染，但不阻止其他状态值的更新。
  - **示例**：当 `query` 变化时，`deferredQuery` 的变化会被延迟，直到其他任务处理完毕。

- **`useTransition`**：
  - **渲染行为**：标记某些状态更新为过渡，允许 React 在后台处理这些更新，优先处理其他任务。
  - **示例**：当 `fetchData` 被调用时，数据加载的更新会被标记为过渡，用户在等待数据时仍能与应用的其他部分进行互动。

### 示例对比

#### `useDeferredValue` 示例

```jsx
import React, { useState, useDeferredValue } from 'react';

function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  const [results, setResults] = useState([]);

  const handleSearch = (e) => {
    setQuery(e.target.value);
    // 模拟异步数据获取
    setTimeout(() => {
      setResults([
        { id: 1, title: `${e.target.value} 1` },
        { id: 2, title: `${e.target.value} 2` },
        { id: 3, title: `${e.target.value} 3` }
      ]);
    }, 1000);
  };

  return (
    <div>
      <input type="text" value={query} onChange={handleSearch} placeholder="Search..." />
      <hr />
      <div>
        {results.filter(item => item.title.includes(deferredQuery)).map(item => (
          <div key={item.id}>{item.title}</div>
        ))}
      </div>
    </div>
  );
}

export default App;
```

#### `useTransition` 示例

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

### 总结

- **`useDeferredValue`**：用于延迟某些状态值的渲染，适用于需要延迟非关键状态值的场景。
- **`useTransition`**：用于标记某些状态更新为过渡，适用于需要标记耗时较长的操作为过渡的场景。
