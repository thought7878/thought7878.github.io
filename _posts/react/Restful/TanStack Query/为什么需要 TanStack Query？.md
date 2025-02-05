在 React 开发中，传统的 `useState` + `useEffect` 模式可以实现数据获取和状态管理，但 TanStack Query *提供了更强大、更便捷的解决方案*，以下是详细对比：

### 1. 缓存与重复请求问题

- **传统 `useState` + `useEffect` 模式**
  - 在使用 `useState` 和 `useEffect` 进行数据获取时，*每次组件重新渲染或挂载，`useEffect` 可能会触发重复的数据请求*。例如，当组件因父组件更新而重新渲染时，`useEffect` 内的数据请求会再次执行，*即使数据已经获取过且没有变化，这会增加不必要的网络开销*。

```jsx
import React, { useState, useEffect } from "react";

const MyComponent = () => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch("https://api.example.com/data");
        const result = await response.json();
        setData(result);
        setLoading(false);
      } catch (err) {
        setError(err);
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      {loading ? (
        <p>Loading...</p>
      ) : error ? (
        <p>Error: {error.message}</p>
      ) : (
        <pre>{JSON.stringify(data, null, 2)}</pre>
      )}
    </div>
  );
};

export default MyComponent;
```

- **TanStack Query**
  - TanStack Query **内置了强大的缓存机制**。*当多次发起相同的查询时，它会优先从缓存中获取数据，避免重复的网络请求*。只有当数据过期或手动触发刷新时，才会重新请求数据，大大减少了网络开销，提高了应用性能。

```jsx
import { useQuery } from "@tanstack/react-query";
import React from "react";

const fetchData = async () => {
  const response = await fetch("https://api.example.com/data");
  return response.json();
};

const MyComponent = () => {
  const { data, isLoading, error } = useQuery(["myData"], fetchData);

  return (
    <div>
      {isLoading ? (
        <p>Loading...</p>
      ) : error ? (
        <p>Error: {error.message}</p>
      ) : (
        <pre>{JSON.stringify(data, null, 2)}</pre>
      )}
    </div>
  );
};

export default MyComponent;
```

### 2. 状态管理与错误处理

- **传统 `useState` + `useEffect` 模式**
  - **需要手动管理多个状态**，如加载状态、数据状态、错误状态等，代码会变得复杂且容易出错。同时，**错误处理逻辑**也需要在 `useEffect` 中自行编写，每个数据请求都要重复实现这些逻辑，代码复用性差。
- **TanStack Query**
  - **自动管理查询的各种状态**，如 `isLoading`、`isError`、`isSuccess` 等，开发者可以直接使用这些状态来渲染不同的 UI。而且，**它提供了统一的错误处理机制**，当查询出错时，可以方便地获取错误信息并进行处理，减少了样板代码。

### 3. 数据同步与更新

- **传统 `useState` + `useEffect` 模式**
  - 处理数据更新和同步比较困难。例如，当一个组件更新了数据，其他依赖相同数据的组件需要手动实现数据刷新逻辑，很难保证数据的一致性。
- **TanStack Query**
  - 提供了强大的数据同步和更新功能。当一个查询的数据更新时，所有使用相同查询的组件会自动更新，确保数据在整个应用中的一致性。同时，它还*支持乐观更新*（Optimistic Updates），可以在用户操作后立即更新 UI，然后在服务器响应成功后进行确认，提升用户体验。

### 4. 重试机制与分页处理

- **传统 `useState` + `useEffect` 模式**
  - 实现重试机制和分页处理*需要编写复杂的逻辑*，代码量会显著增加，并且容易出现错误。
- **TanStack Query**
  - *内置了重试机制*，当查询失败时，会根据配置自动进行重试。*对于分页数据*，它提供了简单易用的 API 来管理分页状态和数据加载，开发者可以轻松实现分页功能。
