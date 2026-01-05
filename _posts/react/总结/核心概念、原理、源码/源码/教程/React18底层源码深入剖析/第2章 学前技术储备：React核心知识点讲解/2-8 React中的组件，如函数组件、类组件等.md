

本视频讲述了**React组件的分类体系**，包括React DOM组件、*类组件*（已归入Legacy API）、*函数组件、React内置组件*（如Fragment、Suspense、StrictMode等）、*Context的Provider/Consumer组件*、*高阶函数型组件*（如forwardRef、lazy、memo、createPortal），并**深入解析了React源码中对组件类型的区分机制及其对应的Fiber节点类型**。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/39d24582b44b4a79dca8657ac9a1ce8e_MD5.webp]]

## React 中的组件分类与源码解析

### React DOM 组件 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/bbf7b18765de16b949aad2109c12d6a9_MD5.webp]]

#### 核心概念

- 定义：React DOM 组件是指那些有对应 DOM 节点的组件，例如 `<div>`、`<input>`、`<img>`等。
- 特点：
    - 与真实 DOM 节点一一对应
    - 是 React 虚拟 DOM 的最终渲染目标
    - 通常用于构建 UI 的基础结构

#### 示例代码

```jsx
function Profile() {
  return (
    <img
      alt="Katherine Johnson"
    />
  );
}
```

#### 常见误区

- 函数组件是否有 DOM 节点？
    - 函数组件本身没有对应的 DOM 节点
    - 其子元素（如 `<img>`）才是真正的 DOM 节点
    - 在虚拟 DOM 树中，函数组件是其子节点的父节点
- 类组件是否有 DOM 节点？
    - 类组件本身也没有对应的 DOM 节点
    - 只有类组件的 `render()` 方法返回的元素才可能生成 DOM 节点

### 类组件 
[02:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=151)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/ce56daeef73fab86723a6abb73776f39_MD5.webp]]

#### 定义

- React 类组件是使用 ES6 类语法定义的组件，继承自 `React.Component` 或 `React.PureComponent`
- 示例代码：

