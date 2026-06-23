# 原理、源码
[[React 的原理]]、[[React 的原理2]]、[[React 的原理1]]、[[1. JSX 、createElement]]

# CLI Tools

## Vite

# Component
参考：[[Component]]

## React Server Component
参考：[[RSC-QW1]]

## Functional Component
参考：[[函数组件]]
本质是返回 JSX 的函数。结合 Hooks 拥有了状态和生命周期能力。
### JSX
参考：[[_posts/react/总结/Components/JSX]]
### Props vs State
参考：[[Props & State]]

### 条件渲染
Conditional Rendering
参考：[[Conditional Rendering]]
### 组合和继承
Composition vs Inheritance
参考：[[Composition & Inheritance]]

# Rendering
参考：[[_posts/react/总结/Render/Render]]
## Component Lifecycle 
参考：[[生命周期]]
## List and Key
参考：[[Key]]
## Render Props
参考：[[Render Props]]
## Ref
参考：[[Ref-DS]]、[[Ref-豆包]]
## Event
参考：[[Event]]
## High Order Components
参考：[[High Order Components]]


# Hooks
Hooks 让函数组件拥有了状态和副作用管理能力。**React 19 进一步优化了 Hooks 的使用体验。**

## useState
声明状态。更新是**异步且批处理**的。支持传入函数进行惰性初始化，支持函数式更新 `setCount(c => c + 1)`。

参考：[[useState-DS]]

## useEffect
处理副作用（数据请求、DOM 操作、订阅）。
- **依赖数组**：`[]` 仅在挂载时执行；`[dep]` 依赖变化时执行；不传则每次渲染执行。
- **清理函数 (Cleanup)**：用于取消订阅、清除定时器，防止内存泄漏。

## useCallback
缓存函数引用，避免子组件不必要的重渲染。（_注：React 19 的 Compiler 已逐渐让手动使用它们成为历史_）。

## useMemo
缓存计算结果，避免子组件不必要的重渲染。（_注：React 19 的 Compiler 已逐渐让手动使用它们成为历史_）。

## useReducer
类似 Redux，适合管理包含多个子值的复杂状态逻辑。

## useRef
1. 获取 DOM 节点引用。
2. 保存跨渲染周期的**可变变量**（修改 `.current` **不会**触发重新渲染）。

## useContext
订阅 Context，实现跨层级数据传递。

## useLayoutEffect
在 DOM 变更之后、浏览器绘制之前**同步**执行。用于需要测量 DOM 布局的场景（避免闪烁）。

## React 19 新增/增强 Hooks
【react19 进阶学习】 https://www.bilibili.com/video/BV155cizCEEf/?p=2&share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862

- `useActionState` (原 `useFormState`)：处理表单提交状态，自动管理 pending 状态和错误返回。
- **`useFormStatus`**：在嵌套的子组件中获取父级 `<form>` 的提交状态（如 `pending`），无需通过 props 传递。
- **`useOptimistic`**：实现**乐观更新**（在服务器响应前先更新 UI，失败则回滚），极大提升用户体验。
- **`use()`**：全新的 API，可以在**渲染期间**读取 Promise（替代部分 `useEffect` 数据请求）或动态读取 Context。

## Custom Hooks


## Hooks Best Practices

# 组件通信

## 父 -> 子
通过 `props` 传递数据或回调函数。

## 子 -> 父
子组件调用父组件通过 `props` 传入的回调函数。

## 兄弟组件
状态提升（Lifting State Up）到共同的父组件。

## 跨层级
Context API、全局状态管理。

## Ref 穿透
React 19 中，函数组件**直接接收 `ref` 作为 prop**，彻底废弃了 `forwardRef` 和 `useImperativeHandle` 的繁琐写法。


# 高级特性 
Advanced Features

## Context API
解决 Props Drilling（属性钻取）问题。但要注意，Context 值改变会导致所有消费组件重渲染，需配合 `useMemo` 或拆分 Context 优化。

## Portals (`createPortal`)
将子节点渲染到 DOM 树之外（如 `document.body`），常用于 Modal、Tooltip、全局通知，**不破坏 React 组件树的事件冒泡机制**。

