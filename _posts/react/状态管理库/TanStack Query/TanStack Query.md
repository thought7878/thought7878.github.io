
`TanStack Query`（前身为大名鼎鼎的 **React Query**）是目前前端生态中最强大、最受欢迎的**异步状态管理（服务器状态管理）** 工具库。

随着其发展，它已经从一个仅支持 React 的库，扩展为一个跨框架的生态系统（TanStack），现在官方支持 *React、Vue、Solid、Svelte 和 Angular*。

以下是对 TanStack Query 的详细深度解析（基于目前主流的 **v5** 版本）。

---

## 一、 核心理念：区分“客户端状态”与“服务器状态”

*在过去，开发者习惯将所有的状态都塞进 Redux、Vuex 或 Zustand 中*。但 *TanStack Query 的核心理念是：**状态应该被分类管理***。

1. **客户端状态（Client State）**：
   - **特点**：同步的、由前端完全控制的、不需要持久化或网络同步的。
   - **例子**：侧边栏是否展开、当前选中的 Tab、UI 主题色。
   - **适用工具**：Zustand, Redux, Context API。
2. **服务器状态（Server State）**：
   - **特点**：异步的、**不受前端控制**（后端可能随时被其他用户修改）、具有“过期”概念、需要缓存和同步的。
   - **例子**：用户列表、商品详情、文章评论。
   - **适用工具**：**TanStack Query**。

TanStack Query 专门用于解决服务器状态带来的各种痛点：缓存、去重、后台更新、加载状态、错误重试等，从而让你**彻底告别在 `useEffect` 中手动写 `useState(isLoading)` 和 `useState(error)` 的样板代码**。

---

## 二、 核心概念

要掌握 TanStack Query，必须理解以下四个核心概念：

### 1. Query（查询）
*一个 Query 是对一个异步数据源的声明*。它通常绑定到一个**查询键（Query Key）** 和一个**查询函数（Query Function）**。只要组件挂载，TanStack Query 就会自动管理数据的获取、缓存和更新。

### 2. Mutation（变更）
Mutation 用于**创建、更新或删除**服务器数据（即 POST/PUT/DELETE 请求）。与 Query 自动拉取不同，Mutation 是手动触发的（例如点击“提交”按钮）。

### 3. Query Key（查询键）
Query Key 是一个数组（如 `['todos']` 或 `['todo', 5]`），它是 TanStack Query 缓存系统的**唯一标识符**。
- 所有的缓存、去重、预取、失效（Invalidation）都严重依赖 Query Key。
- 当 Query Key 发生变化时，Query 会自动重新执行（例如从 `['todo', 1]` 切换到 `['todo', 2]`）。

### 4. Query Invalidation（查询失效）
当你执行了一个 Mutation（比如新增了一条 Todo），本地的缓存就“脏”了。*此时你需要告诉 TanStack Query 让相关的 Query Key 失效*。失效后，TanStack Query 会*自动在后台重新拉取最新数据并无缝更新 UI*。

---

## 三、 为什么需要 TanStack Query？（核心优势）
参考：[[_posts/react/状态管理库/TanStack Query/为什么需要 TanStack Query？|为什么需要 TanStack Query？]]

*如果你不用它，你通常需要手写以下逻辑*，而 TanStack Query 全部**开箱即用**：

* **请求去重**：如果同一时间有 5 个组件都需要 `['user']` 数据，它**只会发送 1 次**网络请求，然后共享结果。
* **Stale-While-Revalidate (SWR)**：优先展示缓存数据（让用户感觉秒开），同时在后台静默拉取最新数据，拉取完成后自动更新 UI。
* **内存管理与垃圾回收**：未使用的数据会在指定时间后自动从内存中清除，防止内存泄漏。
* **窗口聚焦刷新**：当用户切出浏览器标签页，再切回来时，自动在后台刷新旧数据。
* **自动重试**：网络请求失败时，支持指数退避算法自动重试。
* **离线支持**：结合 Service Worker，可以轻松实现离线读取和请求排队。

---

## 四、 基础使用示例 (以 React + v5 为例)

### 1. 安装与配置
```bash
npm i @tanstack/react-query
```