```jsx
class Greeting extends Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

#### 特点

- 支持生命周期方法（如 `componentDidMount`、`componentDidUpdate`）
- 可以使用 `state` 管理内部状态
- 支持错误边界（Error Boundaries）

#### 使用建议

- 现状：在 React 18 中，类组件已被归类为 Legacy React APIs，不再推荐使用
- 适用场景：
    - 维护老旧项目时仍需使用
    - 某些特定功能（如错误边界）目前仍需使用类组件
- 新项目建议：优先使用函数组件 + Hooks

### 函数组件 
[02:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=151)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/b13127b38db08cb48c6808c12f49c8f4_MD5.webp]]

#### 定义

- React 函数组件是一个返回 JSX 的 JavaScript 函数
- 示例代码：

```jsx
function Profile() {
  return (
    <img
      alt="Katherine Johnson"
    />
  );
}
```

#### 特点

- 更简洁的语法
- 更容易测试和理解
- 支持 React Hooks（如 `useState`、`useEffect`）
- 更好的性能优化（如 `React.memo`）

#### 使用建议

- 推荐用于新项目
- 优势：
    - 更好的可组合性
    - 更少的样板代码
    - 更适合现代 React 开发模式（如并发模式）

### React 内置组件 
[04:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=258)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/c61acd8233b3487224d91062cb811217_MD5.webp]]

#### Fragment

- 用途：包裹多个 JSX 节点，但不添加额外 DOM 节点
- 两种写法：
    
    ```jsx
    <Fragment>...</Fragment>
    <>
      ...
    </>
    ```
    
- 注意：如果需要添加 `key`，必须使用 `<Fragment>` 显式写法

#### Profiler

- 用途：衡量组件树的渲染性能
- 示例：
    
    ```jsx
    <Profiler id="MyComponent" onRender={callback}>
      <MyComponent />
    </Profiler>
    ```
    

#### Suspense

- 用途：在子组件加载时显示后备方案（如加载状态）
- 常见用法：
    
    ```jsx
    <Suspense fallback={<LoadingSpinner />}>
      <MyLazyComponent />
    </Suspense>
    ```
    

#### StrictMode

- 用途：开启开发环境下的额外检查，帮助发现潜在问题
- 示例：
    
    ```jsx
    <StrictMode>
      <App />
    </StrictMode>
    ```
    

### Context 组件：Provider 与 Consumer 
[05:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=304)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/443481ac3c8ae0825bd33b0ad931b6f5_MD5.webp]]

#### 定义

- Context：用于跨层级传递数据，避免 props 逐层传递
- 三个核心步骤：
    1. 创建 Context 对象：`const MyContext = React.createContext(defaultValue);`
    2. 使用 `Provider` 分发值：`<MyContext.Provider value={value}>...</MyContext.Provider>`
    3. 使用 `Consumer` 消费值：`<MyContext.Consumer>{value => ...}</MyContext.Consumer>`

#### 示例代码

```jsx
const ThemeContext = React.createContext('light');
function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}
function Toolbar() {
  return (
    <ThemeContext.Consumer>
      {theme => <Button theme={theme} />}
    </ThemeContext.Consumer>
  );
}
```

#### 使用建议

- 函数组件：优先使用 `useContext` Hook
- 类组件：使用 `contextType` 或 `Consumer`

### 高级函数型组件：forwardRef、lazy、memo 与 createPortal 
[05:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=307)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/9c80139ea7cf50e9c0924de3daa6253b_MD5.webp]]

#### forwardRef

- 用途：允许父组件通过 `ref` 访问子组件的 DOM 节点
- 示例：
    
    ```jsx
    const MyInput = forwardRef((props, ref) => (
      <input ref={ref} {...props} />
    ));
    ```
    

#### lazy

- 用途：延迟加载组件，结合 `Suspense` 实现代码分割
- 示例：
    
    ```jsx
    const MarkdownPreview = lazy(() => import('./MarkdownPreview'));
    ```
    

#### memo

- 用途：优化性能，避免不必要的重新渲染
- 示例：
    
    ```jsx
    const MemoizedComponent = memo(SomeComponent);
    ```
    

#### createPortal

- 用途：将子节点渲染到 DOM 的不同位置
- 示例：
    
    ```jsx
    {createPortal(children, domNode)}
    ```
    

### React 源码中的组件分类机制与 Fiber 节点类型解析 
[08:02](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=482)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/e19e4b743c7265fcd3d7870c64f2a3e8_MD5.webp]]

#### Fiber 节点类型定义

```js
export const FunctionComponent = 0;
export const ClassComponent = 1;
export const IndeterminateComponent = 2;
export const HostRoot = 3;
export const HostPortal = 4;
export const HostContainer = 5;
export const HostText = 6;
export const Mode = 8;
export const ContextConsumer = 9;
export const ContextProvider = 10;
export const ForwardRef = 11;
export const Fragment = 12;
export const SuspenseElement = 13;
export const MemoComponent = 14;
export const SimpleMemoComponent = 15;
export const LazyComponent = 16;
export const IncompleteClassComponent = 17;
export const DehydratedFragment = 18;
export const ConcurrentModeList = 19;
export const ScopeComponent = 21;
export const OffscreenComponent = 22;
export const LegacyHiddenComponent = 23;
export const CacheComponent = 24;
export const TracingMarkerComponent = 25;
export const HostInstanceId = 26;
export const HostSingleton = 27;
```

#### 关键类型说明

- FunctionComponent (0)：函数组件
- ClassComponent (1)：类组件
- HostComponent (5)：原生 DOM 组件（如 `<div>`、`<span>`）
- HostText (6)：文本节点
- Fragment (12)：React.Fragment
- SuspenseElement (13)：Suspense 组件
- MemoComponent (14)：使用 `React.memo` 包裹的组件
- LazyComponent (16)：使用 `React.lazy` 加载的组件

#### 调试与源码分析

- 调试技巧：
    - 使用 React DevTools 查看组件树结构
    - 查看 `fiber.tag` 字段判断组件类型
    - 查看 `fiber.stateNode` 获取组件实例或 DOM 节点
- 源码分析：
    - React 源码中通过 `tag` 字段区分不同类型的组件
    - 每种组件类型对应不同的渲染逻辑
    - 例如：函数组件需要执行函数，类组件需要实例化并调用 `render()`

#### 总结

- 组件分类：React 中的组件分为六类：DOM 组件、类组件、函数组件、内置组件、Context 组件、函数型组件（如 `forwardRef`、`memo` 等）
- 源码机制：React 通过 `fiber.tag` 字段区分不同类型的组件，确保正确的渲染逻辑
- 调试建议：使用 React DevTools 结合源码理解组件树结构和渲染流程