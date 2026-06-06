在现代 React 架构中，***TanStack Query** 和 **Zustand** 被公认为状态管理的“黄金搭档”*。它们并*非竞争关系，而是完美互补的拼图：*

*   **TanStack Query** 负责 **Server State（服务端状态）**：处理异步请求、缓存、后台更新、分页等。
*   **Zustand** 负责 **Client State（客户端状态）**：处理同步的 UI 状态、全局配置、本地持久化、跨组件通信等。

*将两者优雅地集成，可以彻底消灭应用中 90% 的“面条代码”和难以追踪的 Bug*。

以下是它们集成的核心原则、实战模式与架构级避坑指南。

---

## 一、 核心架构原则：划清界限

在动手写代码前，必须确立 **“单一数据源 (Single Source of Truth)”** 原则：
1.  **绝不重叠**：如果数据来自 API 且需要缓存，**只放 TanStack Query**；如果是纯前端 UI 交互状态，**只放 Zustand**。
2.  **严禁“二次搬运”**：绝对不要用 TanStack Query 请求到数据后，再在 `onSuccess` 中塞进 Zustand 里供组件读取。*这会导致严重的状态冗余和同步问题。组件应直接 `useQuery` 读取数据*。

---

## 二、 四大经典集成模式 (附代码)

### 模式一：Zustand 驱动 Query (Zustand 作为 Query 的输入)
**场景**：全局的搜索关键字、多标签页的当前激活 Tab、全局筛选条件。这些状态由 Zustand 管理，当它们变化时，TanStack Query 自动重新请求数据。

```tsx
// 1. Zustand Store (管理 UI 筛选状态)
import { create } from 'zustand';

interface FilterState {
  keyword: string;
  category: string;
  setKeyword: (k: string) => void;
  setCategory: (c: string) => void;
}

export const useFilterStore = create<FilterState>((set) => ({
  keyword: '',
  category: 'all',
  setKeyword: (keyword) => set({ keyword }),
  setCategory: (category) => set({ category }),
}));

// 2. React 组件 (Zustand 状态作为 QueryKey)
function ProductList() {
  // 从 Zustand 订阅状态
  const keyword = useFilterStore((s) => s.keyword);
  const category = useFilterStore((s) => s.category);

  // Zustand 的状态直接作为 QueryKey 的一部分
  // 当 keyword 或 category 变化时，Query 会自动 refetch
  const { data, isLoading } = useQuery({
    queryKey: ['products', { keyword, category }], 
    queryFn: () => fetchProducts(keyword, category),
  });

  if (isLoading) return <Spinner />;
  return <ul>{data.map(p => <li key={p.id}>{p.name}</li>)}</ul>;
}
```

### 模式二：Query 同步到 Zustand (初始化全局上下文)
**场景**：应用启动时，通过 Query 获取当前登录用户信息。获取成功后，需要将用户的核心信息（如 Token、角色、权限）存入 Zustand，以便在 **React 组件外部**（如 Axios 拦截器、路由守卫、WebSocket 连接）中使用。

```tsx
// 1. Zustand Store (管理全局鉴权状态)
export const useAuthStore = create((set) => ({
  user: null,
  token: null,
  setAuth: (user, token) => set({ user, token }),
  clearAuth: () => set({ user: null, token: null }),
}));

// 2. 桥接组件 (在应用根部静默运行)
// 这个组件不渲染任何 UI，只负责将 Query 的数据同步到 Zustand
function AuthSyncBridge() {
  const { data: session } = useQuery({ 
    queryKey: ['session'], 
    queryFn: fetchSession 
  });
  
  const setAuth = useAuthStore((s) => s.setAuth);

  useEffect(() => {
    if (session?.user) {
      // 将 Query 获取的数据同步到 Zustand
      setAuth(session.user, session.token);
    }
  }, [session, setAuth]);

  return null; 
}

// 3. 在 React 组件外使用 (如 Axios 拦截器)
// api/client.ts
import { useAuthStore } from '../store/authStore';

axios.interceptors.request.use((config) => {
  // 直接通过 getState() 在组件外读取 Zustand 状态
  const token = useAuthStore.getState().token; 
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

### 模式三：Zustand Action 触发 Query 缓存失效 (跨状态联动)
**场景**：Zustand 管理本地的“购物车临时商品”或“暗黑模式”，当在 Zustand 中执行某个 action 修改了服务端数据后，需要让 TanStack Query 重新拉取最新数据。
**难点**：Zustand 定义在 React 组件树之外，如何拿到 `queryClient`？
**解法**：将 `queryClient` 作为单例导出。

```tsx
// 1. 导出 QueryClient 单例
// lib/queryClient.ts
import { QueryClient } from '@tanstack/react-query';
export const queryClient = new QueryClient();

// 2. Zustand Store
// store/cartStore.ts
import { queryClient } from '../lib/queryClient';

