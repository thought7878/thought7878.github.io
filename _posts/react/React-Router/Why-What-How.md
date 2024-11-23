# Why
- **实现单页应用的页面导航**
    - **动态加载组件**：在单页应用中，页面的内容通常是根据用户的操作（如点击链接）动态加载的，而不是像传统的多页应用那样每次都从服务器获取新的页面。React - Router *允许开发者根据不同的 URL 路径来加载和展示不同的 React 组件*。例如，在一个包含多个页面（如首页、产品页、关于我们页）的单页应用中，当用户点击 “产品页” 的链接时，React - Router 可以**动态地加载和渲染产品页对应的*组件***，而不需要重新加载整个页面，提供了更流畅的用户体验。
    - **创建复杂的路由结构**：它支持嵌套路由，使得可以构建复杂的应用结构。例如，在一个电商应用中，产品页可能有多个子页面，如产品详情页、产品评论页等。*通过嵌套路由，可以将产品详情页和产品评论页作为产品页的子路由，这样当用户在产品页的基础上访问这些子页面时，URL 的变化和组件的加载能够很好地配合，并且可以方便地共享父路由（产品页）的一些状态和逻辑*。
- **管理应用的状态和历史记录**
    - **浏览器历史记录支持**：React - Router 能够与浏览器的历史记录（History API）进行交互，*使得应用的导航行为和浏览器的前进、后退按钮功能相匹配*。当用户使用浏览器的前进或后退按钮时，React - Router 会根据历史记录加载相应的组件，让用户感觉就像在浏览传统的多页应用一样。例如，用户在浏览了几个不同的页面后，通过浏览器后退按钮回到之前的页面，React - Router 会正确地重新加载之前的组件并恢复相应的状态。
    - **URL 作为应用状态的一部分**：URL 在 React - Router 中可以作为应用状态的一种表示方式。通过改变 URL，可以触发不同组件的加载，并且可以在组件中根据 URL 参数来获取和处理数据。例如，在一个搜索结果页面中，搜索关键词可以作为 URL 的参数，当用户分享这个 URL 时，其他人可以通过访问相同的 URL 获取相同的搜索结果，并且应用可以根据这个 URL 参数来重新获取和展示搜索结果。
- **方便的代码组织和维护**
    - **基于路由的代码拆分**：使用 React - Router 可以方便地进行代码拆分，将不同路由对应的组件和逻辑放在不同的文件或模块中。这样可以使代码结构更加清晰，便于团队开发和维护。例如，将与用户认证相关的路由（登录、注册等）放在一个模块中，将产品相关的路由放在另一个模块中，每个模块可以独立开发和测试。
    - **与组件的紧密结合**：它与 React 组件的集成非常紧密，使得开发者可以在组件内部方便地获取路由相关的信息，如当前路径、路径参数等。例如，一个组件可以通过 React - Router 提供的钩子（如`useParams`）来获取 URL 中的参数，并根据这些参数来获取和展示数据，使得组件的功能和路由信息紧密结合，提高了代码的可读性和可维护性。

# What
React - Router 是一个用于在 React 应用中实现路由功能的库。它提供了一系列的组件和钩子，使得在单页应用（SPA）中**能够方便地实现页面导航、 URL 与组件的映射**。


# How
## BrowserRouter 组件

`BrowserRouter`是 React - Router 提供的一个路由器组件，它使用 HTML5 的 History API 来保持 UI 和 URL 的同步。通常，它会包裹整个应用，使得应用中的所有路由都能在浏览器环境下正常工作。

示例：

```jsx
import React from 'react';
import ReactDOM from 'react - dom';
import { BrowserRouter } from 'react - router - dom';
import App from './App';
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);
```

## Routes 和 Route 组件
`Routes`组件用于定义一组路由，它是路由配置的容器。`Route`组件则用于定义具体的路由规则，它有两个重要的属性：`path`和`element`。`path`属性指定了路由的路径，`element`属性则指定了当路径匹配时要渲染的 React 组件。

示例：

```jsx
import React from 'react';
import { Routes, Route } from 'react - router - dom';
import Home from './Home';
import About from './About';
function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  );
}
```

在这个示例中，当用户访问应用的根路径（`/`）时，`Home`组件会被渲染；当用户访问`/about`路径时，`About`组件会被渲染。

  
## 导航（Link 组件）
`Link`组件用于在应用内部创建链接，它会在不重新加载整个页面的情况下，触发路由的切换。它的`to`属性指定了链接的目标路径。

示例：

```jsx
import React from 'react';
import { Link } from 'react - router - dom';
function Home() {
  return (
    <div>
      <h1>Home Page</h1>
      <Link to="/about">Go to About Page</Link>
    </div>
  );
}
```

在这个`Home`组件中，`Link`组件创建了一个指向`/about`路径的链接。当用户点击这个链接时，应用会导航到`About`页面，并且`About`组件会被渲染。

## 获取路由参数（useParams）
`useParams`是 React - Router 提供的一个钩子函数，用于在组件中获取路由参数。当路由路径中包含动态参数时（例如`/user/:id`，其中`id`是参数），可以使用`useParams`来获取这个参数的值。

示例：

```jsx
import React, { useEffect } from 'react';
import { useParams } from 'react - router - dom';
function UserProfile() {
  const { id } = useParams();
  useEffect(() => {
    // 可以根据id参数获取用户数据，例如发送网络请求
    console.log(`获取用户ID为${id}的资料`);
  }, [id]);
  return (
    <div>
      <h1>用户 {id} 的资料</h1>
    </div>
  );
}
```

在这个`UserProfile`组件中，假设路由路径是`/user/:id`，`useParams`用于获取`id`参数的值。然后，可以在`useEffect`中根据这个`id`参数来获取用户资料，例如发送网络请求来获取详细的用户信息。

## 嵌套路由（Route 和 Outlet）
**Route 的嵌套使用**
在复杂的应用中，可能需要使用嵌套路由。可以在一个`Route`组件内部再定义一组`Routes`和`Route`来实现嵌套路由。

示例：

```jsx
import React from 'react';
import { Routes, Route, Outlet } from 'react - router - dom';
import Product from './Product';
import ProductDetails from './ProductDetails';
function Products() {
  return (
    <div>
      <h1>产品页面</h1>
      <Outlet />
    </div>
  );
}
function App() {
  return (
    <Routes>
      <Route path="/products" element={<Products />}>
        <Route path=":productId" element={<ProductDetails />} />
      </Route>
    </Routes>
  );
}
```

在这个示例中，`/products`是父路由，对应的组件是`Products`。在`Products`组件中有一个`Outlet`组件，它用于标记子路由组件的渲染位置。`/products/:productId`是子路由，对应的组件是`ProductDetails`。当用户访问`/products/1`（假设`1`是产品 ID）这样的路径时，`Products`组件会先被渲染，然后`ProductDetails`组件会在`Products`组件中的`Outlet`位置被渲染。