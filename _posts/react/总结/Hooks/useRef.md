# Why
- **访问 DOM 元素**
    - **直接操作 DOM**：在某些情况下，React 的声明式 UI 更新可能无法满足所有需求，*需要直接对 DOM 元素进行操作*。例如，在一个自定义的输入框组件中，可能需要*在组件挂载后自动聚焦输入框*。通过`useRef`可以获取输入框的 DOM 元素引用，然后使用原生的 JavaScript 方法（如`focus`）来实现自动聚焦。这避免了使用复杂的状态管理和事件处理来间接实现相同的效果，提高了代码的效率和可读性。
    - **与第三方 DOM - 相关库集成**：当使用第三方库（如一些动画库或者图表库）时，这些库可能需要直接操作 DOM 元素。`useRef`可以用于获取组件内的 DOM 元素引用，然后将其传递给第三方库，使得第三方库能够正确地与组件集成。例如，一个 JavaScript 动画库可能需要获取一个元素的引用，以便对其应用动画效果，`useRef`提供了一种方便的方式来提供这个元素引用。
- **跨渲染周期存储数据**
    - **避免状态重新初始化**：*与`useState`不同，`useRef`的值在组件重新渲染时不会被重新初始化*。**这使得它非常适合存储一些不需要触发组件重新渲染的数据**。例如，在一个计数器组件中，如果想要记录按钮被点击的总次数，而这个总次数的更新不需要重新渲染组件，可以使用`useRef`来存储这个计数。每次按钮被点击，更新`useRef`对象的`current`属性，*而不会像使用`useState`那样导致组件重新渲染*。
    - **存储组件实例相关的信息**：在某些复杂的组件逻辑中，*可能需要存储一些与组件实例相关的信息，如定时器的引用、上一次的状态值等*。`useRef`可以用于安全地存储这些信息，并且*在组件的整个生命周期内都可以访问和修改*。例如，在一个具有自动保存功能的表单组件中，可以使用`useRef`来存储定时器的引用，用于定期保存表单数据，这个定时器引用在组件重新渲染时不会丢失。

# What

`useRef`是 React 提供的一个 Hook，用于创建一个可变的引用对象，这个引用对象**在组件的整个生命周期内保持不变**。**它可以用于访问 DOM 元素、存储任何可变的值，并且这个值在组件*重新渲染时不会被重新初始化***。

# How

**基本语法**：
```jsx
const ref = useRef(initialValue);
```
- 其中`initialValue`是可选的初始值，它可以是任何类型的数据。`useRef`返回一个对象，这个对象有一个`current`属性，用于存储和访问引用的值。

**示例（访问 DOM 元素）**：

```jsx
import React, { useRef, useEffect } from'react';
function InputFocusComponent() {
  const inputRef = useRef();
  useEffect(() => {
    inputRef.current.focus();
  }, []);
  return (
    <input ref={inputRef} type="text" />
  );
}
```

在这个`InputFocusComponent`中，首先通过`useRef`创建了一个引用对象`inputRef`。在`useEffect`中，当组件挂载（依赖项数组为空，所以只在挂载时执行），通过`inputRef.current.focus()`获取输入框的 DOM 元素引用并调用`focus`方法，实现了输入框的自动聚焦。

**示例（跨渲染周期存储数据）**：

```jsx
import React, { useRef } from'react';
function CounterWithoutReRender() {
  // 
  const countRef = useRef(0);
  const handleClick = () => {
    countRef.current++;
    console.log(`按钮被点击了${countRef.current}次`);
  };
  return (
    <div>
      <button onClick={handleClick}>点击我</button>
    </div>
  );
}
```

在`CounterWithoutReRender`中，`useRef`用于存储按钮点击的计数。初始值为`0`，每次按钮被点击，通过`countRef.current++`更新计数。由于使用的是`useRef`，这个计数的更新不会导致组件重新渲染，只是更新了`countRef`对象的`current`属性，同时可以在控制台打印出按钮被点击的次数。