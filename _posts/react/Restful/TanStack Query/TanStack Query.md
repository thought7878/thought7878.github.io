### DS的思考
嗯，用户让我总结React Query的知识点。之前他问过React的核心概念、函数组件、状态提升、Context等等，看起来他在系统地学习React生态。现在他想了解React Query，可能是想处理数据获取和状态管理的问题。

首先，我需要确定React Query的**核心功能**。React Query*主要用于数据获取、缓存、同步和更新，适用于**管理服务器状态***。用户之前学过Context和Redux，可能对客户端状态管理有基础，但服务器状态管理可能需要不同的工具。

接下来要考虑用户可能的背景。他可能在使用REST或GraphQL API，需要*高效地管理请求，处理加载状态、错误和缓存*。**React Query能简化这些流程**，用户可能希望减少手动处理副作用的工作量，比如用useEffect和useState来管理数据获取。

用户可能的需求包括：如何发起请求，缓存策略，数据更新，自动重新获取，分页和无限加载，与全局状态的结合，以及错误处理。需要覆盖这些方面，但保持简洁，不要过多细节。

另外，用户可能对React Query的优势和适用场景感兴趣，比如与传统的状态管理库相比，React Query如何提升开发效率和应用性能。还要提到与Redux或Context的区别，帮助用户理解何时使用React Query。

需要确保总结结构清晰，分点列出核心概念、常用Hook、查询配置、优化策略、与状态管理的对比等。可能还需要注意事项，比如不适合客户端状态，依赖QueryClientProvider的设置。

最后，检查是否有遗漏的重要功能，比如预取、离线支持、开发者工具，以及如何与TypeScript结合。确保覆盖用户可能关心的所有主要方面，同时避免冗长，保持每个知识点简明扼要。

### React Query 核心知识点总结

---

#### **1. 核心概念**
- **用途**：管理 **服务器状态**（异步数据），提供数据获取、缓存、同步、更新等能力。
- **核心思想**：将服务端数据视为「临时快照」，通过声明式 API 自动处理缓存、重试、过期等逻辑。
- **适用场景**：REST/GraphQL API 交互、分页/无限加载、后台数据同步。
- **核心价值**：替代传统的 `useEffect` + 手动状态管理，标准化数据流逻辑。
- **多框架支持**：支持 React、Vue、Solid 等框架（原 React Query 更名为 **TanStack Query**，核心功能一致）。
---

#### **2. 核心 Hook**
- **`useQuery`**  
  ```jsx
  const { data, isLoading, error, refetch } = useQuery({
    queryKey: ['todos'], // 唯一标识
    queryFn: fetchTodos, // 异步函数
    staleTime: 5000,     // 数据保鲜期（毫秒）
  });
  ```
  - **作用**：获取数据，自动缓存并管理加载/错误状态。
  - **关键状态**：  
    - `isLoading`：首次加载中  
    - `isError`：请求失败  
    - `isSuccess`：请求成功  
    - `isFetching`：任何请求进行中（包括后台刷新）

- **`useMutation`**  
  ```jsx
  const mutation = useMutation({
    mutationFn: addTodo,  // 异步函数（POST/PUT/DELETE）
    onSuccess: () => {
      queryClient.invalidateQueries(['todos']); // 使缓存失效
    },
  });
  ```
  - **作用**：执行数据变更操作（增删改），支持乐观更新、自动重试。

- **`useInfiniteQuery`**  
  ```jsx
  const { data, fetchNextPage } = useInfiniteQuery({
    queryKey: ['projects'],
    queryFn: ({ pageParam = 1 }) => fetchProjects(pageParam),
    getNextPageParam: (lastPage) => lastPage.nextPage,
  });
  ```
  - **作用**：分页或无限滚动场景，管理多页数据加载。

---

#### **3. 核心配置**
- **查询键（Query Key）**  
  - 唯一标识查询，*用于缓存匹配和失效*。  
  - 支持分层结构：`['todos', { status: 'done' }]`。