## Error Boundaries (错误边界)
目前**仍需使用类组件**（`componentDidCatch` / `getDerivedStateFromError`）来*捕获子组件树中的 JS 错误，防止整个应用白屏*。

## Suspense & Lazy
- `React.lazy` 实现组件级别的代码分割（按需加载）。
- `Suspense` 提供 `fallback` UI，等待异步操作（懒加载组件、数据请求）完成。
参考：、[[Suspense 2]]、[[11、深入理解 Suspense 和 ErrorBoundary]]、[[41.v18特性篇-Suspense]]

## flushSync
参考：[[flushSync]]


# 性能优化
随着 React 19 的发布，性能优化理念发生了**范式转移**：

## 1. 传统优化手段 (React 18 及以前)

- `React.memo`：浅比较 props，阻止函数组件无效渲染。
- `useMemo` / `useCallback`：稳定对象/函数引用，防止破坏子组件的 `memo`。
- 状态下放：将状态尽量放在靠近使用它的组件中，缩小重渲染范围。
- 虚拟列表：使用 `react-window` 等库处理十万级列表渲染。

## 2. 现代优化手段 (React 19+ 革命)

- **React Compiler (原 React Forget)**：
    - **自动记忆化**：编译器在构建时自动分析代码，自动插入等效于 `memo`、`useMemo`、`useCallback` 的逻辑。
    - **告别心智负担**：开发者不再需要手动编写依赖数组，不再需要担心闭包陷阱和引用不稳定问题，**代码编写回归原生 JavaScript 直觉**。
- **并发特性 (Concurrent Features)**：
    - `useTransition` / `startTransition`：将耗时的状态更新标记为“非紧急过渡”，保持 UI 响应（如输入框搜索过滤大列表）。
    - `useDeferredValue`：延迟更新非关键部分的 UI。


# Routers

## 为什么使用路由？
参考：[[为什么使用路由？]]

## React Router
现代  React Router v6/v7 已经深度吸收了 Remix 的“数据路由”理念：

- **基础路由**：`BrowserRouter`, `Routes`, `Route`, `Link`, `NavLink`。
- **核心 Hooks**：`useNavigate` (编程式导航), `useParams` (路径参数), `useLocation`, `useSearchParams` (查询参数)。
- **嵌套路由**：使用 `<Outlet />` 渲染子路由。
- **数据路由 (Data Routing)**：
    - `loader`：在路由渲染前并行获取数据，消灭“瀑布流”请求和 Loading 闪烁。
    - `action`：处理表单提交和数据变更。
    - `useFetcher`：在不改变当前 URL 的情况下与后端交互。

参考：[[React Router-DeepSeek]]、[[React Router]]


## TanStack Router
参考：[[TanStack Router]]



# 状态管理
State Management

## 为什么使用状态管理库？
参考：[[为什么使用状态管理库？]]

## 复杂状态管理与数据流设计
参考：[[复杂状态管理与数据流设计]]

## 客户端状态
### Zustand
目前**最流行**的轻量级状态库，基于 Hooks，无 Provider 嵌套，API 极简。

参考：[[Zustand]]、[[Zustand-DeepSeek]]、[[Zustand-豆包]]

### Jotai / Recoil
原子化（Atomic）状态管理，适合细粒度更新，避免全局状态改变导致的全局重渲染。

### MobX

### Context API

### Redux Toolkit (RTK)
适合超大型、业务逻辑极度复杂的企业级应用，严谨且配套完善（RTK Query）。

参考：[[Redux Toolkit (RTK)]]、[[Redux-DS]] 、[[原理-DB]]

【【React前端高频面试】（说一下Redux的核心思想）不能只背出单一数据源、状态只读、使用纯函数。带你从Redux如何通过状态快照序列， 实现完美的状态追溯】 https://www.bilibili.com/video/BV1RK47zxEWV/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862
【什么是Redux】 https://www.bilibili.com/video/BV1Q9Ge67EPU/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862

## 服务端状态

### TanStack Query / SWR
参考：[[_posts/react/状态管理库/TanStack Query/TanStack Query|TanStack Query]]

