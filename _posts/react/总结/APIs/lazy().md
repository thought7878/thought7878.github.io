# Why

**适用场景和优势**：

   - **大型应用优化**：在具有众多组件的大型 React 应用中，特别是单页应用，*通过懒加载那些用户可能不会立即访问的组件*，如管理后台的一些高级设置页面或者用户资料详细页面等，可以减少初始包的大小，从而加快应用的首次加载速度。
   - **代码分割（Code - Splitting）**：`React.lazy`是实现代码分割的一种简单方式。它使得 Webpack 等打包工具可以*将每个懒加载的组件打包成独立的文件，这样在需要的时候才下载相应的组件代码，而不是一次性加载整个应用的所有代码*。

# What
- `React.lazy`是 React 框架中的一个函数，它允许你*以懒加载的方式引入组件*。懒加载（Lazy Loading）也称为延迟加载，是一种优化技术，其主要目的是*在需要的时候才加载组件的代码，而不是在应用程序启动时就全部加载*。这对于大型应用程序来说，可以显著地提高应用的初始加载速度和性能。
- 例如，在一个包含多个页面（每个页面由一个组件表示）的单页应用（SPA）中，用户可能不会访问所有的页面。如果不使用懒加载，所有页面组件的代码都会在应用启动时加载，*这会增加初始加载时间。而使用`React.lazy`，可以只在用户导航到特定页面时才加载该页面的组件代码*。

# How

`React.lazy`接受一个函数作为参数，这个函数应该返回一个动态`import()`调用，这个动态`import`指向你想要懒加载的组件模块。这里`import`函数返回一个 Promise，它会在组件模块加载完成后被解析。
例如，假设你有一个名为`Dashboard`的组件，它位于`./Dashboard.js`文件中，你可以这样懒加载它：

```jsx
const Dashboard = React.lazy(() => import('./Dashboard'));
```
  
**结合 Suspense 使用**：

   - `React.lazy`通常与`React.Suspense`组件一起使用。`Suspense`用于*在懒加载的组件尚未加载完成时显示一个备用的 UI，比如加载指示器*（loading spinner）。
   - 以下是一个简单的示例，展示了它们如何一起工作：

```jsx
import React, { Suspense } from 'react';
const Dashboard = React.lazy(() => import('./Dashboard'));
function App() {
    return (
        <div>
            <Suspense fallback={<div>Loading...</div>}>
                <Dashboard />
            </Suspense>
        </div>
    );
}
```

在这个示例中，当`Dashboard`组件正在加载时，用户会看到一个显示 “Loading...” 的`div`。一旦`Dashboard`组件加载完成，`Suspense`就会渲染`Dashboard`组件。