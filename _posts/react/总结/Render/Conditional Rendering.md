在 React 里，`条件渲染`指的是*依据特定条件来决定渲染哪些内容*。这是构建动态用户界面的重要手段，能够*根据不同的状态、属性或其他条件展示不同的 UI 部分*。以下为你详细介绍 React 中几种常见的条件渲染方式。

### 1. 使用 `if` 语句

在函数组件里，可借助 JavaScript 的 `if` 语句实现条件渲染。

```jsx
import React from "react";

const UserGreeting = () => {
  const isLoggedIn = true;
  //
  if (isLoggedIn) {
    return <h1>Welcome back!</h1>;
  }
  return <h1>Please sign up.</h1>;
};

export default UserGreeting;
```

在类组件中同样可以使用 `if` 语句：

```jsx
import React from "react";

class UserGreetingClass extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isLoggedIn: false,
    };
  }

  render() {
    if (this.state.isLoggedIn) {
      return <h1>Welcome back!</h1>;
    }
    return <h1>Please sign up.</h1>;
  }
}

export default UserGreetingClass;
```

### 2. 元素变量

可以先把元素赋值给变量，然后根据条件决定是否渲染该变量。

```jsx
import React from "react";

const LoginButton = () => <button>Login</button>;
const LogoutButton = () => <button>Logout</button>;

const LoginControl = () => {
  const [isLoggedIn, setIsLoggedIn] = React.useState(false);

  let button;
  if (isLoggedIn) {
    button = <LogoutButton />;
  } else {
    button = <LoginButton />;
  }

  return <div>{button}</div>;
};

export default LoginControl;
```

### 3. 三元运算符

三元运算符 `condition ? trueExpression : falseExpression` 适合在 JSX 中进行*简单的条件渲染*。

```jsx
import React from "react";

const UserStatus = () => {
  const isLoggedIn = false;

  return (
    <div>{isLoggedIn ? <h1>Welcome, user!</h1> : <h1>Guest user</h1>}</div>
  );
};

export default UserStatus;
```

### 4. 逻辑与运算符 `&&`

逻辑与运算符 `&&` 可用于在满足条件时渲染元素。*当条件为 `true` 时，会渲染右侧的元素*；若条件为 `false`，则不会渲染右侧元素。

```jsx
import React from "react";

const MessageList = () => {
  const messages = ["Hello", "How are you?", "Goodbye"];

  return (
    <div>
      {messages.length > 0 && <h2>You have {messages.length} messages.</h2>}
    </div>
  );
};

export default MessageList;
```

### 5. 封装条件渲染组件

将条件渲染逻辑封装到一个单独的组件中，能提高代码的复用性和可维护性。

```jsx
import React from "react";

const ConditionalRender = ({ condition, children }) => {
  return condition ? children : null;
};

const App = () => {
  const showContent = true;

  return (
    <div>
      <ConditionalRender condition={showContent}>
        <p>This content is conditionally rendered.</p>
      </ConditionalRender>
    </div>
  );
};

export default App;
```

### 注意事项

- **布尔值 `false`、`null`、`undefined` 和 `0` 的处理**：在 JSX 中，`false`、`null`、`undefined` 和 `0` 不会被渲染。但要注意 `0` 的情况，例如在使用 `&&` 运算符时，如果左侧条件为 `0`，右侧元素不会渲染。
- **避免在三元运算符中使用复杂逻辑**：若条件渲染逻辑复杂，建议使用 `if` 语句或封装成单独的组件，以保证代码的可读性。

通过这些方法，你可以在 React 中根据不同条件灵活渲染 UI，打造出交互性强、动态变化的用户界面。
