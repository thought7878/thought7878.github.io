`React Server Component（RSC，服务端组件）`是 React 提出的一种新组件模型，其`核心思想`是：**某些 React 组件只在服务端运行，渲染结果以一种特殊的格式（而非 JavaScript 代码）传给客户端**。这项技术旨在解决现代 Web 应用中几个关键的性能和开发体验问题。

---

### 1. 解决了什么问题？

- **减小客户端 JavaScript bundle 体积**  
  Server Components 的代码不会被打包到客户端 bundle 中，因此可以安全使用大型库（比如日期处理、图表、富文本编辑器等），而不用担心拖慢前端加载速度 。

- **简化数据获取与避免瀑布请求（Waterfall）**  
  RSC 允许*在组件内部直接进行数据请求*（例如读数据库、调用 API），并且在服务端并行或串行地组织数据流，从而避免传统客户端组件中常见的“请求嵌套”或“组件树逐层等待数据”的瀑布问题 。

- **提升首屏加载性能**  
  因为*组件在服务端已渲染为结构化数据*（*非 HTML*，但可直接*被客户端 React 恢复为 UI*），客户端只需加载轻量的交互逻辑，大幅加快首屏内容呈现速度 。

- **更好的关注点分离**  
  可将纯 UI 交互逻辑放在 Client Component，而将数据逻辑、权限校验、业务规则等敏感或重型逻辑保留在 Server Component，天然隔离前后端边界 。

---

### 2. React Server Component 是什么？

一句话概括：`Server Component` 是**只在服务端运行、不会被打包到客户端的 React 组件** 。  
它**返回的*不是 HTML*，而是一种 *React 定义的序列化格式（类似 JSON）*，客户端 React *通过该格式重建 UI 树*，同时保留组件语义（如 props、嵌套关系等）**。

与传统的 SSR（服务端渲染）不同，RSC 是**增量、流式、可组合**的，支持*在同一个页面中混合使用 Server Component 和 Client Component*。

---

### 3. 怎么用？（以 Next.js App Router 为例）

在 Next.js 13+ 的 App Router 中，RSC 是默认启用的：

#### 默认组件是 Server Component（无需 “use client”）
```tsx
// app/profile/page.tsx
import db from '@/lib/db';

// 这是一个 Server Component
export default async function ProfilePage({ params }) {
  const user = await db.user.findUnique({ where: { id: params.id } });

  return (
    <div>
      <h1>{user.name}</h1>
      {/* 可以嵌套 Client Component，但需显式声明 */}
      <UserProfileClient user={user} />
    </div>
  );
}
```

#### 需要交互的组件用 “use client” 声明为 Client Component
```tsx
// app/profile/UserProfileClient.tsx
"use client";

import { useState } from 'react';

export default function UserProfileClient({ user }) {
  const [edit, setEdit] = useState(false);
  // 这里可以使用 useState、useEffect、事件处理等
  return <div onClick={() => setEdit(true)}>...</div>;
}
```

#### 注意事项：
- Server Component 不能使用 `useState`、`useEffect`、`useContext`（除了某些服务端可用的 Context）等 hooks。
- props 从 Server Component 传递给 Client Component 必须是可序列化的（不能传函数、类实例等）。
- Server Component 可以直接访问后端资源（数据库、文件系统、环境变量等）。

---

### 总结

React Server Components 通过将组件执行从客户端移到服务端，**在不牺牲 React 组件模型的前提下，显著优化了性能、安全性和开发体验**，特别适合数据密集型、对首屏性能要求高的现代 Web 应用 。目前主流支持 RSC 的框架是 Next.js（App Router 模式）。