
# Why
- **简化状态管理流程**：React-Redux **大大简化了**在 React 应用中使用 Redux 进行状态管理的复杂度。*它提供了一种直观的方式来让组件**获取和更新状态**，减少了开发者手动处理 Redux 与 React 之间交互的工作量*。例如，在一个具有多个组件需要共享和更新用户信息状态的应用中，通过 React-Redux 可以方便地在各个组件中获取用户信息和发送修改用户信息的 Action。
- **性能优化**：React-Redux 内部包含了性能优化机制。它通过比较组件所依赖的状态部分是否真正改变来决定组件是否需要重新渲染。这种浅比较的方式可以避免不必要的组件重新渲染，提高了应用的性能。例如，在一个包含多个列表项的组件中，如果只有一个列表项相关的状态发生变化，React-Redux 可以确保只有那个列表项对应的组件部分重新渲染，而不是整个列表组件。
- **代码结构清晰和可维护性强**：使用 React-Redux *有助于将业务逻辑和 UI 展示逻辑分离*。在应用中，Redux 负责管理状态和业务逻辑（通过 Action Creators 和 Reducer），而 React 组件主要负责 UI 展示和触发 Action。这种分离使得代码结构更加清晰，便于开发团队进行维护和扩展。例如，在一个电商应用中，购物车管理的业务逻辑（如添加商品、删除商品、计算总价）可以在 Redux 中处理，购物车组件只需要关注如何展示购物车内容和触发相关的购物车操作 Action。


建立 Redux 和 React 的联系：
1. React 组件能够在依赖的 Store 的**数据发生变化时，重新 Render**;
2. 在 React 组件中，能够在某些时机去 dispatch 一个 action，从而触发 Store 的更新。
# What
要实现这两点，我们需要引入 Facebook 提供的 **react-redux 这样一个工具库，工具库的作用就是建立一个桥梁，让 React 和 Redux 实现互通**。为了确保组件能够访问到全局唯一的 Redux Store，**在 react-redux 的实现中**，**利用了 React 的 Conext 机制去存放 Store 的数据**。**通常我们会将这个 Context 作为整个 React 应用程序的根节点**。

React-Redux 是 Redux 官方推荐的用于*将 Redux 和 React 应用集成在一起的库*。*它简化了在 React 组件中使用 Redux 进行状态管理的过程*，使得开发者可以更方便地利用 Redux 的可预测状态管理机制来构建复杂的 React 应用。

## `Provider`组件

**功能作用**：`Provider`是 React-Redux 提供的一个*高阶组件*，它用于**在组件树的顶层提供 Redux 的 Store**。通过将整个应用包裹在`Provider`组件中，可以让所有的子组件都能够访问到 Redux Store。

例如，在一个 React 应用的入口文件（如`index.js`）中，可以这样使用`Provider`：

```jsx
import React from'react';
import ReactDOM from'react-dom';
import { Provider } from'react-redux';
import store from './store';
import App from './App';
ReactDOM.render(
  <Provider store = {store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

## 数据传递原理

`Provider`组件**利用了 React 的上下文（Context）机制来传递 Store**。这样，无论组件在组件树中的层级有多深，只要它是`Provider`组件的后代，就可以获取到 Store，**从而实现了跨组件层级的状态共享**。

## `useSelector`和`useDispatch`（用于函数组件）

### useSelector

**它允许函数组件从 Redux Store 中选择（select）需要的状态部分，并将其作为组件的一个值来使用**。

例如，在一个函数组件中可以这样使用`useSelector`：

```jsx
import React from'react';
import { useSelector } from'react-redux';
function MyComponent() {
  const count = useSelector(state => state.count);
  return (
    <div>
      <p>Count: {count}</p>
    </div>
  );
}
```

  
### useDispatch

用于**获取`dispatch`函数**，使得函数组件能够发送 Action 来更新状态。例如，在一个函数组件中结合`useSelector`和`useDispatch`可以这样操作：

```jsx
import React from'react';
import { useSelector, useDispatch } from'react-redux';
function MyComponent() {
  const count = useSelector(state => state.count);
  const dispatch = useDispatch();
  const handleIncrement = () => {
    dispatch({ type: 'INCREMENT' });
  };
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>Increment</button>
    </div>
  );
}
```


## `connect`函数（用于类组件）

**连接组件与 Store**：`connect`函数用于**将 React 类组件与 Redux Store 连接起来**。它会*将 Store 中的状态映射到组件的属性（Props）中，并且将`dispatch`方法也传递给组件，使得组件能够方便地获取状态和发送 Action 来更新状态*。

**映射状态和操作到组件 Props**：`connect`函数接受两个参数，*第一个参数*是`mapStateToProps`函数，用于从 Store 的状态中提取组件需要的部分，并将其映射为组件的`props`。*第二个参数*是`mapDispatchToProps`函数（可选），*用于将 Action Creators（用于创建 Action 的函数）映射为组件可以调用的`props`函数*。这样，组件就可以通过`props`来获取状态和触发 Action，而不需要直接访问 Store。

例如，有一个名为`MyComponent`的类组件，通过`connect`函数连接后可以这样使用：

```jsx
import React from'react';
import { connect } from'react-redux';
class MyComponent extends React.Component {
  render() {
    // 
    const { count, dispatch } = this.props;
    return (
      <div>
        <p>Count: {count}</p>
        <button onClick={() => dispatch({ type: 'INCREMENT' })}>Increment</button>
      </div>
    );
  }
}
//
const mapStateToProps = state => ({
  count: state.count
});
export default connect(mapStateToProps)(MyComponent);
```



# How
