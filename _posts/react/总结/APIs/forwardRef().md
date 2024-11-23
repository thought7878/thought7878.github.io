为什么使用React的forwardRef()？React的forwardRef()是什么？
# Why
- **访问子组件的 DOM 元素或实例**
    - **直接操作子组件 DOM**：在某些情况下，父组件需要直接操作子组件的 DOM 元素。例如，在一个自定义的表单组件中，父组件可能想要聚焦到子组件中的某个输入框，或者获取子组件中某个元素的尺寸等信息。通过`forwardRef`，可以将父组件的`ref`传递给子组件，使得父组件能够访问子组件内部的 DOM 元素，然后使用原生的 JavaScript 方法（如`focus`、`getBoundingClientRect`等）进行操作。
    - **访问子组件实例方法或状态（如果有）**：对于类组件或者通过`useImperativeHandle`暴露了内部方法的函数组件，`forwardRef`可以让父组件访问这些内部方法或状态。例如，在一个包含模态框（Dialog）组件的应用中，父组件可能需要调用模态框组件内部的`open`或`close`方法来控制模态框的显示和隐藏，`forwardRef`可以实现这种跨组件的方法调用。
- **跨组件的功能集成和代码复用**
    - **封装和复用组件逻辑**：当创建可复用的组件时，有时候需要将组件的引用传递给更高层次的组件，以便在不同的场景下进行集成。`forwardRef`使得组件的封装更加灵活，它可以在不破坏组件内部封装性的前提下，允许外部组件在需要的时候访问内部细节。例如，在一个组件库中，有一个通用的下拉菜单组件，通过`forwardRef`，使用这个组件的应用可以在需要的时候访问下拉菜单的内部元素或者方法，同时组件库本身可以继续完善和更新下拉菜单组件的内部逻辑，而不会影响外部的使用方式。
    - **第三方组件的适配和扩展**：在使用第三方组件时，可能需要对其进行扩展或者与其他组件进行集成。`forwardRef`可以帮助将外部的`ref`传递给第三方组件，使得可以更好地与第三方组件进行交互。例如，将一个自定义的样式组件的`ref`传递给一个第三方的功能组件，以实现样式和功能的结合。

# What

`forwardRef`是 React 提供的一个函数，用于**将父组件的`ref`（引用）转发给子组件**。**这使得在父组件中可以直接访问子组件的 DOM 节点或者组件实例**。

# How

**基本语法**：
```jsx
const ForwardedComponent = forwardRef((props, ref) => { 
	// 子组件逻辑 
});
```
- 它接收一个函数作为参数，*这个函数就是子组件的实现*。
- 这个函数有两个参数：`props`（属性）和`ref`（引用）。在子组件内部，可以将`ref`绑定到需要暴露的 DOM 元素或者组件实例上。

**示例（访问子组件 DOM 元素）**：

```jsx
import React, { forwardRef, useRef } from'react';
const InputComponent = forwardRef((props, ref) => {
  return (
    <input ref={ref} type="text" {...props} />
  );
});
function ParentComponent() {
  //
  const inputRef = useRef();
  const handleFocus = () => {
    inputRef.current.focus();
  };
  return (
    <div>
      <InputComponent ref={inputRef} />
      <button onClick={handleFocus}>聚焦输入框</button>
    </div>
  );
}
```

- 在这个示例中，首先通过`forwardRef`创建了`InputComponent`，将父组件传递过来的`ref`绑定到了`<input>`元素上。在`ParentComponent`中，通过`useRef`创建了一个引用`inputRef`，并将其传递给`InputComponent`。当点击 “聚焦输入框” 按钮时，通过`inputRef.current.focus()`可以直接访问`InputComponent`中的`<input>`元素并调用`focus`方法，实现了输入框的聚焦。

**示例（访问子组件实例方法）**：

```jsx
import React, { forwardRef, useImperativeHandle, useState } from'react';
const ChildComponent = forwardRef((props, ref) => {
  const [count, setCount] = useState(0);
  // 
  useImperativeHandle(ref, () => ({
    increment: () => setCount(count + 1),
    getCount: () => count
  }));
  return (
    <div>
      <p>子组件计数: {count}</p>
    </div>
  );
});
function ParentComponent() {
  const childRef = useRef();
  const handleIncrement = () => {
    childRef.current.increment();
  };
  const getCount = () => {
    return childRef.current.getCount();
  };
  return (
    <div>
      <ChildComponent ref={childRef} />
      <button onClick={handleIncrement}>增加计数</button>
      <p>子组件计数: {getCount()}</p>
    </div>
  );
}
```

 在这个示例中，`ChildComponent`通过`forwardRef`接收`ref`，并且在内部使用`useImperativeHandle`暴露了`increment`和`getCount`方法。在`ParentComponent`中，`useRef`创建的`childRef`传递给`ChildComponent`，然后可以通过`childRef.current`访问子组件暴露的方法，实现了对子组件内部计数功能的控制。