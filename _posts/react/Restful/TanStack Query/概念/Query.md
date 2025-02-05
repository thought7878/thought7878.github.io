在 TanStack Query 里，`query`（查询）是其核心功能之一，用于*从服务器获取数据并对这些数据进行管理*，下面从概念、使用方法、高级特性等维度详细介绍。

### 基本概念

在 TanStack Query 中，*一个 `query` 代表了一次数据获取操作*，*它包含一个唯一的查询键（query key）和一个查询函数（query function）*。**查询键**用于标识这个查询，*方便缓存和数据的管理*；**查询函数**则是实际执行数据获取的异步函数，通常会*发起网络请求*。

### 基本使用方法

#### 1. 安装 TanStack Query

在项目中安装 `@tanstack/react-query` 包：

```bash
pnpm add @tanstack/react-query
```

#### 2. 配置 QueryClient

在应用的入口文件中*创建并配置 `QueryClient` 实例*，然后*使用 `QueryClientProvider` 包裹应用*，这样应用中的组件就可以使用 TanStack Query 了。

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import App from "./App";

// 创建 QueryClient 实例
const queryClient = new QueryClient();

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

#### 3. 使用 `useQuery` 钩子发起查询

在组件中使用 `useQuery` 钩子来发起查询，以下是一个简单的示例：

```jsx
import React from "react";
import { useQuery } from "@tanstack/react-query";

// 定义查询函数，这里使用 fetch API 获取数据
const fetchPosts = async () => {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts");
  return response.json();
};

const PostList = () => {
  // 使用 useQuery 钩子发起查询
  const { isLoading, error, data } = useQuery({
    queryKey: ["posts"], // 查询键，用于标识这个查询
    queryFn: fetchPosts, // 查询函数，实际执行数据获取的函数
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <ul>
      {data.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default PostList;
```

在上述代码中，`useQuery` 钩子返回一个包含多个属性的对象，常用属性如下：

- `isLoading`：表示查询是否正在加载数据，为 `true` 时表示正在加载。
- `error`：如果查询过程中出现错误，该属性会包含错误信息。
- `data`：查询成功后返回的数据。

### 高级特性

#### 1. 缓存和重复查询

TanStack Query 会自动对查询结果进行缓存，当使用相同的查询键发起查询时，如果缓存中已经有数据，会优先使用缓存数据，避免重复的网络请求。

```jsx
// 在不同组件中使用相同的查询键发起查询
const AnotherComponent = () => {
  const { data } = useQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
  });

  return <div>{data && <p>There are {data.length} posts.</p>}</div>;
};
```

#### 2. 刷新和失效查询

可以手动刷新查询结果，或者让查询失效，使其重新获取数据。

```jsx
import { useQueryClient } from "@tanstack/react-query";

const RefreshButton = () => {
  const queryClient = useQueryClient();

  const handleRefresh = () => {
    // 手动刷新查询
    queryClient.invalidateQueries({ queryKey: ["posts"] });
  };

  return <button onClick={handleRefresh}>Refresh Posts</button>;
};
```

#### 3. 查询参数

可以在查询键中传递参数，以区分不同的查询。

```jsx
const fetchPost = async (postId) => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/posts/${postId}`
  );
  return response.json();
};

const SinglePost = ({ postId }) => {
  const { isLoading, error, data } = useQuery({
    queryKey: ["post", postId], // 传递查询参数
    queryFn: () => fetchPost(postId),
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h2>{data.title}</h2>
      <p>{data.body}</p>
    </div>
  );
};
```
