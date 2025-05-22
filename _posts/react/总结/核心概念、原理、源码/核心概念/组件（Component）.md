在 React 中，**组件（Component）是构建用户界面的核心概念之一**。React 的设计理念基于*组件化开发思想*，**它将 UI 拆分成独立、可复用的部分，每个部分都封装了自身的结构、行为和状态**。

---

## 🧱 一、什么是组件？

`组件`是一个 **可复用的 UI 单元**，可以理解为一个*函数或类*，它可以接收输入（称为 `props`），并返回一段 React 元素（即 JSX），描述应该显示什么内容。

---

## 二、组件的两种类型

### 1. 函数组件（Function Component）

- 使用 JavaScript 函数定义。
- 推荐方式，更简洁、易读。
- 可以使用 Hooks 来管理状态和副作用。

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

### 2. 类组件（Class Component）

- 使用 ES6 的类定义。
- 支持生命周期方法、内部状态等复杂逻辑（现在大多被 Hook 替代）。

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

---

## 三、组件的特点

### 可组合性（Composability）
 
你可以*将多个小组件组合成一个更大的组件：*

```jsx
function App() {
  return (
    <div>
      <Welcome name="Alice" />
      <Welcome name="Bob" />
    </div>
  );
}
```

### 可重用性（Reusability）

组件可以*在不同地方重复使用*，只要传入不同的 props。

### 可维护性（Maintainability）

*每个组件只关注自己的功能，便于测试和维护*。

---

## 四、props（属性）

- *组件间通信的方式*。
- 是“只读”的，不能直接修改 props。

```jsx
<Welcome name="Tom" />
```

在组件内部通过 `props.name` 访问。

---

## 五、state（状态）

- 状态是**组件内部的数据**。
- 用于保存、控制组件的行为和渲染。
- 使用 `useState`（函数组件）或 `this.state`（类组件）来管理。

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

---

## 六、组件通信

- **父传子**：通过 props。
- **子传父**：*子组件调用 props 中的回调函数*。
- **跨级传递**：可用 Context API 或 Redux 等状态管理工具。

---

## 七、组件树（Component Tree）

React 应用通常由多个嵌套的组件构成一棵树状结构：

```
App
├── Header
├── MainContent
│   ├── PostList
│   └── Sidebar
└── Footer
```

---

## 八、组件更新机制

*当组件的 `props` 或 `state` 发生变化时*，React 会自动重新渲染该组件及其子组件。

---

## 九、高级组件（HOC）与自定义 Hook

- **高阶组件（Higher-Order Component）**：*接收一个组件并返回一个新组件*。
- **自定义 Hook**：*封装可复用的逻辑，如数据获取、订阅等。*

---

## 十、最佳实践

- 组件应保持单一职责原则。
- 尽量使用函数组件 + Hook。
- 把状态提升到共同父组件中共享。
- 组件命名清晰、语义明确。
- 使用 TypeScript 提升类型安全性。

---

## 📝 总结

| 特性         | 描述 |
|--------------|------|
| 组件类型     | 函数组件 / 类组件 |
| 数据传递     | props（只读）、state（可变） |
| 组件通信     | 父传子、子传父、Context、Redux |
| 更新机制     | state/props 变化触发 re-render |
| 开发理念     | 组件化、声明式编程 |

---
