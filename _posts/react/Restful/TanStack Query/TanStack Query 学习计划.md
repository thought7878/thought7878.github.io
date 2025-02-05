> 我计划学习TanStack Query，请帮我列一个详细的学习步骤、学习计划

以下是针对 **TanStack Query（原 React Query）** 的详细学习步骤与计划，分为 **4 个阶段**，从基础到实战逐步深入，帮你系统掌握这一工具：

---

### 一、**基础入门（1-3 天）**
#### 1. **前置知识准备**
   - **必会**：React Hooks（`useState`, `useEffect`）、JavaScript 异步操作（Promise、async/await）
   - **推荐**：了解 REST API 基础概念、HTTP 方法（GET/POST/PUT/DELETE）

#### 2. **核心概念初识**
   - **目标**：理解 TanStack Query 的核心功能与设计思想
   - **学习内容**：
     - 什么是「服务端状态」与「客户端状态」？
     - 为什么需要 TanStack Query（对比传统 `useState` + `useEffect` 模式）？
     - 核心术语：`Query`（查询）、`Mutation`（变更）、`Query Key`（查询键）、`Stale Time`（保鲜时间）

#### 3. **环境搭建**
   - **安装**：
     ```bash
     npm install @tanstack/react-query @tanstack/react-query-devtools
     ```
   - **基础配置**（在 React 根组件中）：
     ```jsx
     import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
     const queryClient = new QueryClient();

     function App() {
       return (
         <QueryClientProvider client={queryClient}>
           <YourApp />
         </QueryClientProvider>
       );
     }
     ```

#### 4. **第一个查询与变更**
   - **查询数据（useQuery）**：
     ```jsx
     const { data, isLoading, isError } = useQuery({
       queryKey: ['todos'], 
       queryFn: () => fetch('/api/todos').then(res => res.json())
     });
     ```
   - **修改数据（useMutation）**：
     ```jsx
     const mutation = useMutation({
       mutationFn: (newTodo) => fetch('/api/todos', { method: 'POST', body: newTodo }),
       onSuccess: () => queryClient.invalidateQueries(['todos'])
     });
     ```

---

### 二、**核心功能进阶（3-7 天）**
#### 1. **查询深度使用**
   - **查询键（Query Key）**：动态键、层级键（`['todos', { status: 'done' }]`）
   - **查询参数传递**：
     ```jsx
     useQuery({
       queryKey: ['todo', todoId],
       queryFn: () => fetchTodo(todoId)
     });
     ```
   - **依赖查询**（`enabled` 选项）：
     ```jsx
     const { data: user } = useQuery(['user', userId], fetchUser);
     const { data: projects } = useQuery(['projects', userId], fetchProjects, {
       enabled: !!userId // 依赖 userId 存在后执行
     });
     ```

#### 2. **变更（Mutation）高级模式**
   - **乐观更新（Optimistic Updates）**：
     ```jsx
     useMutation({
       mutationFn: updateTodo,
       onMutate: async (newTodo) => {
         await queryClient.cancelQueries(['todos']);
         const prevTodos = queryClient.getQueryData(['todos']);
         queryClient.setQueryData(['todos'], old => [...old, newTodo]);
         return { prevTodos };
       },
       onError: (err, newTodo, context) => {
         queryClient.setQueryData(['todos'], context.prevTodos);
       }
     });
     ```
   - **自动重试与错误处理**：
     ```jsx
     useMutation({
       mutationFn: addTodo,
       retry: 3, // 失败自动重试 3 次
       onError: (error) => toast.error(error.message)
     });
     ```

#### 3. **缓存与性能优化**
   - **缓存策略**：`staleTime`（保鲜时间）、`cacheTime`（缓存保留时间）
   - **数据预取（Prefetching）**：
     ```jsx
     await queryClient.prefetchQuery(['todos'], fetchTodos);
     ```
   - **手动操作缓存**：
     ```jsx
     queryClient.invalidateQueries(['todos']); // 标记缓存失效
     queryClient.setQueryData(['todos'], newData); // 直接更新缓存
     ```

#### 4. **开发者工具使用**
   - **React Query Devtools** 集成：
     ```jsx
     import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
     <ReactQueryDevtools initialIsOpen={false} />
     ```
   - **功能**：查看缓存状态、手动触发查询/变更、调试查询键

---

