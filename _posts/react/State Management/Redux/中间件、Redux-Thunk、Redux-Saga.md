# Middleware 的定义与场景
    
- **定义**：在 Redux 中，Middleware（中间件）是一种*位于 Action 被发送到 Reducer 之间（前）* 的处理机制。它本质上*是一个函数，可以对 Action 进行拦截、修改、执行额外的操作，如日志记录、异步操作处理等，然后再将 Action 传递给 Reducer*。
- **作用场景示例**：
	- **日志记录**：在开发过程中，通过 Middleware 可以记录每一个被发送的 Action 的类型和内容，*这有助于调试应用*，特别是当应用的状态变化逻辑比较复杂时。例如，当有多个用户操作（如添加商品到购物车、删除商品、修改用户信息）会导致不同的 Action 被发送，使用日志记录 Middleware 可以清楚地看到每个 Action 的触发顺序和具体内容。
	- **异步操作处理**：在实际应用中，很多操作是异步的，比如从服务器获取数据（如获取商品列表、用户订单信息等）。Middleware 可以用于处理这些异步操作，*使得在异步操作完成后能够正确地发送 Action 来更新应用的状态*。

# Middleware 的工作原理
    
## 函数结构与参数

一个 Redux Middleware 是一个具有特定签名的函数，它*接收三个参数：* 
- `store`：Redux 的 Store 对象
- `next`：下一个中间件或者最终的 Reducer 处理函数
- `action`：被发送的 Action 对象
例如，一个简单的 Middleware 函数可能如下：

```jsx
const myMiddleware = (store, next, action) => {
  // 在这里可以进行一些操作，如打印日志
  console.log('正在处理Action:', action.type);
  // 调用下一个中间件或者Reducer
  const result = next(action);
  // 也可以在Action被处理后进行一些操作
  console.log('Action处理完成');
  return result;
}
```

  
## 调用链与顺序执行

当一个 Action 被发送到 Store 的`dispatch`方法时，Middleware 会按照它们被添加到 Store 的顺序依次执行。
假设我们有三个 Middleware：`middleware1`、`middleware2`和`middleware3`，当一个 Action 被发送时，执行顺序是`middleware1` -> `middleware2` -> `middleware3` -> `Reducer`。
每个 Middleware *可以选择是否将 Action 传递给下一个中间件或者 Reducer*，这通过调用`next(action)`来实现。如果一个 Middleware 没有调用`next(action)`，那么 Action 就不会继续传递，后续的 Middleware 和 Reducer 都不会接收到这个 Action。


常用的 Middleware 示例：
# Redux-Thunk

**用途与功能**：Redux-Thunk 是一个非常常用的 Middleware，用于处理异步操作。*它允许 Action Creators（用于创建 Action 的函数）返回一个函数*，而不是一个普通的 Action 对象。这个返回的函数可以*接收`dispatch`和`getState`作为参数，这样就可以在函数内部进行异步操作（如发送网络请求），并根据异步操作的结果发送新的 Action*。

**工作方式与优势**：通过允许 Action Creators 返回函数，Redux-Thunk 打破了 Redux 中 Action 必须是一个简单对象的限制，*使得异步操作的处理更加自然和方便*。它可以在异步操作（如网络请求）的不同阶段（如请求开始、请求成功、请求失败）发送不同的 Action 来更新应用的状态，从而更好地管理异步操作对应用状态的影响。

例如，在一个从服务器获取用户信息的场景中，可以这样使用 Redux-Thunk：

```jsx
const fetchUserInfo = () => {
  return async (dispatch, getState) => {
    try {
      const response = await fetch('https://example.com/api/user-info');
      const userInfo = await response.json();
      dispatch({ type: 'SET_USER_INFO', payload: userInfo });
    } catch (error) {
      dispatch({ type: 'FETCH_USER_INFO_ERROR', payload: error });
    }
  };
}
```

  

# Redux-Saga
**用途与功能**：Redux-Saga 是*另一种处理异步操作的 Middleware，它使用了 ES6 的 Generator 函数来实现*。它可以将复杂的异步操作（如多个网络请求的顺序执行或者并行执行、基于异步操作结果的复杂逻辑判断等）进行集中管理，*使得异步操作的流程更加清晰和易于维护*。例如，在一个电商应用中，使用 Redux-Saga 可以处理购物车商品的价格更新（可能涉及到从服务器获取最新价格）和订单提交（包括多个网络请求，如验证用户信息、创建订单、支付等）等复杂的异步操作。

**工作方式与优势**：Redux-Saga 通过监听特定的 Action 来触发 Generator 函数的执行。在 Generator 函数内部，可以使用各种 Effect（如`call`用于调用函数，`put`用于发送 Action，`take`用于等待一个特定的 Action 等）来处理异步操作和发送 Action。这种方式使得异步操作的代码与 Redux 的其他部分（如 Reducer）分离，并且可以方便地进行测试和维护。例如，在一个处理用户登录的 Saga 中，可以有一个 Generator 函数，它等待`LOGIN_REQUEST`的 Action，然后调用登录函数进行登录操作，根据登录结果发送`LOGIN_SUCCESS`或者`LOGIN_FAILURE`的 Action。