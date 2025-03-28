# React 核心概念

## JSX
参考 [[JSX]]

- 语法扩展
- 嵌入表达式
- JSX 编译

## 组件
参考 [[Component]]

- 函数组件。参考 [[函数组件]]
- 类组件
- 组件生命周期。参考 [[生命周期]]
- 组件状态 (`state`)。参考 [[Props & State]]
- 组件属性 (`props`)。参考 [[Props & State]]

## 事件处理
参考 [[Event]]

- 事件绑定
- 合成事件
- 事件处理函数
- 事件对象

## 组合（Composition）和继承（Inheritance）
参考 [[Composition & Inheritance]]

## 渲染

### 条件渲染
参考 [[Conditional Rendering]]

- `if` 语句
- 三元运算符
- 逻辑与运算符 (`&&`)
- 或

### 列表渲染
- `map` 方法
- `key` 属性。参考 [[Key]]

### Render Props
参考 [[Render Props]]

## Refs
参考 [[Ref-DS]]、[[Ref-豆包]]

- `React.createRef`。参考 [[Ref-DS]]
- `useRef`。参考[[useRef-DS]]
- 转发 Refs (`React.forwardRef`)。参考 [[forwardRef-DS]]
- useImperativeHandle。参考 [[useImperativeHandle-DS]]


## 高阶组件 (HOC)
参考 [[High Order Components]]
- 组件复用
- 属性代理
- 反向继承


## 表单
参考 [[受控组件、非受控组件]]
- 受控组件
- 非受控组件
- 表单验证

## 状态管理
参考 [[Props & State]]

- `useState` Hook。参考 [[useState-DS]]
- `useReducer` Hook。参考 [[useReducer]]
- 状态提升。参考 [[状态提升]]

## 组件间的通信
### Props
参考 [[Props & State]]
### Context API
参考 [[_posts/react/总结/Context/Context|Context]]

- `React.createContext`
- `Provider`
- `Consumer`
- `useContext`

### 状态管理库

## Hooks

1. **内置 Hooks**
   - useState。参考：[[useState-DS]]、[[useState的原理]]
   - useEffect。参考：[[useEffect-QW]]、[[useEffect的原理]]
   - useContext
   - useReducer
   - useCallback
   - useMemo
   - useRef
   - useImperativeHandle
   - useLayoutEffect
   - useDebugValue
2. **自定义 Hooks**
   - 自定义 Hook 的创建与使用
   - 自定义 Hook 复用逻辑


## **错误边界**

- `componentDidCatch`
- 错误捕获

## **Fragments**
参考：[[Fragment]]
- `<React.Fragment>`
- 短语法 `<>`

## **Portals**
参考：[[Portal]]
- `ReactDOM.createPortal`

## 性能优化
参考：[[_posts/react/总结/性能优化/性能优化-QW]]、
- `React.memo`
- `PureComponent`
- `shouldComponentUpdate`
- 懒加载 (`React.lazy` 和 `Suspense`)

## React DOM

- `ReactDOM.render`
- `ReactDOM.hydrate`
- `ReactDOM.createRoot`

## React 18+

- 并发模式（Concurrent Mode）。参考：[[并发模式-QW]]
	- `useTransition`/`useDeferredValue`
- 自动批处理。参考：[[自动批处理]]
- 服务端组件（Server Components）/客户端组件。参考：[[服务端组件]]

### React Server Components

- 服务端组件。参考：[[服务端组件]]
- 客户端组件。参考：[[客户端组件]]

# React 生态系统

## React Router
参考 [[React Router-DeepSeek]]

   - `BrowserRouter`
   - `Route`
   - `Link`
   - `useParams`
   - `useHistory`
   - `useLocation`
   - `Switch`
   - `Redirect`

## 状态管理库

### Redux
参考 [[Redux-DS]] 、[[原理-DB]]
- `createStore`
- `reducer`
- `dispatch`
- `connect`
- `useSelector`
- `useDispatch`
- `combineReducers`
- `applyMiddleware`
- Redux Thunk
- Redux 

### Zustand
参考 [[Zustand-DeepSeek]]、[[Zustand-豆包]]
- `create`
- `useStore`


### MobX
- `observable`
- `computed`
- `action`
- `observer`

### Recoil
- `atom`
- `selector`
- `useRecoilState`
- `useRecoilValue`

### Context API

## 数据获取

### Restful
#### Axios/Fetch

#### TanStack Query
参考 [[TanStack Query]]

- `useQuery`
- `useMutation`
- `QueryClient`
- `QueryClientProvider`

#### SWR（自动缓存）

### GraphQL

#### Apollo

#### Relay

#### urql

## **样式方案**

   - **CSS Modules**
   - **CSS-in-JS（Styled Components、Emotion）**
   - **Tailwind CSS**
   - **Sass/SCSS**

## **表单管理**

   - **Formik**
   - **React Hook Form**
   - **Final Form**

## **测试**

   - **Jest**（单元测试）
   - **React Testing Library**（组件测试）
   - **Enzyme**
   - **Cypress**（E2E）

## **动画**

   - **React Spring**
   - **Framer Motion**
   - **React Transition Group**

## **工具链**

### 构建工具（Webpack、Vite、Babel）

- **Webpack**
- **Babel**
- **Vite**
- **Parcel**

### 类型检查（TypeScript、PropTypes）

- **TypeScript**
- **PropTypes**

### 代码规范（ESLint、Prettier）
- **ESLint**
- **Prettier**


## **服务端渲染 (SSR)**

   - **Next.js**
     - `getStaticProps`
     - `getServerSideProps`
     - `getStaticPaths`
     - `next/link`
     - `next/router`
   - **Gatsby**
     - 静态站点生成
     - GraphQL 数据层
     - 插件系统

## **UI 组件库**

- **Material-UI (MUI)**
- **Ant Design**
- **Chakra UI**

## **开发工具**

- **React DevTools**
- **Redux DevTools**
- Storybook（组件文档）



## **PWA**（离线支持）

- **Workbox**
- **Create React App PWA**

## **微前端**

- **Single-SPA**
- **Module Federation**

## **国际化**

   - **React Intl**
   - **i18next**

## **桌面应用**

- **Electron**
- **Proton Native**

## **移动应用**

- **React Native**
- **Expo**

## **其他生态**

- 拖拽库（React DnD）
- 表格处理（React Table）
- 虚拟化（React Virtualized）
- 状态机（XState）