#### 为什么需要 TanStack Query？
参考：[[_posts/react/状态管理库/TanStack Query/为什么需要 TanStack Query？|为什么需要 TanStack Query？]]


**TanStack Query (React Query) / SWR**：**服务端状态管理**的绝对霸主。用于处理数据请求、缓存、轮询、重试、离线同步，将“服务端状态”与“客户端UI状态”彻底分离。

# Styling
**CSS 方案**：
- **Tailwind CSS**：原子化 CSS，目前的**绝对主流**，配合 RSC 完美契合。
- **CSS Modules**：传统且稳妥的局部作用域方案。
- **CSS-in-JS (Styled-components / Emotion)**：在 RSC 环境下支持较差（因为需要运行时），正逐渐被零运行时的方案（如 Vanilla Extract, Panda CSS）替代。

## Tailwind CSS

## Panda CSS

## CSS Modules



# Component Libraries
- **Shadcn UI**：不是传统的 npm 包，而是提供源码直接复制到项目中，基于 Tailwind + Radix UI，高度可定制，**目前最火爆**。
- **Ant Design / MUI**：适合中后台管理系统。

## Shadcn UI

## Material UI

## Chakra UI


# Headless Component Libraries
## Radix UI

## React Aria

## Ark UI


# API Calls
APIs, short for Application Programming Interfaces, are software-to-software interfaces. Meaning, they allow different applications to talk to each other and exchange information or functionality. This allows businesses to access another business’s data, piece of code, software, or services in order to extend the functionality of their own products — all while saving time and money. There are several options available to make API calls from your React.js applications.  
API，用于应用程序编程接口的缩写，*是软件对软件的接口*。意思是，**它们允许不同的应用程序互相交谈并交换信息或功能**。这使企业可以访问另一个企业的数据，代码，软件或服务，以扩展自己的产品的功能，同时节省时间和金钱。有几个选项可从您的react.js应用程序中进行API调用。

## GraphQL

### Apollo

### Relay

### urql

## Restful
### TanStack Query
参考：[[_posts/react/Restful/TanStack Query/TanStack Query]]
### Axios

### SWR

### rtk-query


# Testing
Vitest + React Testing Library (强调以用户行为而非组件内部状态进行测试)。

## React Testing Library

## Vitest

## Jest

## Playwright

## Cypress

# SSR Frameworks
## Next.js

## Astro

## react-router

# Forms
**表单校验**：**React Hook Form** + **Zod**（或 Yup）。基于非受控组件，性能极高，避免输入时全局重渲染。

## React hook form

## Formik

# Types & Validation
## TypeScript

## Zod


# Animation
## Framer Motion

## react spring

## GSock

# Error Boundaries

# Server、Server APIs

### 七、 React Server Components (RSC) 与元框架

在 2026 年，**纯客户端 SPA (单页应用) 已不再是大型项目的首选**，基于 Next.js / Remix 的 **RSC 架构**成为行业标准。

#### 1. 服务端组件 (Server Components)

- **零客户端 JS**：组件在服务端渲染为特殊的 JSON 格式（RSC Payload），不打包进客户端 Bundle，极大减小首屏体积。
- **直接访问后端**：可以直接 `await` 数据库查询或读取本地文件，无需写 API 接口。
- **限制**：不能使用 `useState`、`useEffect` 等 Hooks，不能绑定浏览器事件（如 `onClick`）。

#### 2. 客户端组件 (Client Components)

- 文件顶部需添加 `"use client"` 指令。
- 用于需要交互、状态管理、浏览器 API 的场景。
- **最佳实践**：将 `"use client"` 的边界尽量**向下推**（例如：整个页面是服务端组件，只有底部的“点赞按钮”是客户端组件）。

#### 3. 核心生态：Next.js (App Router)

- **SSR / SSG / ISR**：灵活的服务端渲染策略。
- **Server Actions**：允许客户端组件直接调用服务端函数（底层通过 RPC 实现），配合 `useActionState` 实现极简的全栈表单提交。
- **Streaming (流式渲染)**：配合 `Suspense`，服务端分块发送 HTML，实现首屏秒开。

# Suspense

# Portals

# Mobile Application
## React Native




