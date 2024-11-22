# Why

- **与外部状态管理系统集成**
    - **跨框架或库的状态共享**：*在大型应用中，可能会存在多种状态管理方案或者需要与外部的状态存储进行交互*。例如，*当与 Redux、MobX 等状态管理库或者与浏览器的本地存储（localStorage）、IndexedDB 等数据存储方式一起使用时，`useSyncExternalStore`可以作为一个桥梁，让 React 组件能够订阅这些外部数据源的变化，并且以一种高效、一致的方式更新 UI*。这样可以方便地将外部的状态整合到 React 应用的渲染流程中，而不需要大量复杂的手动同步操作。
    - **接入第三方数据提供源**：如果应用需要使用第三方提供的数据服务，如实时数据推送（如 WebSockets）或者外部 API 的更新通知，`useSyncExternalStore`可以用于订阅这些数据变化，确保 React 组件能够及时更新以反映最新的外部信息。例如，一个金融应用可能需要订阅股票价格更新的实时数据源，`useSyncExternalStore`可以帮助组件高效地接收和处理这些价格变化，更新界面上的股票价格显示。
- **性能优化和减少不必要的渲染**
    - **精准的数据获取和更新**：`useSyncExternalStore`允许组件精确地订阅自己需要的数据部分，并且只有当这些数据发生变化时才进行更新。与简单地在每次外部数据源有任何变化就重新渲染组件相比，这种方式可以避免不必要的渲染，提高应用的性能。例如，在一个包含多个数据字段的大型数据对象中，如果组件只关心其中一个字段的变化，`useSyncExternalStore`可以配置为仅在该字段变化时更新组件，而忽略其他无关字段的变化。
    - **防止过度渲染和数据不一致**：通过使用`useSyncExternalStore`，可以确保组件在数据变化时以同步的方式更新，避免了因为异步更新导致的数据不一致和过度渲染的问题。例如，在一个多组件共享数据的场景中，如果没有正确的同步机制，可能会出现一个组件已经更新而另一个组件还在使用旧数据的情况。`useSyncExternalStore`提供了一种同步更新的机制，保证所有订阅组件能够及时、一致地获取最新数据。

# What

`useSyncExternalStore`是 React 提供的一个 Hook，主要用于*从外部数据源读取数据，并在数据变化时同步更新组件*。*它为组件提供了一种订阅外部数据源的机制，使得组件能够高效地获取最新的数据并进行渲染*。

# How

**基本语法**：
```jsx
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
```
- `subscribe`：是一个函数，用于订阅外部数据源的变化。当外部数据源发生变化时，这个函数会被调用，并且它应该返回一个取消订阅的函数。例如，在订阅 Redux Store 的变化时，这个函数可以用于添加一个 Redux Store 的订阅者，返回的取消订阅函数可以用于在组件卸载时移除订阅。
- `getSnapshot`：也是一个函数，用于获取外部数据源的当前快照（snapshot）。这个快照是组件用于渲染的数据部分。例如，在从 Redux Store 获取数据时，这个函数可以返回组件需要的状态部分。
- `getServerSnapshot`：是一个可选的函数，用于在服务器端渲染（SSR）时获取数据快照。如果应用支持 SSR，这个函数可以帮助在服务器环境中获取初始数据，以确保客户端和服务器端的数据一致性。
   
**示例（与简单的外部数据源集成）**：

```jsx
import React, { useSyncExternalStore } from'react';
let externalData = { value: 0 };
const subscribe = (callback) => {
  const handleChange = () => callback();
  window.addEventListener('external - data - change', handleChange);
  return () => window.removeEventListener('external - data - change', handleChange);
};
const getSnapshot = () => externalData;
function ExternalDataComponent() {
  const data = useSyncExternalStore(subscribe, getSnapshot);
  return (
    <div>
      <p>External Data Value: {data.value}</p>
    </div>
  );
}
```

**示例说明**：

在这个示例中，创建了一个简单的外部数据源`externalData`。`subscribe`函数用于订阅外部数据源的变化，通过添加一个`external - data - change`事件监听器来实现。当事件触发时，订阅的回调函数（用于触发组件更新）会被调用。`getSnapshot`函数用于获取外部数据源的当前快照，这里直接返回`externalData`。在`ExternalDataComponent`中，通过`useSyncExternalStore`订阅外部数据源，获取数据并在组件中展示。当外部数据源的`value`属性发生变化并且`external - data - change`事件被触发时，组件会更新并展示新的数据。

