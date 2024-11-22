# Why
- **提升调试体验**
    - **自定义 Hook 的内部状态可视化**：在开发复杂的 React 应用时，经常会创建自定义 Hook 来封装和复用逻辑。这些自定义 Hook 可能包含一些内部状态，对于调试者来说，很难直接了解这些内部状态的情况。例如，一个自定义 Hook 用于获取和管理用户的权限信息，它内部可能有多个状态来表示不同的权限级别和状态。通过使用`useDebugValue`，可以在 React 开发者工具中显示这些权限相关的信息，使得调试者能够快速查看自定义 Hook 的关键状态，而不需要深入到自定义 Hook 的实现代码中去查找。
    - **方便追踪 Hook 的行为和数据流向**：在大型项目中，多个组件可能会使用同一个自定义 Hook，而且这些自定义 Hook 可能会根据不同的条件返回不同的值或者执行不同的操作。`useDebugValue`提供了一种简单的方式来标记这些差异，帮助开发者更好地理解数据是如何在组件和 Hook 之间流动的。例如，一个用于数据过滤的自定义 Hook，根据不同的过滤条件会返回不同的过滤后的数据集合，使用`useDebugValue`可以在开发者工具中显示当前的过滤条件，有助于追踪数据的来源和处理过程。

# What
`useDebugValue`是 React 提供的一个 Hook，用于*在 React 开发者工具中为自定义 Hook 添加一个可显示的标签*。*这个标签能够帮助开发者在调试过程中更清楚地了解自定义 Hook 的状态或值*。

# How
**基本语法**：`useDebugValue(value, formatDebugValue)`
- `value`是要在开发者工具中显示的值。这个值可以是任何类型，如字符串、数字、对象等，它代表了自定义 Hook 的某个关键状态或者计算结果。
- `formatDebugValue`是一个可选的函数，用于格式化`value`。如果`value`是一个复杂的对象，通过这个函数可以将其转换为更易于阅读和理解的格式。

**示例**：
  
```jsx
import React, { useState, useDebugValue } from'react';
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);
  // 模拟获取朋友状态的异步操作
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status);
    }
    // 假设这是一个获取朋友在线状态的API调用
    setTimeout(() => {
      const status = Math.random() > 0.5;
      handleStatusChange(status);
    }, 1000);
  }, [friendID]);
  // 
  useDebugValue(isOnline, (status) =>
    status === null? 'Loading...' : status? 'Online' : 'Offline'
  );
  return isOnline;
}
function FriendListItem({ friend }) {
  const isOnline = useFriendStatus(friend.id);
  return (
    <li style={{ color: isOnline? 'green' : 'black' }}>
      {friend.name}
    </li>
  );
}
```

- **示例说明**：
    - 在这个示例中，`useFriendStatus`是一个自定义 Hook，用于获取朋友的在线状态。它内部有一个`isOnline`状态，初始值为`null`，通过`useEffect`模拟了一个异步获取状态的操作。在`useFriendStatus`中使用了`useDebugValue`，它将`isOnline`状态作为要显示的值。`formatDebugValue`函数根据`isOnline`的值返回一个更易于理解的字符串，如`Loading...`（当`isOnline`为`null`时，表示正在获取状态）、`Online`或者`Offline`。在 React 开发者工具中，当检查使用了`useFriendStatus`的组件（如`FriendListItem`）时，可以看到这个自定义 Hook 的状态显示，这有助于开发者快速了解朋友状态的获取情况。
