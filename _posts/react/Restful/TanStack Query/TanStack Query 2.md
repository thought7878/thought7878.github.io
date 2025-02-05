### TanStack Query（原 React Query）核心总结

---

#### **1. 核心定位**  
- **多框架支持**：支持 React、Vue、Solid 等框架（原 React Query 更名为 **TanStack Query**，核心功能一致）。  
- **核心能力**：专为**异步数据管理**设计，处理服务端状态（API 数据）的获取、缓存、同步、更新。  
- **核心价值**：替代传统的 `useEffect` + 手动状态管理，标准化数据流逻辑。

---

#### **2. 核心概念**  
- **查询（Query）**  
  - 定义：从服务端**读取数据**的操作（GET 请求）。  
  - 生命周期：`fresh` → `stale` → `inactive` → 垃圾回收。  
- **变更（Mutation）**  
  - 定义：向服务端**写入数据**的操作（POST/PUT/DELETE 请求）。  
  - 支持：乐观更新、自动重试、依赖失效。  
- **查询键（Query Key）**  
  - 唯一标识查询，支持层级结构（如 `['todos', { status: 'done' }]`）。  
  - 用于缓存匹配、手动操作（失效/更新）。  

---

#### **3. 核心 API**  
##### **Hook**  
- **`useQuery`**  
  ```tsx
  const { data, isLoading, isError, refetch } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,
    staleTime: 10_000,
  });
  ```  
  - **关键状态**：`isLoading`（首次加载）、`isFetching`（任何请求中）。  

- **`useMutation`**  
  ```tsx
  const mutation = useMutation({
    mutationFn: addTodo,
    onSuccess: () => {
      queryClient.invalidateQueries(['todos']);
    },
  });
  ```  
  - **关键方法**：`mutate`（触发变更）、`reset`（重置状态）。  

- **`useInfiniteQuery`**  
  ```tsx
  const { data, fetchNextPage } = useInfiniteQuery({
    queryKey: ['projects'],
    queryFn: ({ pageParam }) => fetchProjects(pageParam),
    getNextPageParam: (lastPage) => lastPage.nextCursor,
  });
  ```  
  - 用于无限滚动或分页加载。  

##### **全局管理器**  
- **`QueryClient`**  
  ```tsx
  const queryClient = new QueryClient({
    defaultOptions: { queries: { staleTime: 60 * 1000 } }
  });
  // 包裹根组件
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
  ```  
  - 管理所有查询缓存、全局配置。  

---

#### **4. 关键配置与优化**  
| **配置项**          | **作用**                               | **示例值**              |
|----------------------|----------------------------------------|-------------------------|
| `staleTime`          | 数据保鲜时间（过期后自动重新获取）     | `3000`（3 秒）          |
| `cacheTime`          | 未使用数据的缓存保留时间               | `5 * 60 * 1000`（5 分钟）|
| `refetchOnWindowFocus`| 窗口聚焦时自动刷新                     | `true`（默认）          |
| `retry`              | 失败自动重试次数                       | `2`                     |
| `enabled`            | 控制查询是否自动执行                   | `!!userId`（依赖生效后）|

---

#### **5. 高级功能**  
- **数据预取（Prefetching）**  
  ```tsx
  await queryClient.prefetchQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos,
  });
  ```  
- **乐观更新（Optimistic Update）**  
  ```tsx
  useMutation({
    mutationFn: updateTodo,
    onMutate: async (newTodo) => {
      await queryClient.cancelQueries(['todos']);
      const prev = queryClient.getQueryData(['todos']);
      queryClient.setQueryData(['todos'], old => [...old, newTodo]);
      return { prev };
    },
    onError: (err, _, context) => {
      queryClient.setQueryData(['todos'], context.prev);
    },
  });
  ```  
- **自动垃圾回收**：未使用的查询在 `cacheTime` 后自动清除。  

---

#### **6. 开发者工具**  
- **TanStack Query Devtools**  
  ```tsx
  import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
  <ReactQueryDevtools initialIsOpen={false} />
  ```  
  - 可视化查看缓存、手动触发操作。  

---

#### **7. 与 React Query 的主要区别**  
| **特性**            | **TanStack Query**                   | **React Query v3**           |
|----------------------|--------------------------------------|-------------------------------|
| 框架支持             | React、Vue、Solid                    | 仅 React                      |
| 类型定义             | 更严格的 TypeScript 支持             | 基础类型支持                  |
| 查询键类型           | 强制数组形式（`['todos']`）          | 支持字符串/数组               |
| 默认配置             | 部分默认值优化（如 `staleTime: 0`）  | 旧版默认值                    |

---

#### **8. 最佳实践**  
- **分离服务端与客户端状态**：  
  - TanStack Query 管理 API 数据，Context/Redux 管理 UI 状态。  
- **合理拆分查询键**：  
  ```tsx
  // 按业务拆分
  const { data: user } = useQuery({ queryKey: ['user', userId] });
  const { data: posts } = useQuery({ queryKey: ['posts', userId] });
  ```  
- **统一错误处理**：  
  ```tsx
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { onError: (err) => toast.error(err.message) },
      mutations: { onError: (err) => toast.error(err.message) },
    },
  });
  ```  

---

### **总结**  
- **核心定位**：TanStack Query 是 React Query 的演进版，专注异步数据流管理，支持多框架。  
- **核心能力**：自动缓存、后台刷新、错误重试、分页/无限加载、乐观更新。  
- **适用场景**：任何需要与 API 交互的应用，替代传统手动状态管理。  
- **迁移建议**：React 项目可平滑升级至 TanStack Query，注意查询键格式和 TypeScript 类型调整。