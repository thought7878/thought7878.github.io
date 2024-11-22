# Why

- **性能优化**
  - **避免不必要的函数重新创建**：在 React 函数组件中，每次组件重新渲染，内部定义的函数都会被重新创建。当这些函数作为属性（Props）传递给子组件时，可能会导致子组件不必要地重新渲染。例如，在一个包含列表组件和每个列表项组件的应用中，列表项组件接收一个`onClick`函数作为属性。如果没有`useCallback`，每次列表组件重新渲染（可能因为其他无关状态的变化），传递给列表项组件的`onClick`函数都会被重新创建，这可能会导致列表项组件重新渲染，即使`onClick`函数的逻辑并没有改变。使用`useCallback`可以缓存这个`onClick`函数，只有当函数的依赖项（例如，函数内部引用的某些状态或属性）发生变化时，才会重新创建它，从而避免了子组件不必要的重新渲染。
  - **减少计算开销**：对于一些复杂的函数，重新创建它们可能会带来一定的计算开销。例如，一个函数内部包含大量的计算或者对外部资源的访问（如发送网络请求来获取数据），如果这个函数频繁地被重新创建，这些计算或资源访问就会被重复执行。通过`useCallback`缓存函数，可以减少这种不必要的计算开销。
- **保持引用相等**
  - **用于依赖比较**：在一些场景下，React 依赖函数引用的相等性来决定是否执行某些操作。例如，在`useEffect`钩子中，如果依赖项是一个函数，React 会比较每次渲染时这个函数的引用是否相同。如果没有`useCallback`，函数在每次渲染时都会有不同的引用，这可能会导致`useEffect`钩子中的副作用函数被频繁执行，即使函数的逻辑并没有改变。使用`useCallback`可以确保在函数逻辑不变的情况下，函数引用保持相等，从而使得`useEffect`等依赖引用比较的机制能够正确地工作。

# What

`useCallback`是 React 提供的一个 Hook，用于缓存函数。_它返回一个记忆化（memorized）的函数，这个函数在组件重新渲染时只有在其依赖项发生变化时才会重新创建。_

# How

**基本语法**：

```jsx
const memoizedCallback = useCallback(() => { // 函数逻辑
}, [dependency1, dependency2,...]);
```

- 第一个参数是要缓存的函数，
- 第二个参数是一个依赖项数组。只有当依赖项数组中的任何一个元素发生变化时，`useCallback`才会重新创建这个函数。

**示例**：

```jsx
import React, { useState, useCallback } from "react";
function ParentComponent() {
  const [count, setCount] = useState(0);
  const handleIncrement = useCallback(() => {
    setCount(count + 1);
  }, [count]);
  return (
    <div>
      <p>Count: {count}</p>
      <ChildComponent onIncrement={handleIncrement} />
    </div>
  );
}
function ChildComponent({ onIncrement }) {
  console.log("ChildComponent重新渲染");
  return <button onClick={onIncrement}>Increment</button>;
}
```

- 在这个示例中，`ParentComponent`中有一个状态`count`和一个通过`useCallback`缓存的函数`handleIncrement`。这个函数用于增加`count`的值，并且它的依赖项是`count`。只有当`count`发生变化时，`handleIncrement`函数才会被重新创建。`ParentComponent`将`handleIncrement`函数作为属性`onIncrement`传递给`ChildComponent`。在`ChildComponent`中，每次接收新的`onIncrement`函数时会打印日志。由于`useCallback`的作用，只有当`count`变化导致`handleIncrement`函数重新创建时，`ChildComponent`才会重新渲染，避免了因为`ParentComponent`的其他无关状态变化而导致`ChildComponent`不必要的重新渲染。