在应用根节点配置 `QueryClient` 和 `Provider`：
```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

// 创建 client，可以在这里 设置全局默认配置
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5分钟内视为新鲜数据，不触发后台重新获取
      gcTime: 1000 * 60 * 30,   // 原 cacheTime，30分钟未使用则垃圾回收
    },
  },
});

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <TodoList />
    </QueryClientProvider>
  );
}
```

### 2. 使用 `useQuery` 获取数据
```tsx
import { useQuery } from '@tanstack/react-query';

const fetchTodos = async () => {
  const res = await fetch('/api/todos');
  return res.json();
};

function TodoList() {
  const { data, isLoading, error, isError } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,
  });

  if (isLoading) return <span>Loading...</span>;
  if (isError) return <span>Error: {error.message}</span>;

  return (
    <ul>
      {data.map(todo => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

### 3. 使用 `useMutation` 与 查询失效 (Invalidation)
```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function AddTodo() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newTodo) => fetch('/api/todos', { method: 'POST', body: JSON.stringify(newTodo) }),
    onSuccess: () => {
      // 数据新增成功后，让 ['todos'] 缓存失效，触发自动重新拉取
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });

  return (
    <button onClick={() => mutation.mutate({ title: 'Learn TanStack Query' })}>
      Add Todo
    </button>
  );
}
```

---

## 五、 两个极易混淆的概念：`staleTime` vs `gcTime`

这是初学者最容易踩坑的地方，必须严格区分：

1. **`staleTime` (新鲜度时间)**：
   - 决定数据**何时算作“过期”**。
   - *在 `staleTime` 内，组件挂载或窗口聚焦**不会**触发后台重新请求（直接用缓存）*。
   - 默认值为 `0`（即数据一拿到手就立刻算作 stale，但依然可以使用缓存，并在后台静默更新）。
2. **`gcTime` (垃圾回收时间，v5 之前叫 `cacheTime`)**：
   - 决定数据**在内存中保留多久**。
   - 当没有任何组件在使用这个 Query 时，它会在后台保留 `gcTime` 的时间。超时后，数据会被彻底销毁。
   - 默认值为 `5 分钟`。

---

## 六、 高级特性

TanStack Query 的强大之处在于*它提供了大量应对复杂业务场景的 API：*

### 1. 依赖查询 (Dependent Queries)
当 Query B 的执行依赖于 Query A 的结果时，使用 `enabled` 选项：
```tsx
// 先获取 user
const { data: user } = useQuery({ queryKey: ['user'], queryFn: getUser });
// 只有当 user 存在时，才去获取 projects
const { data: projects } = useQuery({
  queryKey: ['projects', user?.id],
  queryFn: getProjectsByUserId,
  enabled: !!user?.id, // 核心：条件开关
});
```

### 2. 预取 (Prefetching)
在用户需要数据之前，提前将其拉取并放入缓存（例如鼠标 Hover 到链接上时）：
```tsx
const queryClient = useQueryClient();

const prefetch = () => {
  queryClient.prefetchQuery({
    queryKey: ['article', id],
    queryFn: fetchArticle,
  });
};

