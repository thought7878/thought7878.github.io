
`错误边界（Error Boundaries）` 是 React 16 引入的一项重要功能，用于**在组件树中捕获 JavaScript 错误，并防止整个应用崩溃**。它是 React *在 Fiber 架构下实现的容错机制之一*，尤其*适用于大型复杂项目*。

---

## 一、什么是错误边界？

> `错误边界`是*一个 React 组件*，它可以*捕获并处理其子组件树中任何位置抛出的 JavaScript 错误*，并*显示一个备用 UI*。

### 核心作用：
- 捕获渲染过程中的错误
- 显示 fallback UI（降级界面）
- 防止整个应用崩溃
- 支持日志记录和错误上报

---

## 二、错误边界能捕获哪些错误？

| 类型 | 是否可捕获 |
|------|------------|
| 渲染函数中抛出的错误 | ✅ 可以 |
| 生命周期方法中的错误 | ✅ 可以 |
| 子组件抛出的错误 | ✅ 可以 |
| 事件处理中的错误 | ❌ 不可以 |
| 异步代码中的错误（如 setTimeout） | ❌ 默认不可以（但可通过 try/catch 处理） |

---

## 三、如何创建一个错误边界组件？

你需要定义一个类组件，并实现以下两个生命周期方法之一或全部：

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新状态，触发 fallback UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 可在此处记录错误信息（如发送到服务器）
    console.error("Caught an error:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

### 使用方式：
```jsx
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>
```

---

## 四、错误边界的限制与注意事项

| 限制                | 说明                                 |
| ----------------- | ---------------------------------- |
| **只能是类组件**        | **目前不能用函数组件直接实现错误边界**              |
| 不能捕获异步错误          | 如 `setTimeout` 中的错误需手动包裹 try/catch |
| 不能捕获自身错误          | 如果错误边界本身抛出错误，无法自我修复                |
| 不支持服务端渲染（SSR）自动捕获 | 需要在服务端也做错误处理                       |
| 不会捕获事件处理器中的错误     | 如 `onClick` 中的错误不会被捕获              |

---

## 五、错误边界 vs 全局异常处理

| 对比项 | 错误边界 | window.onerror / Sentry 等全局错误监听器 |
|--------|-----------|----------------------------------------|
| 捕获范围 | 子组件树中的渲染错误 | 整个页面的 JS 错误 |
| 是否能显示 fallback UI | ✅ 可以 | ❌ 不可以 |
| 是否能控制组件树更新 | ✅ 可以 | ❌ 不可以 |
| 是否支持 React 特性 | ✅ 完全集成 | ⚠️ 需额外配置 |
| 是否推荐使用 | ✅ 推荐用于组件级别的错误处理 | ✅ 推荐用于全局监控和日志记录 |

---

## 六、错误边界的应用场景

| 场景 | 应用方式 |
|------|----------|
| **组件库开发** | 封装第三方组件，防止崩溃 |
| **页面级容错** | 包裹整个页面内容，展示“出错了”页面 |
| **模块级保护** | 包裹可能出错的模块（如用户头像、评论区） |
| **错误日志上报** | 在 `componentDidCatch` 中上报错误堆栈 |
| **恢复按钮** | 提供“重试”按钮重新加载组件 |

---

## 七、错误边界 + Suspense 的组合使用（React 18+）

你可以将 `ErrorBoundary` 和 `Suspense` 结合使用，来实现更优雅的错误和加载体验：

```jsx
<ErrorBoundary>
  <Suspense fallback="Loading...">
    <DataFetchingComponent />
  </Suspense>
</ErrorBoundary>
```

> ✅ 这样可以同时处理加载状态和错误状态。

---

## 八、错误边界在 React 生态中的发展

| 工具/框架 | 是否支持错误边界 |
|-----------|------------------|
| **React 16+** | ✅ 原生支持 |
| **Next.js** | ✅ 支持页面级错误边界 |
| **Gatsby** | ✅ 支持 |
| **React Native** | ✅ 支持 |
| **Vue 3** | ❌ 无类似机制，但可用 `onErrorCaptured` 替代 |
| **Angular** | ❌ 无类似结构，但可用 ErrorHandler 替代 |

---

## 九、错误边界与 Fiber 架构的关系

错误边界是 React **Fiber 架构**的重要组成部分。Fiber 架构允许 React 更细粒度地控制组件树的更新流程，而错误边界正是利用这一特性，在某个子树发生错误时，**只替换该部分 UI，而不是整棵树崩溃**。

---

## 十、最佳实践建议

| 实践 | 描述 |
|------|------|
| **不要过度使用** | 通常只需包裹关键区域即可 |
| **提供有用信息** | fallback UI 中应提示用户问题或提供“重试”按钮 |
| **结合日志系统** | 在 `componentDidCatch` 中上报错误信息 |
| **避免嵌套过多** | 多层错误边界可能导致调试困难 |
| **测试你的错误边界** | 主动模拟错误，确保 fallback 正常显示 |

---

## 十一、常见错误边界模板（可复用）

```jsx
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, info) {
    console.error('Error caught by boundary:', error, info.componentStack);
    // 发送到日志服务器
    // logErrorToService(error, info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div style={{ padding: '20px' }}>
          <h2>Something went wrong.</h2>
          <details>
            <summary>Error Details</summary>
            <pre>{this.state.error?.toString()}</pre>
          </details>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

---

## 十二、总结卡片（Summary Flashcard）

| 项目 | 内容 |
|------|------|
| 引入版本 | React 16 |
| 功能 | 捕获子组件树的 JS 错误 |
| 适用对象 | 类组件 |
| 能捕获的错误类型 | 渲染函数、生命周期方法中的错误 |
| 不能捕获的错误类型 | 异步错误、事件处理器错误 |
| 是否支持 SSR | ✅ 支持（需配合 getServerSideProps） |
| 是否推荐使用 | ✅ 推荐用于大型项目 |
| 是否支持 fallback UI | ✅ 支持 |
| 是否能阻止应用崩溃 | ✅ 可以 |

---

如果你想让我为你生成以下内容，请告诉我：

✅ PDF 版《React 错误边界详解》  
✅ 可复用的 ErrorBoundary 组件模板（含样式）  
✅ 如何用 TypeScript 编写类型安全的错误边界？  
✅ 错误边界 + Suspense 的组合模板  

是否还想了解：
- Vue 3 中的错误捕获机制？
- 如何在 Next.js 中使用错误边界？
- 如何编写单元测试验证错误边界行为？
