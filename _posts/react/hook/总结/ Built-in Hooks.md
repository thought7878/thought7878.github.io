# useReducer

`useReducer` 是 React 中的一个 Hook，**它允许你将状态更新逻辑从事件处理程序中提取到组件外部的单个函数中**。虽然它与 `useState` 类似，但在处理复杂值的更新时，`useReducer` 提供了更结构化的方法。让我们来深入了解一下 `useReducer` 吧。

- **什么是 `useReducer`？**
  
  - `useReducer` 是<u>一个用于状态管理的 React Hook</u>，<u>类似于 `useState`</u>，但**功能更强大**。**它允许你将状态更新逻辑从组件内部的事件处理程序中分离出来，放到一个名为“reducer”的函数中**。
  - `useReducer` 接收一个 `reducer 函数`和`一个初始状态`作为参数，并返回当前状态和一个 `dispatch` 方法，用于触发状态更新和重新渲染。

- **使用场景：**
  
  - 当你的状态具有多个子值（例如包含需要独立更新的键的对象）时，通常可以使用 `useReducer`。
  - 如果你需要更结构化地管理状态，或者需要处理复杂的状态更新逻辑，例如在一个 To-Do 应用中添加、删除和更新用户，那么 `useReducer` 是一个不错的选择。

- **如何使用 `useReducer`：**
  
  1. 首先，编写一个 reducer 函数，该函数接收当前状态和一个操作（action），并返回更新后的状态。
  2. 在组件中调用 `useReducer`，传入 reducer 函数和初始状态。
  3. `useReducer` 返回一个数组，包含当前状态和一个 `dispatch` 方法。
  4. 使用 `dispatch` 方法来触发状态更新，传入操作（action）作为参数。

以下是一个简单的示例：

```jsx
import React, { useReducer } from 'react';

// 定义 reducer 函数
function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'DECREMENT':
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

function MyComponent() {
  // 使用 useReducer
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>Increment</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>Decrement</button>
    </div>
  );
}
```

在上面的示例中，我们使用了一个简单的计数器状态，并通过 `dispatch` 方法触发状态更新。`reducer` 函数根据操作类型来更新状态。

[总之，`useReducer` 是一个强大且灵活的 Hook，适用于处理复杂的状态逻辑和结构化的状态管理。](https://react.dev/reference/react/useReducer)[1](https://react.dev/reference/react/useReducer)[2](https://qiita.com/seira/items/2fbad56e84bda885c84c)[3](https://www.react.express/hooks/usereducer)[4](https://www.w3schools.com/react/react_usereducer.asp)[5](https://www.freecodecamp.org/news/usereducer-hook-react/)



# useTransition

在 **React** 中，`useTransition` 是一个重要的 **Hook**。让我来解释一下：

1. **什么是 `useTransition`？**
   
   - `useTransition` 是一个帮助你在不阻塞用户界面（UI）的情况下更新状态的 **React Hook**。
   - 它允许你将状态更新标记为非阻塞的 **transition**。通过这种方式，你可以在不影响用户体验的情况下更新应用程序的状态。

2. **为什么使用 `useTransition`？**
   
   - 使用 `useTransition` 的目的是使用户界面的更新在慢速设备上仍保持响应性。
   - 通过 `transition`，UI 仍将在重新渲染过程中保持响应性。例如，用户点击一个选项卡，但改变了主意并点击另一个选项卡，他们可以在不等待第一个重新渲染完成的情况下完成操作。
   - 使用 `useTransition` 与常规状态更新的区别在于，它允许你在 **transition** 中更新状态，而不会阻塞用户界面。

3. **如何使用 `useTransition`？**
   
   - 首先，在组件的顶层调用 `useTransition` 以将状态更新标记为非阻塞的 **transition**。
   
   - 然后，你可以使用 `startTransition` 函数将状态更新标记为 **transition**。例如：
     
     ```jsx
     import { useState, useTransition } from 'react';
     
     function TabContainer() {
      const [isPending, startTransition] = useTransition();
      const [tab, setTab] = useState('about');
     
      function selectTab(nextTab) {
        startTransition(() => {
          setTab(nextTab);
        });
      }
     
      // 其他组件渲染逻辑...
     }
     ```
   
   - 这样，你可以在 `selectTab` 函数中更新 `tab` 状态，而不会阻塞用户界面。

总之，`useTransition` 是一个有助于提高应用程序性能和用户体验的强大工具。通过将状态更新标记为非阻塞的 **transition**，你可以在用户界面仍保持响应性的同时进行状态更新。🚀


