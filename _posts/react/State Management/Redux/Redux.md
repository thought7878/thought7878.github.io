# 为什么使用 Redux？

## Redux 解决了什么问题？

1. **解决组件间状态共享和传递的复杂性**
   - **多层组件数据共享难题**：_在复杂的 React 应用中，当多个组件需要共享同一份数据时，如果仅通过组件间的属性（Props）层层传递，会导致代码变得非常复杂_。例如，在一个具有多层嵌套组件的电商应用中，购物车状态（商品列表、总价等）可能需要在购物车组件、商品列表组件、商品详情组件以及结算组件等多个地方使用。如果使用传统的 Props 传递方式，从最顶层组件将购物车状态传递到最底层的结算组件，需要经过许多中间组件，这不仅使代码难以维护，还容易出错。
   - **Redux 的解决方案**：Redux 提供了一个全局的状态存储（Store），可以将购物车状态等共享数据存储在其中。任何组件都可以通过订阅 Store 的状态变化来获取最新的购物车状态，而不需要通过复杂的 Props 传递。例如，通过使用 Redux，购物车组件可以修改购物车状态，而其他关注购物车状态的组件（如结算组件）可以自动获取到更新后的状态，大大简化了组件间数据共享的方式。
2. **可预测的状态管理**
   - **不可预测的状态变化问题**：在没有像 Redux 这样的状态管理工具时，当多个组件可以修改同一份数据时，很难追踪状态是如何以及何时被修改的。例如，在一个团队开发的大型应用中，不同的开发者可能在不同的组件中对共享的用户信息状态进行修改。如果没有统一的管理方式，可能会出现一个组件修改了用户信息，导致其他组件中的相关功能出现意外错误的情况，这种状态变化是不可预测的。
   - **Redux 的工作机制带来的可预测性**：Redux 通过定义明确的规则来管理状态变化。所有的状态修改都必须通过发送一个 Action 来触发，并且 Action 是一个简单的 JavaScript 对象，其中包含了描述动作类型的`type`属性。例如，要修改用户信息状态，需要发送一个如`{ type: 'UPDATE_USER_INFO', payload: { name: '新名字' } }`这样的 Action。然后，由 Reducer 纯函数根据 Action 的类型来更新状态。_这种方式使得状态的变化路径清晰、可追溯，开发者可以很容易地理解状态是如何被修改的，增强了应用状态管理的可预测性_。
3. **便于调试和时间旅行（Time - Travel）功能**
   - **调试复杂状态变化的困难**：在应用的状态频繁变化且变化逻辑复杂的情况下，传统的调试方式可能会遇到困难。例如，当一个应用中有多个用户操作会导致状态变化，如用户登录、修改个人信息、进行购物等操作，在出现问题时很难确定是哪一个操作导致了状态的异常。
   - **Redux DevTools 和时间旅行功能**：Redux 配合 Redux DevTools 提供了强大的调试功能，其中包括时间旅行功能。Redux DevTools 可以记录应用中每一个 Action 的发送和对应的状态变化。开发者可以在开发工具中查看状态变化的历史记录，并且可以回退到之前的状态，就像时间旅行一样。这对于重现问题和理解状态变化的过程非常有帮助，大大提高了调试的效率。
4. **将业务逻辑与组件分离**
   - **组件内业务逻辑混杂的问题**：在没有状态管理工具的情况下，组件往往不仅包含 UI 展示逻辑，还包含大量的数据获取、状态修改等业务逻辑。例如，一个用户登录组件可能既要处理 UI 的渲染（如显示登录表单），又要负责发送登录请求、处理登录成功或失败后的状态更新等业务逻辑。这样会导致组件变得臃肿，难以维护和复用。
   - **Redux 对业务逻辑的分离作用**：使用 Redux 可以将业务逻辑（如数据获取、状态修改规则）从组件中分离出来。例如，_登录相关的业务逻辑可以放在 Redux 的 Action Creators（用于创建 Action 的函数）和 Reducer 中_。组件只需要发送登录 Action 和获取登录状态，从而使组件更加专注于 UI 展示，提高了组件的可维护性和复用性。

# Redux 是什么？

Redux 是一个用于 JavaScript 应用的可预测状态管理容器。**它的主要功能**是帮助开发者管理应用程序中的状态，使得状态的变化更可预测、更容易调试。**其核心原则**是将应用的整个状态存储在一个单一的对象树中，并且这个状态是只读的，只能通过触发特定的动作（Action）来改变。通常*用于大型复杂的前端应用*，特别是和 React 等框架配合使用，但也可以用于其他 JavaScript 环境，如 Node.js。

## 核心概念

**State（旧数据）** 即 Store，一般就是一个纯 JavaScript Object。 **Action（新数据）** 也是一个 Object，用于描述发生的动作和动作产生的数据。**Reducer（产生新 state 的逻辑）** 则是一个函数，接收 Action 和 State 作为参数，通过计算得到新的 State。它们三者之间的关系可以用下图来表示:
![](2024-02-08-11-07-26-image.png)

### Store（数据存储）

**单一数据源**：整个应用**只有一个 Store**，它是一个**包含应用全部状态的 JavaScript 对象**。例如，在一个电商应用中，商品列表、购物车内容、用户信息等所有数据都存储在这个单一的 Store 中。_这就像是一个数据库，所有组件需要的数据都从这里获取_。