<a href={`/article/${id}`} onMouseEnter={prefetch}>阅读文章</a>
```

### 3. 乐观更新 (Optimistic Updates)
在发送网络请求前，先“假装”成功并立即更新 UI。如果请求失败，再回滚数据。这能带来**极致的用户体验**。
```tsx
useMutation({
  mutationFn: updateTodo,
  onMutate: async (newTodo) => {
    // 1. 取消可能正在进行的后台获取
    await queryClient.cancelQueries({ queryKey: ['todos'] });
    // 2. 保存旧数据快照
    const previousTodos = queryClient.getQueryData(['todos']);
    // 3. 乐观地更新缓存
    queryClient.setQueryData(['todos'], old => [...old, newTodo]);
    return { previousTodos }; // 传递给 onError
  },
  onError: (err, newTodo, context) => {
    // 失败时回滚
    queryClient.setQueryData(['todos'], context.previousTodos);
  },
  onSettled: () => {
    // 无论成功失败，最后都重新拉取一次确保与服务器同步
    queryClient.invalidateQueries({ queryKey: ['todos'] });
  },
});
```

### 4. 分页与无限滚动 (Infinite Query)
提供了专门的 `useInfiniteQuery` hook，完美处理 `hasNextPage` 和 `fetchNextPage` 的逻辑，是写瀑布流和无限滚动列表的神器。

---

## 七、 生态系统与 DevTools

### 1. TanStack Query Devtools
这是开发者的“透视眼镜”。在生产环境默认不打包，在开发环境下，它可以悬浮在页面角落，让你实时看到：
- 当前所有的 Query Keys 及其状态（`fresh` 新鲜, `stale` 过期, `fetching` 请求中, `inactive` 未激活）。
- 缓存的具体 JSON 数据结构。
- 手动触发重试、失效、清空缓存。

### 2. 框架无关的传输层
TanStack Query **不绑定任何 HTTP 库**。你的 `queryFn` 里可以使用 `fetch`、`axios`、`GraphQL` (配合 GraphQL-Request)、甚至 `WebSocket` 和 `Firebase`。只要能返回一个 Promise，它就能接管。

### 3. 跨框架支持
如果你以后转战其他框架，你的 Query 逻辑几乎可以无缝迁移：
- `@tanstack/vue-query`
- `@tanstack/solid-query`
- `@tanstack/svelte-query`

---

## 八、 总结：什么时候该用 TanStack Query？

**只要你的应用需要与后端 API 交互，并且有以下任一需求，就强烈推荐使用：**
1. 厌倦了在 Redux/Context 中手动写 `isLoading`, `isError` 逻辑。
2. 希望应用拥有类似原生 App 的“秒开”体验（利用缓存）。
3. 页面上有多个组件需要请求相同的接口，希望避免重复请求。
4. 需要做分页、无限滚动、轮询（Polling）、乐观更新等复杂交互。

**不适合的场景：**
- 纯客户端状态（如表单校验状态、侧边栏折叠状态），这些依然推荐使用 Zustand、Redux 或 React Context。
- 简单的、一次性的、不需要缓存的表单提交（直接用普通的 `async/await` 即可，杀鸡焉用牛刀）。


---

在前面的交流中，我们已经探讨了 TanStack Query（原 React Query）的基础概念和核心优势。既然您再次提出详细介绍，这次我们将跳出基础 API 的范畴，直接切入 **TanStack Query (基于目前主流的 v5 版本) 的进阶实战、架构设计模式以及在复杂企业级项目中的最佳实践**。

我们将从“思维范式转换”、“v5 核心进阶 API”、“高级实战模式”、“架构级集成”四个维度进行深度剖析。

---

## 一、 核心哲学再认知：从 "Fetching" 到 "Syncing"

很多开发者把 TanStack Query 当作一个“更好用的 `fetch` 封装库”，这是对它最大的误解。

*   **传统思维 (Fetching)**：组件挂载 -> 发起请求 -> 等待 Loading -> 渲染数据。关注的是 **“如何把数据拿回来”**。
*   **TanStack Query 思维 (Syncing)**：你的应用状态是后端数据库在客户端的**一个“缓存快照”**。TanStack Query 的工作是**维持这个快照与后端数据库的 eventual consistency（最终一致性）**。

因此，它不是在“发请求”，而是在“**订阅并同步某个 Query Key 对应的数据源**”。理解了这一点，你才能真正用好它的缓存、后台更新（Background Refetch）和失效（Invalidation）机制。

---

## 二、 v5 核心 API 进阶解析

在 v5 版本中，API 经历了重大重构，全面拥抱了 React 的最新特性（如 Suspense）。

### 1. 拥抱 Suspense：`useSuspenseQuery`
在 v4 及以前，我们需要在组件里写大量的 `if (isLoading) return <Spinner />`。v5 引入了 `useSuspenseQuery`，它将 Loading 和 Error 状态直接交给了 React 的 `<Suspense>` 和 `ErrorBoundary` 处理，**让组件代码变得极其纯粹**。

```tsx
import { useSuspenseQuery } from '@tanstack/react-query';

// 组件内部再也没有 isLoading 和 isError！
function UserProfile({ userId }) {
  const { data: user } = useSuspenseQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
  });

  // 直接渲染数据，因为能执行到这里，data 必定存在
  return <h1>{user.name}</h1>; 
}