export const useCartStore = create((set) => ({
  localItems: [],
  addToCart: async (item) => {
    // 1. 调用 API 修改服务端数据
    await api.addCartItem(item);
    
    // 2. 更新 Zustand 本地状态 (可选，用于乐观更新)
    set((state) => ({ localItems: [...state.localItems, item] }));
    
    // 3. 核心：在组件外直接让 Query 缓存失效，触发全局购物车列表刷新
    queryClient.invalidateQueries({ queryKey: ['cartItems'] });
  },
}));
```

### 模式四：Query Mutation 更新 Zustand UI 状态
**场景**：表单提交（Mutation）成功或失败后，需要触发全局的 Toast 提示，或者关闭某个全局 Loading 遮罩（这些 UI 状态由 Zustand 管理）。

```tsx
function UpdateProfileForm() {
  const showToast = useUIStore((s) => s.showToast);
  const closeModal = useUIStore((s) => s.closeModal);

  const mutation = useMutation({
    mutationFn: updateProfile,
    onSuccess: (data) => {
      // 1. 更新 Query 缓存
      queryClient.setQueryData(['profile'], data);
      // 2. 触发 Zustand 管理的 UI 状态
      showToast('资料更新成功！', 'success');
      closeModal();
    },
    onError: (error) => {
      showToast(error.message, 'error');
    }
  });

  return <button onClick={() => mutation.mutate(formData)}>保存</button>;
}
```

---

## 三、 架构级避坑指南 (极其重要)

### 1. 警惕“无限循环”陷阱 (Infinite Loops)
**错误反模式**：
Zustand 状态变化 -> 触发 Query refetch -> Query `onSuccess` 回调中又去修改了 Zustand -> Zustand 变化再次触发 Query...
**解决方案**：
确保数据流是**单向**的。如果 Query 的结果必须影响 Zustand，请使用 `useEffect` 并严格控制依赖项，或者只在特定的用户交互（如点击按钮）时进行同步，而不是在每次 Query 成功时都同步。

### 2. 避免“伪集成”导致的状态冗余
**错误反模式**：
```tsx
// ❌ 极度糟糕的做法
const { data } = useQuery({ queryKey: ['users'], queryFn: fetchUsers });
useEffect(() => {
  if (data) useUserStore.setState({ users: data }); // 把 Query 数据塞进 Zustand
}, [data]);
// 组件再从 Zustand 读取 users 渲染
```
**为什么错**：这违背了单一数据源原则。内存中存了两份一模一样的数据，且 `useEffect` 的异步执行会导致首屏渲染时 Zustand 里没数据，产生闪烁。
**正确做法**：组件直接 `useQuery` 读数据，Zustand 只存 `selectedUserId` 等 UI 状态。

### 3. QueryClient 的注入方式选择
*   **推荐（单例导出）**：如上文的 `lib/queryClient.ts`，简单直接，完美支持 Zustand、路由守卫等非 React 环境。
*   **不推荐（Context 传递）**：试图通过 React Context 把 `queryClient` 传给 Zustand，这违背了 Zustand “脱离组件树” 的设计初衷，且代码极其臃肿。

### 4. SSR (服务端渲染) 环境下的内存泄漏
如果你在使用 Next.js，**绝对不能**在模块顶层直接 `export const queryClient = new QueryClient()`。
因为 Next.js 的 Node 环境是长驻的，所有用户的请求会共享同一个 `queryClient` 实例，导致**用户 A 看到了用户 B 的缓存数据**（严重的数据串号和安全漏洞）。
**Next.js 正确做法**：
```tsx
// 在组件内部或工厂函数中创建
function makeQueryClient() {
  return new QueryClient({ defaultOptions: { queries: { staleTime: 60 * 1000 } } });
}

let browserQueryClient: QueryClient | undefined = undefined;

export function getQueryClient() {
  if (typeof window === 'undefined') {
    // Server: 每次请求创建全新的 client
    return makeQueryClient();
  } else {
    // Browser: 复用同一个 client
    if (!browserQueryClient) browserQueryClient = makeQueryClient();
    return browserQueryClient;
  }
}
```

---

## 四、 总结：完美架构的全景图

在一个成熟的现代 React 应用中，状态管理的职责分配应该如下所示：

1.  **URL (React Router)**：管理分页 (`?page=2`)、搜索词 (`?q=react`)、筛选器。保证页面可分享、刷新不丢失。
2.  **TanStack Query**：管理所有 API 数据（用户列表、商品详情）。负责缓存、去重、后台静默更新。
3.  **Zustand**：
    *   管理全局 UI 状态（侧边栏折叠、主题色、全局 Toast）。
    *   管理复杂的表单多步向导状态。
    *   管理需要跨组件共享但**不需要发请求**的临时数据（如购物车里的本地草稿）。
    *   作为“桥梁”，在组件外部（Axios/Router）提供全局上下文（如 Token）。

通过这种 **“URL 承载共享 -> Query 承载异步 -> Zustand 承载交互”** 的三层架构，你的代码将变得极其清晰、可测试，且性能达到最优。