### 三、**实战场景应用（7-14 天）**
#### 1. **分页与无限滚动**
   - **分页查询**（`keepPreviousData` 平滑过渡）：
     ```jsx
     const [page, setPage] = useState(1);
     const { data } = useQuery({
       queryKey: ['projects', page],
       queryFn: () => fetchProjects(page),
       keepPreviousData: true
     });
     ```
   - **无限滚动（useInfiniteQuery）**：
     ```jsx
     const { data, fetchNextPage } = useInfiniteQuery({
       queryKey: ['projects'],
       queryFn: ({ pageParam = 1 }) => fetchProjects(pageParam),
       getNextPageParam: (lastPage) => lastPage.nextPage
     });
     ```

#### 2. **与 UI 库集成**
   - **加载状态处理**：结合骨架屏（Skeleton）、加载动画
   - **错误边界（Error Boundaries）**：全局统一处理查询错误

#### 3. **身份认证集成**
   - **Token 自动注入**：在 Query Client 中统一配置请求头
     ```jsx
     const queryClient = new QueryClient({
       defaultOptions: {
         queries: {
           queryFn: async ({ queryKey }) => {
             const response = await fetch('/api' + queryKey[0], {
               headers: { Authorization: `Bearer ${getToken()}` }
             });
             return response.json();
           }
         }
       }
     });
     ```

#### 4. **服务端渲染（SSR/Next.js）**
   - **Next.js 预取数据**：
     ```jsx
     export async function getServerSideProps() {
       const queryClient = new QueryClient();
       await queryClient.prefetchQuery(['todos'], fetchTodos);
       return { props: { dehydratedState: dehydrate(queryClient) } };
     }
     ```
   - **客户端水合（Hydration）**：
     ```jsx
     import { Hydrate } from '@tanstack/react-query';
     function MyApp({ Component, pageProps }) {
       return (
         <Hydrate state={pageProps.dehydratedState}>
           <Component {...pageProps} />
         </Hydrate>
       );
     }
     ```

---

### 四、**高级优化与最佳实践（14 天+）**
#### 1. **性能优化技巧**
   - **避免过度渲染**：使用 `select` 选择最小数据子集
     ```jsx
     useQuery({
       queryKey: ['todos'],
       queryFn: fetchTodos,
       select: (data) => data.filter(todo => todo.isDone)
     });
     ```
   - **批量失效查询键**：使用 `queryClient.invalidateQueries({ predicate: ... })`

#### 2. **自定义 Hooks**
   - **抽象通用逻辑**：
     ```jsx
     export function useTodos() {
       return useQuery({
         queryKey: ['todos'],
         queryFn: fetchTodos,
         staleTime: 5 * 60 * 1000
       });
     }
     ```

#### 3. **TypeScript 深度集成**
   - **类型安全查询**：
     ```tsx
     interface Todo {
       id: number;
       title: string;
     }
     const { data } = useQuery<Todo[]>(['todos'], fetchTodos);
     ```

#### 4. **测试策略**
   - **单元测试**：使用 `@testing-library/react-hooks` 测试自定义 Hooks
   - **Mock API**：通过 `Mock Service Worker (MSW)` 拦截请求

---

### **学习资源推荐**
1. **官方文档**：[TanStack Query Docs](https://tanstack.com/query/latest)（必读）
2. **实战课程**：
   - Udemy: [React Query: Server State Management in React](https://www.udemy.com/course/react-query-react)
   - YouTube: [TanStack Query 完整教程](https://www.youtube.com/watch?v=novnyCaa7To)（TkDodo）
3. **开源项目参考**：
   - [GitHub 热门项目](https://github.com/tanstack/query/tree/main/examples)中的示例代码
   - [React Query + Next.js 全栈模板](https://github.com/vercel/next.js/tree/canary/examples/with-react-query)

---

### **学习计划建议**
| **阶段**       | **每日学习时间** | **目标**                          |
|----------------|------------------|-----------------------------------|
| 基础入门       | 1-2 小时         | 完成官方文档 Quick Start          |
| 核心功能进阶   | 2 小时           | 实现一个带查询/变更的完整功能模块 |
| 实战场景应用   | 2-3 小时         | 完成分页、无限滚动、错误处理 Demo |
| 高级优化       | 1-2 小时         | 重构现有项目集成 TanStack Query   |

通过以上步骤，你可以在 **2-3 周** 内系统掌握 TanStack Query，并能在实际项目中灵活运用其优化数据流管理。