// 父组件使用 Suspense 包裹
function App() {
  return (
    <ErrorBoundary fallback={<div>出错了</div>}>
      <Suspense fallback={<Skeleton />}>
        <UserProfile userId={1} />
      </Suspense>
    </ErrorBoundary>
  );
}
```

### 2. 依赖查询的优雅解法：`skipToken`
当一个查询依赖另一个查询的结果时（例如：先获取用户 ID，再获取用户的文章），过去我们用 `enabled: !!userId`。v5 引入了 `skipToken`，提供了更好的类型推导和语义。

```tsx
import { useQuery, skipToken } from '@tanstack/react-query';

const { data: user } = useQuery({ queryKey: ['user'], queryFn: getUser });

const { data: posts } = useQuery({
  queryKey: ['posts', user?.id],
  // 如果 user.id 不存在，传入 skipToken，TS 会推断 data 可能为 undefined
  queryFn: user?.id ? () => fetchPosts(user.id) : skipToken, 
});
```

### 3. 无限滚动与分页：`useInfiniteQuery`
处理“加载更多”或“下拉刷新”的终极方案。它会自动管理 `pages` 数组和 `pageParams`。

```tsx
const {
  data,
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
} = useInfiniteQuery({
  queryKey: ['projects'],
  queryFn: fetchProjects, // fetchProjects 接收 { pageParam } 参数
  initialPageParam: 0,
  getNextPageParam: (lastPage, allPages, lastPageParam) => {
    // 根据最后一页的数据决定下一页的 cursor，如果没有则返回 undefined
    return lastPage.nextCursor ?? undefined; 
  },
});

// 渲染时扁平化 pages 数组
const allProjects = data?.pages.flatMap(page => page.items) ?? [];
```

---

## 三、 高级实战模式 (Advanced Patterns)

### 1. 极致体验：乐观更新 (Optimistic Updates)
在点赞、发消息等场景，我们不等服务器返回，**直接修改 UI 缓存**，如果失败再**回滚**。

```tsx
const queryClient = useQueryClient();

const updateTodoMutation = useMutation({
  mutationFn: updateTodo,
  // 1. 突变前：快照旧数据，乐观更新缓存
  onMutate: async (newTodo) => {
    await queryClient.cancelQueries({ queryKey: ['todos'] }); // 取消正在进行的拉取
    const previousTodos = queryClient.getQueryData(['todos']); // 留存快照
    
    queryClient.setQueryData(['todos'], (old) =>
      old.map((t) => (t.id === newTodo.id ? { ...t, ...newTodo } : t))
    );
    return { previousTodos }; // 传递给 onError
  },
  // 2. 失败时：利用快照回滚
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(['todos'], context.previousTodos);
    toast.error('更新失败，已恢复原状');
  },
  // 3. 最终：无论成败，重新拉取保证绝对一致
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] });
  },
});
```

### 2. 请求预取 (Prefetching)
为了消灭 Loading 状态，我们可以在用户**还没进入页面**时，就把数据塞进缓存。

*   **路由级预取**：结合 React Router 的 `loader`。
*   **交互预取 (Hover Prefetch)**：当鼠标悬停在链接上时触发。

```tsx
import { useQueryClient } from '@tanstack/react-query';

function PostLink({ postId }) {
  const queryClient = useQueryClient();
  
  const prefetch = () => {
    queryClient.prefetchQuery({
      queryKey: ['post', postId],
      queryFn: () => fetchPost(postId),
      staleTime: 10000, // 10秒内进入页面，直接用缓存，不发请求
    });
  };

  return (
    <Link to={`/post/${postId}`} onMouseEnter={prefetch}>
      查看文章
    </Link>
  );
}
```

### 3. 架构级模式：Query Key 工厂 (Query Key Factory)
在大型项目中，硬编码 `['todos', id]` 会导致缓存失效时难以维护。推荐采用类似 tRPC 的**对象工厂模式**来统一管理 Query Key。

```typescript
// queryKeys.ts
export const todoKeys = {
  all: ['todos'] as const,
  lists: () => [...todoKeys.all, 'list'] as const,
  list: (filters: Filter) => [...todoKeys.lists(), { filters }] as const,
  details: () => [...todoKeys.all, 'detail'] as const,
  detail: (id: number) => [...todoKeys.details(), id] as const,
};

