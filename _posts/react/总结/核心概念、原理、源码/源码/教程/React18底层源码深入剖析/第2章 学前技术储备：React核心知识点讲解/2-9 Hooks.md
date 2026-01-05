本视频讲述了React中*Hooks的概念、引入版本及解决的核心问题*，并列举了*常用Hooks API及其用途*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/f043f7fb8dfe7a91e4db8f9c1127eae4_MD5.webp]]


### Hooks简介与版本要求 
[00:03](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=3)

- **引入版本**  
    React Hooks 是在 `React 16.8 版本`中正式引入的新特性。
- 版本判断依据  
    若项目 React 版本大于等于 16.8，可以使用 Hooks；否则不可用。

### Hooks的背景与作用 
[00:30](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=30)

- **函数组件状态管理**限制  
    *在 Hooks 出现之前，函数组件无法在内部定义状态值*（state）。
- 类组件与函数组件对比  
    *类组件通过实例保存状态*，而函数组件每次调用都会重新执行，状态难以持久化。
- Hooks解决方案  
    React 引入如 `useState`、`useReducer` 等 API，*将状态值绑定到 Fiber Node 上*，实现函数组件的状态管理。

### Hooks带来的变革与优势 
[01:43](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=103)

- 函数组件可完全替代类组件  
    Hooks 让函数组件具备状态管理、副作用处理等能力。
- Hooks并非凭空出现  
    它为了解决类组件存在的若干问题而设计。

### Hooks解决的问题一：状态逻辑复用困难 
[02:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=136)

- 传统方式：HOC 与 Render Props
    - 高阶组件（HOC）如 `connect`、`withRouter`、`createForm`广泛用于状态逻辑复用。
- 存在问题：嵌套地狱（Callback Hell）
    - 如同时使用 `createForm` 和 `connect`，造成四层括号嵌套，不易维护。
- Hooks解决方案：自定义 Hook
    - 替代 HOC，如 `useForm` 替代 `createForm`，`useDispatch`、`useSelector`替代 `connect`。
    - 不修改组件结构即可复用状态逻辑。

### Hooks解决的问题二：复杂组件难以维护 
[04:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=272)

- 生命周期合并问题  
    类组件中一个生命周期方法需包含多个不相关逻辑，维护困难。
- Hooks拆分逻辑的能力
    - 使用多个 `useEffect` 拆分副作用逻辑。
    - 可进一步封装至自定义 Hook 中，提高复用性与清晰度。

### Hooks解决的问题三：类组件理解门槛高 
[05:11](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=311)

- 初学者常见问题：this 的指向问题
    - 类组件中频繁遇到 `this` 丢失问题，需手动绑定或使用箭头函数。
- 对机器和开发者均友好
    - 函数是 JavaScript 的一等公民，不存在兼容性问题。
    - 更易理解和编写，降低学习成本。

### 常用 Hooks API 列举 
[06:28](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=388)

- 状态管理
    - `useState`：用于定义组件状态。
    - `useReducer`：适用于复杂状态逻辑。
- 副作用处理
    - `useEffect`：处理副作用，如数据获取、订阅等。
    - `useLayoutEffect`：同步执行副作用，在 DOM 更新后立即执行。
    - `useInsertionEffect`：用于 CSS-in-JS 性能优化。
- 上下文消费
    - `useContext`：替代 `<Consumer>` 组件，更简洁地访问 Context。
- 性能优化
    - `useCallback`：缓存回调函数。
    - `useMemo`：缓存计算结果。
- 引用与变量保持
    - `useRef`：创建可变引用对象，常用于访问 DOM 或保存状态。
- 并发模式支持
    - `useTransition`：标记某些更新为非紧急更新。
    - `useDeferredValue`：延迟响应状态变化。
- 唯一标识生成
    - `useId`：生成可用于 HTML 元素的唯一 ID。
- 外部状态同步
    - `useSyncExternalStore`：用于同步外部存储的状态，如 Redux、MobX 等状态管理库。

### 总结与回顾 
[08:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=481)

- Hooks是什么？  
    一组用于在函数组件中使用状态和其他 React 特性的 API。
- Hooks解决了哪些问题？  
    状态逻辑复用难、复杂组件维护难、类组件理解难等问题。
- 有哪些常用 Hooks API？  
    包括状态管理、副作用处理、上下文消费、性能优化、并发控制等多个方面。