- **缓存策略**  
  - `staleTime`：数据保鲜时间（过期后自动重新获取）。  
  - `cacheTime`：未使用数据的缓存保留时间（默认 5 分钟）。  
  - `refetchOnWindowFocus`：窗口聚焦时自动刷新（默认 `true`）。  
  - `retry`：失败自动重试次数（默认 3 次）。

- **全局配置（QueryClient）**  
  ```jsx
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { staleTime: 60 * 1000 },
    },
  });

  // 包裹根组件
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
  ```

---

#### **4. 高级功能**
- **数据预取（Prefetching）**  
  ```jsx
  await queryClient.prefetchQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,
  });
  ```

- **乐观更新（Optimistic Updates）**  
  ```jsx
  useMutation({
    mutationFn: updateTodo,
    onMutate: async (newTodo) => {
      await queryClient.cancelQueries(['todos']);
      const prevTodos = queryClient.getQueryData(['todos']);
      queryClient.setQueryData(['todos'], (old) => [...old, newTodo]);
      return { prevTodos }; // 用于错误回滚
    },
    onError: (err, newTodo, context) => {
      queryClient.setQueryData(['todos'], context.prevTodos);
    },
  });
  ```

- **自动垃圾回收**：未使用的查询数据在 `cacheTime` 后自动清除。

---

#### **5. 开发者工具**
- **React Query Devtools**  
  ```jsx
  import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

  <ReactQueryDevtools initialIsOpen={false} />;
  ```
  - 可视化查看缓存、查询状态及手动操作数据。

---

#### **6. 与状态管理库对比**
| **维度**          | **React Query**                  | **Redux**                          |
|-------------------|----------------------------------|-------------------------------------|
| 数据来源          | 服务端状态                       | 客户端状态                          |
| 缓存管理          | 自动（基于时间/依赖）           | 需手动实现                          |
| 数据更新          | 后台自动同步/手动失效            | 需通过 Action 触发                  |
| 代码复杂度        | 低（声明式 API）                 | 高（需定义 Action、Reducer）        |
| 适用场景          | API 数据管理                     | 全局客户端状态管理                  |

---

#### **7. 最佳实践**
- **分离服务端与客户端状态**：  
  - React Query 管理 API 数据，Context/Redux 管理 UI 状态（如弹窗开关）。
  
- **合理设置缓存策略**：  
  - 高频变数据：`staleTime: 0`（实时更新）。  
  - 低频静态数据：`staleTime: Infinity`（永不自动刷新）。

- **错误统一处理**：  
  ```jsx
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        onError: (error) => toast.error(error.message),
      },
    },
  });
  ```

---

#### **8. 常见问题**
- **Q：如何手动触发查询？**  
  ```jsx
  const { refetch } = useQuery({ queryKey: ['todos'], ... });
  <button onClick={refetch}>刷新</button>
  ```

- **Q：如何清除缓存数据？**  
  ```jsx
  queryClient.removeQueries({ queryKey: ['todos'] });
  ```

- **Q：如何依赖其他查询结果？**  
  ```jsx
  const { data: user } = useQuery(['user'], fetchUser);
  const { data: projects } = useQuery({
    queryKey: ['projects', user.id],
    queryFn: () => fetchProjects(user.id),
    enabled: !!user.id, // 依赖生效后触发
  });
  ```

---

### **总结**
- **核心价值**：React Query 将服务端状态管理标准化，减少手动处理异步数据的复杂度。
- **核心能力**：自动缓存、后台刷新、分页管理、错误重试、乐观更新。
- **适用场景**：任何涉及 API 交互的 React 应用，替代传统的 `useEffect` + 手动状态管理。


### 相关资料
- [精通React Query，由React Query核心团队合作打造: 像专业人士一样构建应用程序](https://www.bilibili.com/video/BV1ez421q7uV/?spm_id_from=333.337.search-card.all.click&vd_source=22af953ea4c09540ad1966711a2d53f0)
- 