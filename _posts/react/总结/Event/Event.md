在 React 中，**事件处理**是*用户与组件交互*的核心机制。React 的事件系统通过**合成事件（SyntheticEvent）** 对原生浏览器事件进行封装，提供了跨浏览器的一致性和优化。

以下是 React 事件处理的详细指南：

---

### 1. 基本语法与特性

#### (1) 驼峰命名法

React 事件名使用**驼峰命名**（如 `onClick`），而非原生 DOM 的全小写形式（如 `onclick`）。

```jsx
<button onClick={handleClick}>Click</button>
```

#### (2) 传递函数而非字符串

事件处理程序是一个**函数引用**（如 `handleClick`），而不是字符串形式的代码。

```jsx
// ✅ 正确
<button onClick={handleClick}></button>

// ❌ 错误（类似 HTML 的字符串形式）
<button onClick="handleClick()"></button>
```

#### (3) 阻止默认行为

通过调用 `e.preventDefault()` 阻止原生行为（如表单提交、链接跳转）。

```jsx
function Form() {
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Form submitted!");
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

---

### 2. 合成事件（SyntheticEvent）
参考 [[合成事件（SyntheticEvent）]]

React 的 `SyntheticEvent` 是*对原生事件的包装*，具有与原生事件*相同的接口*（如 `e.target`, `e.stopPropagation()`），_但解决了浏览器兼容性问题_。

#### 事件池（Event Pooling）

- **React 17 之前**：合成事件会被重用（事件池机制），异步访问事件属性需调用 `e.persist()`。
- **React 17+**：事件池机制被移除，无需再使用 `e.persist()`。

```jsx
// React 17+ 无需处理事件池
const handleChange = (e) => {
  setTimeout(() => {
    console.log(e.target.value); // 正常访问
  }, 1000);
};
```

---

### 3. 事件处理中的 `this` 绑定（类组件）

在类组件中，事件处理函数默认没有绑定 `this`，需手动绑定或使用箭头函数。

#### 方法 1：构造函数中绑定

```jsx
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    console.log("Clicked!", this.props);
  }

  render() {
    return <button onClick={this.handleClick}>Click</button>;
  }
}
```

#### 方法 2：箭头函数（类属性语法）

```jsx
class Button extends React.Component {
  // 类属性语法、类对象的属性
  handleClick = () => {
    console.log("Clicked!", this.props);
  };

  render() {
    return <button onClick={this.handleClick}>Click</button>;
  }
}
```

---

### 4. 传递参数给事件处理函数

使用箭头函数或 `bind` 传递额外参数。_再包装一层函数。外层函数，是事件处理函数，接收 Event 对象。内层函数，是具体的事件处理逻辑函数，接收 Event 对象、其他参数如 Id_。

```jsx
function ItemList({ items }) {
  const handleDelete = (itemId, e) => {
    console.log("Delete item:", itemId, e);
  };

  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>
          {item.name}
          <button onClick={(e) => handleDelete(item.id, e)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}
```

---

### 5. 常见事件类型

React 支持绝大多数 DOM 事件，常见事件包括：

| 事件名                   | 说明        |
| --------------------- | --------- |
| `onClick`             | 点击事件      |
| `onChange`            | 输入框/表单值变化 |
| `onSubmit`            | 表单提交      |
| `onMouseEnter`        | 鼠标进入元素    |
| `onMouseLeave`        | 鼠标离开元素    |
| `onKeyDown`/`onKeyUp` | 键盘按下/释放   |
| `onFocus`/`onBlur`    | 元素聚焦/失焦   |

---

### 6. 事件冒泡与捕获

React 事件默认在**冒泡阶段**触发，但可以通过 `onClickCapture` 在捕获阶段处理。

```jsx
function Parent() {
  const handleCapture = (e) => {
    console.log("捕获阶段: Parent");
  };

  const handleBubble = (e) => {
    console.log("冒泡阶段: Parent");
  };

  return (
    <div onClickCapture={handleCapture} onClick={handleBubble}>
      <button onClick={() => console.log("冒泡阶段: Button")}>Click</button>
    </div>
  );
}

// 输出顺序:
// 捕获阶段: Parent
// 冒泡阶段: Button
// 冒泡阶段: Parent
```

---

### 7. 性能优化

#### (1) 避免不必要的重渲染

在渲染时，如果使用内联箭头函数，*创建新函数可能导致子组件不必要的重渲染*。使用 `useCallback` 缓存函数：

```jsx
function Button({ onClick }) {
  return <button onClick={onClick}>Click</button>;
}

function Parent() {
  //
  const handleClick = useCallback(() => {
    console.log("Optimized click");
  }, []);

  return <Button onClick={handleClick} />;
}
```

#### (2) 停止事件冒泡

使用 `e.stopPropagation()` 阻止事件冒泡：

```jsx
function Child() {
  const handleClick = (e) => {
    e.stopPropagation();
    console.log("Child clicked, but parent won't know");
  };

  return <button onClick={handleClick}>Click Child</button>;
}
```

---

### 8. 自定义事件与事件传递

在复杂组件层级中，可以通过 `props` 或 `Context` *传递事件处理函数*：

```jsx
// 父组件传递事件处理函数
function Parent() {
  const handleCustomEvent = (data) => {
    console.log("Received data:", data);
  };

  return <Child onCustomEvent={handleCustomEvent} />;
}

// 子组件触发自定义事件
function Child({ onCustomEvent }) {
  const handleClick = () => {
    onCustomEvent({ message: "Hello from Child!" });
  };

  return <button onClick={handleClick}>Trigger Event</button>;
}
```

---

### 9. 合成事件与原生事件的混用

在需要直接操作原生事件时，可通过 `addEventListener` 绑定，**但需注意清理**：

```jsx
function NativeEventExample() {
  const ref = useRef(null);

  useEffect(() => {
    const handleResize = () => console.log("Window resized");
    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize); // 清理
    };
  }, []);

  return <div ref={ref}>Resize the window!</div>;
}
```

---

### 10. 常见陷阱与解决方案

#### 陷阱 1：直接在 JSX 中调用函数

```jsx
// ❌ 错误：页面加载时立即执行，点击按钮时无效
<button onClick={handleClick()}>Click</button>

// ✅ 正确：传递函数引用
<button onClick={handleClick}>Click</button>
```

#### 陷阱 2：异步访问事件属性

在 React 17 之前，异步操作需调用 `e.persist()`：

```jsx
const handleChange = (e) => {
  e.persist(); // React 17+ 不需要
  setTimeout(() => {
    console.log(e.target.value);
  }, 1000);
};
```

---

### 总结

- **合成事件**：React 封装了跨浏览器兼容的事件系统。
- **性能优化**：避免内联函数，使用 `useCallback` 或类组件的正确绑定。
- **事件传播**：冒泡与捕获阶段的行为与原生 DOM 一致。
- **异步处理**：React 17+ 无需手动处理事件池。
- **自定义事件**：通过 props 或 Context 实现组件间通信。

掌握这些核心概念，可以高效处理 React 中的用户交互，同时避免常见错误！
