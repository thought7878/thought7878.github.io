## State

In React, data that changes over time is called **state**

# React

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
- [react 知识点梳理](https://juejin.cn/post/7020384564862255117)：这是一篇掘金上的文章，它梳理了 React 的一些核心知识点，包括原理、运行过程、常见对象、常用 Hook、性能优化等内容，适合有一定基础的读者阅读。

# Redux

Redux 是一个用于管理和更新应用状态的模式和工具库，它可以让你以一种可预测和可测试的方式来编写复杂的应用程序。😊

Redux 的知识点主要包括以下几个方面：

- **Store**：Store 是 Redux 中用来存储应用状态的对象，它提供了 getState、dispatch 和 subscribe 等方法来获取、更新和监听状态的变化。
- **Action**：Action 是一个描述发生了什么事情的普通对象，它至少有一个 type 字段来表示动作的类型，还可以有其他的字段来携带一些额外的信息。
- **Reducer**：Reducer 是一个纯函数，它接收当前的状态和一个 action，然后根据 action 的类型来返回一个新的状态。Reducer 必须遵循不可变性的原则，即不能直接修改原来的状态，而是返回一个新的状态对象。
- **Middleware**：Middleware 是一种扩展 Redux 功能的方式，它可以在每个 action 被发送到 reducer 之前或之后执行一些操作，比如打印日志、处理异步请求、缓存数据等。
- **React-Redux**：React-Redux 是一个官方的库，它可以让 React 组件和 Redux Store 连接起来，通过 Provider 组件来提供 Store，通过 connect 函数或 useSelector 和 useDispatch Hooks 来访问和更新状态。
- **Redux Toolkit**：Redux Toolkit 是一个官方推荐的工具集，它可以简化 Redux 的开发过程，提供了一些实用的函数和中间件，比如 configureStore、createAction、createReducer、createSlice、createAsyncThunk 等。

以上就是我对 Redux 的一些知识点的概述，你可以参考以下的网站或书籍来深入学习 Redux：

- [Redux 官方文档](https://redux.js.org/)：这是 Redux 的官方文档，它包含了 Redux 的基本概念、教程、API 参考、常见问题、最佳实践等内容，是学习 Redux 的最佳资料。
- [Redux 基础教程，第一节：Redux 概述和概念](https://cn.redux.js.org/tutorials/essentials/part-1-overview-concepts/)：这是 Redux 中文官网上的一篇教程，它介绍了 Redux 的背景、目的、术语和数据流，适合有一定基础的读者阅读。

# React Router

React Router 是一个用于实现 React 应用中的客户端路由的库，它可以让你根据不同的 URL 来显示不同的组件，从而创建多页面的应用。😊

React Router 的知识点主要包括以下几个方面：

- **Router**：Router 是一个组件，它可以提供路由的基本功能，比如监听 URL 的变化，匹配路由，渲染组件等。React Router 提供了不同类型的 Router，比如 BrowserRouter、HashRouter、MemoryRouter 等，它们适用于不同的场景和平台。
- **Route**：Route 是一个组件，它可以根据当前的 URL 来决定是否渲染某个组件。它接收一些属性，比如 path、exact、sensitive、element 等，来控制路由的匹配和渲染逻辑。
- **Link**：Link 是一个组件，它可以让你在应用中创建可点击的链接，从而导航到不同的页面。它接收一个 to 属性，来指定要跳转的 URL，还可以接收一些其他的属性，比如 replace、state、className 等，来控制链接的行为和样式。
- **NavLink**：NavLink 是一个特殊的 Link 组件，它可以根据当前的 URL 来给链接添加激活状态的样式。它接收一些属性，比如 activeClassName、activeStyle、activeClassName 等，来控制激活状态的样式。
- **Switch**：Switch 是一个组件，它可以让你在多个 Route 之间选择一个来渲染，从而避免多个 Route 同时匹配的情况。它会渲染第一个匹配的 Route，如果没有匹配的 Route，它会渲染一个默认的组件，比如一个 404 页面。
- **Redirect**：Redirect 是一个组件，它可以让你在路由中重定向到另一个 URL。它接收一个 to 属性，来指定要重定向的 URL，还可以接收一个 from 属性，来指定要匹配的路径。
- **useParams**：useParams 是一个 Hook，它可以让你在组件中获取路由参数的值。路由参数是指在路径中使用冒号（:）来定义的变量，比如 /users/:id，它可以匹配任意的值，并将其作为参数传递给组件。
- **useLocation**：useLocation 是一个 Hook，它可以让你在组件中获取当前的 URL 信息。它返回一个 location 对象，包含了 pathname、search、hash、state 等属性，来描述当前的 URL。
- **useHistory**：useHistory 是一个 Hook，它可以让你在组件中访问和操作浏览器的历史记录。它返回一个 history 对象，提供了一些方法，比如 push、replace、go、goBack、goForward 等，来控制导航的行为。
- **useNavigate**：useNavigate 是一个 Hook，它可以让你在组件中导航到不同的 URL。它返回一个 navigate 函数，接收一个 to 参数，来指定要导航的 URL，还可以接收一些其他的参数，比如 replace、state 等，来控制导航的行为。

以上就是我对 React Router 的一些知识点的概述，你可以参考以下的网站或书籍来深入学习 React Router：

- [React Router 官方文档](https://reactrouter.com/en/main/start/tutorial)：这是 React Router 的官方文档，它包含了 React Router 的基本概念、教程、API 参考、示例、常见问题等内容，是学习 React Router 的最佳资料。
- [A Complete Beginner’s Guide to React Router (Including Router Hooks)](https://www.freecodecamp.org/news/a-complete-beginners-guide-to-react-router-include-router-hooks/)：这是一篇在 freeCodeCamp 上的文章，它用例子和代码来介绍 React Router 的基本用法、组件、Hook、参数、重定向、路由保护等内容，适合初学者阅读。
- [A Complete Guide to React Router: Everything You Need to Know](https://ui.dev/react-router-tutorial)：这是一篇在 ui.dev 上的文章，它用深入浅出的方式来解释 React Router 的工作原理和核心概念，包括路由、导航、匹配、渲染、嵌套、动画等内容，适合有一定基础的读者阅读。

# Next.js

Next.js 是一个用于构建 React 应用的框架，它提供了很多内置的功能，比如：

- **路由**：Next.js 通过文件系统来实现基于页面的路由，你可以在 pages 目录下创建 .js、.jsx、.ts 或 .tsx 文件来定义路由，也可以使用动态路由参数来匹配任意的 URL。
- **渲染**：Next.js 支持两种渲染模式，静态生成（SSG）和服务器端渲染（SSR），你可以在每个页面中通过导出 getStaticProps 或 getServerSideProps 函数来选择渲染模式，也可以使用 getStaticPaths 函数来生成动态路由的静态页面。
- **增量静态生成**：Next.js 允许你在构建后以增量的方式生成和更新静态页面，你可以在 getStaticProps 函数中返回一个 revalidate 属性来指定页面的重新生成间隔，这样可以保证页面的数据始终是最新的。
- **TypeScript**：Next.js 支持 TypeScript，你可以直接使用 .ts 或 .tsx 文件来编写组件，Next.js 会自动配置和编译 TypeScript。
- **快速刷新**：Next.js 提供了快速刷新的功能，它可以在你修改代码后实时更新页面，而不会丢失组件的状态，这样可以提高开发效率和体验。
- **API 路由**：Next.js 允许你在 pages/api 目录下创建 API 路由，它们是一些可以处理 HTTP 请求的函数，你可以在这些函数中实现后端的逻辑，比如连接数据库、验证身份、发送邮件等。
- **CSS 支持**：Next.js 支持 CSS 模块，你可以在组件中导入 .css 或 .scss 文件来定义样式，Next.js 会自动为每个组件生成唯一的类名，以避免样式冲突。你也可以在 pages/\_app.js 文件中导入全局的样式文件，或者使用 styled-components 等第三方库来创建样式。

以上就是我对 Next.js 的一些知识点的概述，你可以参考以下的网站或书籍来深入学习 Next.js：

- [Next.js 官方文档](https://www.nextjs.cn/)：这是 Next.js 的官方文档，它包含了 Next.js 的基本概念、教程、API 参考、示例、常见问题等内容，是学习 Next.js 的最佳资料。
- [快速入门 | Next.js | Next.js 中文网](https://www.nextjs.cn/docs/getting-started)：这是 Next.js 中文网上的一篇快速入门教程，它介绍了 Next.js 的安装、设置、路由、渲染、CSS 支持等内容，适合初学者阅读。
- [Create a Next.js App | Learn Next.js](https://nextjs.org/learn-pages-router/basics/create-nextjs-app)：这是 Next.js 官网上的一个互动课程，它通过一些小测验和项目来帮助你学习 Next.js 的用法，包括路由、数据获取、API 路由、部署等内容，适合有一定基础的读者阅读。

# 高级前端

高级的前端工程师是指具有丰富的前端开发经验和能力的工程师，他们不仅要掌握基础的 HTML、CSS 和 JavaScript，还要掌握以下一些技能：

- **框架和库**：高级的前端工程师应该熟练使用至少一种主流的前端框架或库，比如 React、Vue、Angular 等，以及它们相关的生态系统，比如 Redux、Vuex、React Router 等，来构建复杂的用户界面和交互。
- **工程化和自动化**：高级的前端工程师应该能够使用各种工具和技术，来提高开发效率和代码质量，比如 Webpack、Babel、ESLint、Prettier 等，来编译、打包、检查、格式化代码，以及使用 Git、CI/CD、Docker 等，来管理版本、部署、容器化应用。
- **性能优化和安全**：高级的前端工程师应该能够优化网页的加载速度和用户体验，比如使用 CDN、缓存、压缩、代码分割、懒加载等技术，来减少请求次数和大小，以及使用 SSR、预渲染、骨架屏、图片优化等技术，来减少白屏时间和卡顿感。同时，高级的前端工程师还应该能够保证网页的安全性，比如使用 HTTPS、CSP、XSS、CSRF 等技术，来防止网络攻击和数据泄露。
- **测试和调试**：高级的前端工程师应该能够使用各种测试和调试工具，来保证代码的正确性和健壮性，比如使用 Jest、Mocha、Enzyme 等，来进行单元测试、集成测试、端到端测试等，以及使用 Chrome DevTools、VS Code、Sentry 等，来进行代码分析、性能测试、错误追踪等。
- **新技术和趋势**：高级的前端工程师应该能够关注和学习前端领域的新技术和趋势，比如 TypeScript、GraphQL、WebAssembly、PWA、Serverless 等，以及能够评估和应用这些技术，来提升自己的技术水平和竞争力。

以上就是我对高级的前端工程师应该掌握的一些技能的总结，你可以参考以下的网站或书籍来深入学习这些技能：

- [前端开发者手册](https://github.com/webman158/webman158.github.io/blob/master/index.html)：这是一本由 Cody Lindley 编写的书籍，它介绍了前端开发的基础知识、工具、框架、库、模式、实践等内容，适合前端开发者阅读。
- [高级前端开发工程师面试题]：这是一篇在掘金上的文章，它收集了一些高级前端开发工程师常见的面试题，涉及到 HTML、CSS、JavaScript、框架、工程化、性能优化、安全等方面的内容，适合前端开发者复习。
- [高级前端工程师必备的 8 大技能]：这是一篇在 InfoQ 上的文章，它总结了高级前端工程师必备的八大技能，包括数据结构与算法、设计模式、网络协议、浏览器原理、前端框架、前端工程化、前端性能优化、前端安全等内容，适合前端开发者学习。

# 美国前端的收入

## 美国的高级前端工程师的年收入是多少？

这个问题没有一个确定的答案，因为美国的高级前端工程师的年收入可能会受到很多因素的影响，比如地区、公司、经验、技能、教育等。不过，根据一些网站的数据，我们可以得到一些大致的范围和平均值，比如：

- [Glassdoor]：根据 Glassdoor 的数据，美国的高级前端工程师的年收入的中位数是  **$120,000**，最低是  **$77,000**，最高是  **$168,000**。
- [Indeed]：根据 Indeed 的数据，美国的高级前端工程师的年收入的平均值是  **$122,321**，最低是  **$30,000**，最高是  **$250,000**。
- [ZipRecruiter]：根据 ZipRecruiter 的数据，美国的高级前端工程师的年收入的平均值是  **$124,537**，最低是  **$71,500**，最高是  **$176,500**。

## 美国的中级的前端工程师的年收入是多少？

这个问题没有一个确定的答案，因为美国的中级的前端工程师的年收入可能会受到很多因素的影响，比如地区、公司、经验、技能、教育等。不过，根据一些网站的数据，我们可以得到一些大致的范围和平均值，比如：

- Glassdoor：根据 Glassdoor 的数据，美国的中级的前端工程师的年收入的中位数是  **88,000**，最低是 **56,000**，最高是  **$121,000**。
- Indeed：根据 Indeed 的数据，美国的中级的前端工程师的年收入的平均值是  **$89,798**，最低是  **$30,000**，最高是  **$200,000**。
- ZipRecruiter：根据 ZipRecruiter 的数据，美国的中级的前端工程师的年收入的平均值是  **$91,409**，最低是**$55,000**，最高是  **$136,500**。