// 使用
useQuery({ queryKey: todoKeys.detail(5), ... });

// 失效时，极其优雅且安全
queryClient.invalidateQueries({ queryKey: todoKeys.lists() }); // 失效所有列表
queryClient.invalidateQueries({ queryKey: todoKeys.all }); // 失效所有 todo 相关
```

---

## 四、 架构级集成 (SSR / Next.js / Remix)

在 SSR（服务端渲染）框架中，TanStack Query 扮演着**数据水合 (Hydration)** 的核心角色，避免客户端和服务端重复请求（Waterfall）。

**Next.js App Router 集成示例：**

```tsx
// app/posts/page.tsx (Server Component)
import { dehydrate, HydrationBoundary, QueryClient } from '@tanstack/react-query';
import { getPosts } from './api';
import PostsClient from './PostsClient';

export default async function PostsPage() {
  const queryClient = new QueryClient();

  // 在服务端预取数据
  await queryClient.prefetchQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  });

  return (
    // 将服务端缓存的状态“脱水”传递给客户端
    <HydrationBoundary state={dehydrate(queryClient)}>
      <PostsClient />
    </HydrationBoundary>
  );
}
```
在客户端组件 `PostsClient` 中，当调用 `useQuery({ queryKey: ['posts'] })` 时，它会**瞬间命中服务端传过来的缓存**，实现真正的“秒开”且无 Loading 闪烁。

---

## 五、 性能优化与避坑指南

### 1. 避免不必要的重渲染 (`select` 与 `structuralSharing`)
如果一个巨大的对象返回给组件，但组件只用到其中一个字段，对象的重新生成会导致组件无意义重渲染。
*   **解法**：使用 `select` 选项提取所需片段。
```tsx
const { data: userName } = useQuery({
  queryKey: ['user', id],
  queryFn: fetchUser,
  select: (user) => user.profile.name, // 只有 name 变化时才触发组件渲染
});
```

### 2. 内存泄漏与垃圾回收 (`gcTime`)
*   `gcTime` (原 `cacheTime`)：组件卸载后，数据在内存中保留的时间（默认 5 分钟）。
*   **避坑**：对于占用内存极大的数据（如长列表、大型图表配置），应在 `useQuery` 中手动将 `gcTime` 设置为 `0` 或较短时间，让其在组件卸载后立即被垃圾回收，防止 OOM（内存溢出）。

### 3. 请求竞态与自动取消
TanStack Query 内部**自动集成了 `AbortController`**。
如果 `queryKey` 发生变化（例如搜索框输入关键字，从 `['search', 'a']` 变成 `['search', 'ab']`），它会自动 `abort` 掉前一个慢请求，只采用最新请求的结果。开发者无需手动处理竞态条件。

### 4. 慎用 `refetchInterval` (轮询)
虽然轮询可以实现类似 WebSocket 的实时效果，但会消耗大量网络资源。
*   **优化**：结合 `useWindowFocus`，当用户切出浏览器标签页时，应自动停止轮询（TanStack Query 默认在窗口失去焦点时会暂停请求）。

---

## 六、 总结：何时使用，何时不用？

**✅ 必须使用的场景：**
1.  所有来自后端的 API 数据（CRUD）。
2.  需要缓存、去重、后台静默更新的数据。
3.  分页、无限滚动列表。
4.  复杂的表单提交与乐观更新。

**❌ 不要使用的场景：**
1.  **纯客户端 UI 状态**：如弹窗开关、侧边栏折叠（请使用 Zustand / Jotai / useState）。
2.  **全局同步的本地状态**：如多标签页状态同步（请使用 BroadcastChannel 或 Zustand persist）。
3.  **URL 可以承载的状态**：如筛选条件、分页页码（请直接使用 React Router / URL SearchParams，保证可分享和刷新不丢失）。

TanStack Query 并非只是一个工具库，它代表了现代前端 **“以数据为中心 (Data-Centric)”** 的架构演进。掌握它，意味着你彻底告别了手写 `useEffect` 获取数据的刀耕火种时代，迈入了企业级前端工程化的新阶段。