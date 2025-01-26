在 React 中，组合（Composition）和继承（Inheritance）是**两种不同的代码复用和组件构建方式**，下面将详细介绍它们的概念、使用方式以及优缺点对比。

### 组合（Composition）

#### 概念

`组合`是指*将多个小的、功能单一的组件组合在一起，形成一个更大、更复杂的组件*。在 React 里，*组合是推荐的代码复用方式*，它利用组件之间的嵌套关系来实现功能的复用和扩展。*通过将不同功能封装在独立的组件中，再将这些组件组合起来，可以构建出灵活且可维护的用户界面*。

#### 使用方式

##### 普通子组件组合

父组件可以通过 JSX 语法将子组件作为标签嵌套在自身内部，子组件可以接收父组件传递的 `props` 来进行个性化渲染。

```jsx
// 子组件
const WelcomeMessage = (props) => {
  return <p>Welcome, {props.name}!</p>;
};

// 父组件
const App = () => {
  return (
    <div>
      <WelcomeMessage name="John" />
    </div>
  );
};

export default App;
```

在上述代码中，`App` 组件将 `WelcomeMessage` 组件组合进来，并传递了 `name` 属性。

##### 使用 `children` prop 进行内容传递

父组件可以通过 `props.children` 来渲染嵌套在其标签内部的子元素，这样可以实现更灵活的内容插入。

```jsx
// 容器组件
const Container = (props) => {
  return <div className="container">{props.children}</div>;
};

// 使用容器组件
const Page = () => {
  return (
    <Container>
      <h1>Page Title</h1>
      <p>Page content goes here.</p>
    </Container>
  );
};

export default Page;
```

这里 `Container` 组件通过 `props.children` 渲染了嵌套在 `<Container>` 标签内的 `h1` 和 `p` 元素。

#### 优点

- **灵活性高**：可以根据不同的需求自由组合各种组件，轻松实现不同的 UI 布局和功能。
- **可维护性强**：每个组件功能单一，职责明确，修改一个组件不会对其他组件产生过多影响。
- **易于理解**：组件之间的关系通过嵌套结构直观展示，代码逻辑清晰。

#### 缺点

- 当组件嵌套层次过深时，可能会导致代码结构复杂，影响代码的可读性。

### 继承（Inheritance）

#### 概念

`继承`是面向对象编程中的一种机制，在 React 中，类组件可以通过继承 `React.Component` 或其他自定义类组件来复用代码和功能。子类可以继承父类的属性和方法，并且可以进行重写和扩展。

#### 使用方式

```jsx
import React from "react";

// 父类组件
class ParentComponent extends React.Component {
  sayHello() {
    return "Hello!";
  }

  render() {
    return <div>{this.sayHello()}</div>;
  }
}

// 子类组件
class ChildComponent extends ParentComponent {
  sayHello() {
    return "Hello from child!";
  }
}

export default ChildComponent;
```

在上述代码中，`ChildComponent` 继承自 `ParentComponent`，并重写了 `sayHello` 方法。

#### 优点

- **代码复用**：可以复用父类的属性和方法，减少代码重复。
- **层次结构清晰**：通过继承关系可以明确组件之间的层次和依赖关系。

#### 缺点

- **耦合度高**：子类和父类之间的依赖关系紧密，父类的修改可能会影响到子类，增加了代码的维护难度。
- **缺乏灵活性**：继承关系一旦确定，很难在运行时进行调整和改变。

### 组合与继承的选择

在 React 中，官方推荐优先使用组合而非继承。组合更加灵活和可维护，能够更好地适应不同的需求变化。只有在极少数情况下，当多个组件具有非常相似的行为和属性，并且需要通过层次结构来管理时，才考虑使用继承。例如，在实现一些基础组件库时，可能会使用继承来构建具有相似功能的组件系列。

综上所述，组合是 React 中更常用、更推荐的代码复用方式，而继承则应谨慎使用。