- **方法**：
  - **`getState`**：用于*获取 Store 中的当前状态*。任何组件都可以调用这个方法来获取应用的最新状态。例如，`const state = store.getState();`，可以得到整个应用状态的一个快照。
  - **`dispatch`**：**这是改变 Store 中状态的唯一方式**。组件通过发送一个 Action（动作）到 Store 的`dispatch`方法来请求状态的改变。比如，`store.dispatch({ type: 'ADD_PRODUCT_TO_CART', payload: { productId: '123', quantity: 1 } });`，这里发送了一个添加产品到购物车的 Action。
  - **`subscribe`**：用于**订阅 Store 状态的变化**。**当状态更新时，订阅的函数会被调用**。这使得组件能够在状态改变时及时更新自己。例如，`const unsubscribe = store.subscribe(() => console.log('状态已更新'));`，每次状态更新都会执行这个打印日志的函数。

![](2024-02-08-11-06-16-image.png)

### Action

- **定义**：Action 是一个简单的 JavaScript 对象，用于**描述发生了什么事情**，它是改变状态的最小单位。每个 Action 必须包含一个`type`属性，这个属性是一个字符串，用于**标识动作的类型**。例如，`{ type: 'INCREMENT_COUNTER' }`是一个典型的 Action，表示计数器增加的动作。
- **更新 State 的数据**：这些数据被称为`payload`，用于传递与动作相关的详细信息。例如，`{ type: 'UPDATE_USER_INFO', payload: { name: '新名字', age: 30 } }`这个 Action 包含了更新用户姓名和年龄的信息。

### Reducer

**函数特性与作用**：Reducer 是一个**纯函数**，它接收当前的状态和一个 Action 作为输入，然后返回一个新的状态。它根据 Action 的`type`来决定如何更新状态。

**纯函数要求**：因为 Reducer 是纯函数，所以对于相同的输入（相同的当前状态和 Action），它总是返回相同的输出。并且它不能有副作用，不能修改外部变量，也不能进行如发送网络请求、操作 DOM 等操作。

例如，对于一个计数器应用的 Reducer 可以这样写：

```js
function counterReducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT_COUNTER":
      return state + 1;
    case "DECREMENT_COUNTER":
      return state - 1;
    default:
      return state;
  }
}
```

## 工作流程

### 状态更新过程：
  - 当用户在应用中进行操作（如点击按钮、输入文本等），组件会通过`dispatch`发送一个 Action 到 Store。例如，在一个 React - Redux 应用中，一个按钮的`onClick`事件可能触发一个`{ type: 'DELETE_ITEM' }`的 Action。
  - Store 会**将这个 Action 传递给所有的 Reducer**。每个 Reducer 检查 Action 的`type`，如果匹配自己处理的类型，就会返回一个新的状态；如果不匹配，就返回原来的状态。
  - **Store 用 Reducer 返回的新状态替换原来的状态，并且通知所有订阅了状态变化的组件。这些组件会重新获取状态来更新自己的 UI。**

### 状态初始化：
- 在应用启动时，通过`createStore`函数创建 Store，并传入一个或多个 Reducer 来处理不同类型的 Action。在创建 Store 时，Redux 会*调用 Reducer 并传入初始状态（通常是在 Reducer 函数中定义的默认状态）来得到初始的应用状态*。

## 源码
伪代码：

```js
export default function createStore(reducer, enhancer) {
  if (enhancer) {
    return enhancer(createStore)(reducer);
  }

  // 数据、全局的数据源、全局的状态
  let currentState;

  let listenerIdCounter = 0;
  let currentListeners = new Map(); //[];

  // 查询 state
  function getState() {
    return currentState;
  }

  // 修改 state
  function dispation(action) {
    currentState = reducer(currentState, action);
	// 发布、通知
    currentListeners.forEach((listener) => listener());
    return action;
  }

  // 订阅
  function subscribe(listener) {
    const listenerId = listenerIdCounter++;
    currentListeners.set(listenerId, listener);
    return () => {
      currentListeners.delete(listenerId);
    };
  }

  dispatch({ type: `@@redux/INIT${randomString()}` });

  return { getState, dispatch, subscribe };
}

const randomString = () =>
  Math.random().toString(36).substring(7).split("").join(".");
```

## 中间件

[[中间件、Redux-Thunk、Redux-Saga]]

# How

**与其他技术结合（以 React 为例）**
在 React - Redux 应用中，使用`react - redux`库来连接 Redux 和 React。

- 对于类组件，可以使用`connect`函数将组件与 Redux 的 Store 连接起来，它会将 Store 中的状态映射到组件的`props`中，并且将`dispatch`方法也传递给组件。
- 对于函数组件，可以使用`useSelector`和`useDispatch`钩子，`useSelector`用于从 Store 中获取状态，`useDispatch`用于获取`dispatch`方法，这样组件就可以方便地利用 Redux 来管理状态并更新 UI。

# 参考

[[7.全局状态管理：如何在函数组件中使用 Redux]]
[[05\_数据是如何在 React 组件之间流动的？（下）
