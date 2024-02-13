## State

In React, data that changes over time is called **state**



React 是一个用于构建用户界面的 JavaScript 库，它有以下几个主要的知识点：

- **组件**：React 通过组件的方式来组织和复用 UI，组件可以是函数或者类，它们接收一些输入（props）并返回一些输出（JSX）。
- **JSX**：JSX 是一种 JavaScript 的扩展语法，它允许你在代码中直接使用 HTML 标签和自定义的组件标签，它会被编译成 React.createElement 的调用。
- **Hook**：Hook 是一种特殊的函数，它可以让你在函数组件中使用状态（state）和副作用（effect），以及其他 React 的特性。常用的 Hook 有 useState、useEffect、useRef、useContext 等。
- **状态**：状态是一种组件内部的数据，它可以决定组件的渲染结果。你可以通过 useState Hook 来创建和更新状态，当状态发生变化时，组件会重新渲染。
- **副作用**：副作用是一种组件外部的操作，比如数据请求、事件监听、DOM 操作等。你可以通过 useEffect Hook 来执行和清理副作用，你还可以指定一个依赖项数组来控制副作用的执行时机。
- **事件处理**：事件处理是一种响应用户操作的方式，比如点击、输入、滚动等。你可以在 JSX 中使用 on 开头的属性来绑定事件处理函数，事件对象会作为参数传递给函数。
- **条件渲染**：条件渲染是一种根据条件来显示或隐藏组件的方式，比如 if、三元运算符、逻辑与等。你可以在 JSX 中使用 JavaScript 表达式来实现条件渲染。
- **列表渲染**：列表渲染是一种根据数组数据来生成多个组件的方式，比如 map、forEach 等。你可以在 JSX 中使用 JavaScript 表达式来实现列表渲染，你还需要给每个组件指定一个唯一的 key 属性来帮助 React 识别和优化渲染。
- **表单**：表单是一种收集和提交用户输入的方式，比如 input、textarea、select 等。你可以使用受控组件或非受控组件来处理表单，受控组件的值由状态控制，非受控组件的值由 DOM 节点控制。
- **组件间通信**：组件间通信是一种在不同组件之间传递数据和函数的方式，比如 props、context、自定义事件等。你可以使用 props 来实现父子组件间的通信，使用 context 来实现跨层级的通信，使用自定义事件来实现兄弟组件间的通信。
- **生命周期**：生命周期是一种描述组件在不同阶段所执行的操作的方式，比如挂载、更新、卸载等。你可以使用 useEffect Hook 来模拟类组件中的生命周期方法，比如 componentDidMount、componentDidUpdate、componentWillUnmount 等。
- **高阶组件**：高阶组件是一种接收一个组件并返回一个新组件的函数，它可以用来实现组件的复用和增强，比如 Redux 的 connect、React Router 的 withRouter 等。
- **自定义 Hook**：自定义 Hook 是一种封装和复用 Hook 逻辑的方式，它是一个以 use 开头的函数，它可以调用其他的 Hook，并返回一些值或函数。你可以根据自己的需求来创建自定义 Hook，比如 useFetch、useDebounce、useLocalStorage 等。
- **性能优化**：性能优化是一种提高组件渲染效率和用户体验的方式，比如 useMemo、useCallback、React.memo、PureComponent 等。你可以使用这些工具来避免不必要的渲染，提升性能。
- **错误边界**：错误边界是一种捕获和处理组件内部错误的方式，它是一个类组件，它可以实现 static getDerivedStateFromError 和 componentDidCatch 这两个生命周期方法来处理错误。你可以在组件树的任何位置添加错误边界，它可以防止错误导致整个应用崩溃。

以上就是我对 React 的一些知识点的概述，你可以参考以下的网站或书籍来深入学习 React：

- [React 官方文档](https://www.kagoya.jp/howto/it-glossary/develop/react/)：这是 React 的官方文档，它包含了 React 的基本概念、教程、API 参考、高级指南等内容，是学习 React 的最佳资料。
- [React 入门 - 学习 Web 开发](https://developer.mozilla.org/zh-CN/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started)：这是 MDN 上的一篇 React 入门教程，它介绍了 React 的基本用法、组件、状态、事件、列表、表单等内容，适合初学者阅读。
- [react知识点梳理](https://juejin.cn/post/7020384564862255117)：这是一篇掘金上的文章，它梳理了 React 的一些核心知识点，包括原理、运行过程、常见对象、常用 Hook、性能优化等内容，适合有一定基础的读者阅读。
