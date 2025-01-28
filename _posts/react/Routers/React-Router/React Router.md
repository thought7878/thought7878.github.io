### React Router 的核心概念  
`React Router` 是 React 生态中用于实现**单页面应用（SPA）路由管理**的核心库。*它通过 URL 与组件之间的映射关系*，实现页面间的无刷新跳转和状态同步。以下是 React Router 的核心用法和最佳实践（以 **v6** 版本为主）。

---

### 基础路由配置  
#### 路由入口与基本结构  
使用 `BrowserRouter` 包裹整个应用，并通过 `Routes` 和 `Route` 定义路由规则：  
```jsx  
import { BrowserRouter, Routes, Route } from "react-router-dom";  

function App() {  
  return (  
    <BrowserRouter>  
      <Routes>  
        <Route path="/" element={<HomePage />} />  
        <Route path="/about" element={<AboutPage />} />  
        <Route path="/user/:id" element={<UserPage />} />  
      </Routes>  
    </BrowserRouter>  
  );  
}  
```

#### 导航组件  
通过 `Link` 或 `NavLink` 实现页面跳转（避免整页刷新）：  
```jsx  
import { Link, NavLink } from "react-router-dom";  

function Navigation() {  
  return (  
    <nav>  
      {/* 普通链接 */}  
      <Link to="/">首页</Link>  

      {/* 高亮当前激活链接 */}  
      <NavLink  
        to="/about"  
        style={({ isActive }) => ({ color: isActive ? "red" : "black" })}  
      >  
        关于  
      </NavLink>  
    </nav>  
  );  
}  
```

---

### 动态路由与参数获取  
#### 定义动态路由  
在路径中使用 `:param` 语法定义动态参数：  
```jsx  
<Route path="/user/:id" element={<UserPage />} />  
```

#### 获取 URL 参数  
通过 `useParams` Hook 读取动态参数：  
```jsx  
import { useParams } from "react-router-dom";  

function UserPage() {  
  const { id } = useParams();  
  return <div>用户ID: {id}</div>;  
}  
```

#### 查询参数处理  
通过 `useSearchParams` 获取和修改查询参数：  
```jsx  
import { useSearchParams } from "react-router-dom";  

function SearchPage() {  
  // https://domaim.com/user?q=1
  const [searchParams, setSearchParams] = useSearchParams();  
  const keyword = searchParams.get("q");  

  const handleSearch = (newKeyword) => {  
    setSearchParams({ q: newKeyword });  
  };  

  return <input value={keyword} onChange={(e) => handleSearch(e.target.value)} />;  
}  
```

---

### 嵌套路由与布局  
#### 嵌套路由定义  
通过 `<Outlet>` 实现嵌套路由的*占位*渲染：  
```jsx  
// 父路由组件  
function Dashboard() {  
  return (  
    <div>  
      <h1>仪表盘</h1>  
      <Outlet />  {/* 子路由在此渲染 */}  
    </div>  
  );  
}  

// 路由配置  
<Route path="/dashboard" element={<Dashboard />}>  
  <Route index element={<DashboardHome />} />  
  <Route path="settings" element={<DashboardSettings />} />  
</Route>  
```

#### 相对路径与绝对路径  
- **相对路径**：`settings` 会解析为 `/dashboard/settings`  
- **绝对路径**：以 `/` 开头（如 `/settings`）会忽略父路径  

---

### 编程式导航  
通过 `useNavigate` Hook 实现代码中跳转：  
```jsx  
import { useNavigate } from "react-router-dom";  

function LoginButton() {  
  const navigate = useNavigate();  

  const handleLogin = () => {  
    // 跳转到指定路径  
    navigate("/dashboard");  

    // 替换历史记录（无返回按钮）  
    navigate("/dashboard", { replace: true });  

    // 携带状态  
    navigate("/dashboard", { state: { from: "login" } });  
  };  

  return <button onClick={handleLogin}>登录</button>;  
}  
```

---

### 路由懒加载与代码分割  
结合 `React.lazy` 和 `Suspense` *实现按需加载*：  
```jsx  
import { lazy, Suspense } from "react";  

const LazyAbout = lazy(() => import("./AboutPage"));  

function App() {  
  return (  
    <Suspense fallback={<div>加载中...</div>}>  
      <Routes>  
        <Route path="/about" element={<LazyAbout />} />  
      </Routes>  
    </Suspense>  
  );  
}  
```

---

### 路由守卫与权限控制  
通过*高阶组件*或*自定义 Hook* 实现路由拦截：  
- 高阶组件：添加登陆、权限的验证逻辑
- 自定义Hook：获取登陆状态
```jsx  
function PrivateRoute({ children }) {  
  // 自定义Hook，获取登陆状态
  const isAuthenticated = checkAuth();  
  const navigate = useNavigate();  

  useEffect(() => {  
    if (!isAuthenticated) navigate("/login");  
  }, [isAuthenticated]);  

  return isAuthenticated ? children : null;  
}  

// 使用  
<Route  
  path="/admin"  
  element={  
    <PrivateRoute>  
      <AdminPage />  
    </PrivateRoute>  
  }  
/>  
```

---

### 常见问题与解决方案  

#### 处理 404 页面  
通过 `path="*"` 捕获未匹配的路由：  
```jsx  
<Route path="*" element={<NotFoundPage />} />  
```

#### 重定向  
使用 `Navigate` 组件或 `useNavigate` Hook 实现重定向：  
```jsx  
// 组件形式  
<Route path="/old" element={<Navigate to="/new" replace />} />  

// 代码形式  
const navigate = useNavigate();  
navigate("/new", { replace: true });  
```

---

### 总结  
- **核心组件**：`BrowserRouter`、`Routes`、`Route`、`Link`  
- **动态路由**：通过 `:param` 和 `useParams` 实现动态路径  
- **嵌套路由**：使用 `<Outlet>` 占位渲染子路由  
- **编程式导航**：`useNavigate` 控制跳转逻辑  
- **性能优化**：结合 `React.lazy` 实现懒加载  
- **权限控制**：通过路由守卫拦截未授权访问  

React Router 通过声明式的 API 让路由管理变得直观，同时保持与 React 组件生态的无缝集成。掌握这些模式后，可以轻松构建复杂且高效的单页面应用！