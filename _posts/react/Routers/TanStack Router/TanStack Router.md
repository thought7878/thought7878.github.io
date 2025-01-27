### 什么是 TanStack Router

TanStack Router 是一个现代化的 React 路由库，是 TanStack 生态系统的重要组成部分。它致力于为 React 应用提供高效、灵活且类型安全的路由解决方案，通过声明式的路由配置、强大的数据加载和预加载功能，以及丰富的导航控制 API，帮助开发者更轻松地构建复杂的单页面应用（SPA）。

### 为什么使用 TanStack Router

#### 1. 类型安全

深度集成 TypeScript，在路由配置、导航函数和数据加载过程中提供全面的类型检查。这意味着开发者在编写代码时，编译器就能捕获类型相关的错误，减少运行时错误，提高代码的健壮性和可维护性。例如在定义路由参数和数据加载器返回值时，TypeScript 能确保数据类型的一致性。

#### 2. 数据加载与预加载

将数据加载与路由紧密结合，提供简洁的加载器（loader）机制。在路由切换时，自动触发加载器获取所需数据，同时支持数据预加载。这可以显著提升用户体验，减少页面加载时间，并且与 TanStack Query 等数据管理库无缝集成，方便进行数据缓存和同步。

#### 3. 声明式路由配置

采用清晰直观的声明式语法，使用 JavaScript 对象来定义路由结构。这种方式使得路由配置代码易于理解和维护，尤其是在处理复杂的嵌套路由时，能清晰地表达路由之间的层次关系。

#### 4. 灵活的导航控制

提供丰富的导航 API，支持程序化导航、条件导航和导航守卫等功能。开发者可以根据不同的业务逻辑灵活控制路由跳转，实现如权限验证、用户登录状态检查等复杂的导航场景。

#### 5. 生态系统集成

作为 TanStack 生态系统的一部分，能与 TanStack 家族的其他库（如 TanStack Query、TanStack Table 等）完美配合，形成一套完整的数据管理和界面展示解决方案，提高开发效率。

### 怎么用 TanStack Router

#### 1. 安装

首先，使用 npm 或 yarn 安装 TanStack Router：

```bash
npm install @tanstack/react-router
# 或者
yarn add @tanstack/react-router
```

#### 2. 创建路由配置

定义路由结构，包括路径、组件和数据加载器等。以下是一个简单的示例：

```jsx
import React from "react";
import { createBrowserRouter } from "@tanstack/react-router";

// 定义路由组件
const Home = () => <div>这是主页</div>;
const About = () => <div>这是关于页面</div>;

// 创建路由配置
const rootRoute = {
  path: "/",
  children: [
    {
      path: "",
      component: Home,
    },
    {
      path: "about",
      component: About,
    },
  ],
};

// 创建浏览器路由器
const router = createBrowserRouter({
  routeTree: rootRoute,
});

export default router;
```

#### 3. 在应用中使用路由器

将路由器提供给 React 应用：

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { RouterProvider } from "@tanstack/react-router";
import router from "./router";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<RouterProvider router={router} />);
```

#### 4. 导航

在组件中使用 `useNavigate` 钩子进行程序化导航：

```jsx
import { useNavigate } from "@tanstack/react-router";

const NavigationButton = () => {
  const navigate = useNavigate();

  const handleClick = () => {
    navigate("/about");
  };

  return <button onClick={handleClick}>前往关于页面</button>;
};

export default NavigationButton;
```

#### 5. 数据加载

为路由添加数据加载器，在路由切换时自动获取数据：

```jsx
import React from "react";
import { createBrowserRouter } from "@tanstack/react-router";

const ProductPage = ({ data }) => {
  return <div>产品名称: {data.product.name}</div>;
};

const productRoute = {
  path: "/products/:productId",
  component: ProductPage,
  loader: async ({ params }) => {
    const productId = params.productId;
    const response = await fetch(`/api/products/${productId}`);
    const product = await response.json();
    return { product };
  },
};

const router = createBrowserRouter({
  routeTree: productRoute,
});

export default router;
```

通过以上步骤，你可以在 React 应用中使用 TanStack Router 实现基本的路由功能，包括路由配置、导航和数据加载。
