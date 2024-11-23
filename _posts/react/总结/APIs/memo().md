为什么使用React的memo()？React的memo()是什么？

# Why
- **性能优化**
    - **减少不必要的重新渲染**：在 React 应用中，*组件重新渲染是一个比较消耗性能的操作。当一个父组件重新渲染时，它的所有子组件也会默认重新渲染。但是，在很多情况下，子组件的属性并没有发生变化，这种重新渲染是不必要的*。例如，在一个包含列表组件和列表项组件的应用中，当列表组件的某些状态（如排序方式）发生变化，但列表项组件的属性（如单个项目的内容）没有改变时，使用`memo()`包裹列表项组件可以避免列表项组件的不必要重新渲染，从而提高性能。
    - **优化复杂组件树的渲染效率**：在大型的、复杂的组件树结构中，组件之间的嵌套层次较多。如果不进行优化，一个高层级组件的状态变化可能会导致大量底层组件的不必要重新渲染。`memo()`可以在这个复杂的组件树中，针对那些对属性变化比较敏感的组件进行优化，通过记忆组件的渲染结果，只有当组件的属性真正发生变化时才重新渲染，有效地减少了整个组件树的渲染开销。
- **控制组件的渲染行为**
    - **精确控制重新渲染条件**：`memo()`允许开发者明确地定义组件重新渲染的条件。默认情况下，它通过浅比较（shallow comparison）组件的属性来判断是否需要重新渲染。开发者也可以自定义比较函数来更精细地控制重新渲染的条件。例如，对于一个接收复杂对象作为属性的组件，如果默认的浅比较不能满足需求（如需要比较对象内部的某些属性），可以通过自定义比较函数来决定组件是否重新渲染。

# What

`memo()`是 React 提供的一个**高阶组件**（Higher - Order Component，HOC）。它用于对函数组件进行记忆化（memoization），**可以避免在组件的属性（props）没有发生变化的情况下重新渲染组件**。

# How

**基本语法**：
```jsx
const MemoizedComponent = memo(OriginalComponent, compareFunction);
```
- `OriginalComponent`：是要进行记忆化的原始函数组件。
- `compareFunction`：是一个可选的比较函数。这个函数用于*比较新的属性和旧的属性，以决定组件是否需要重新渲染*。如果不提供这个函数，`memo()`会*使用浅比较来检查属性是否变化*。

**示例（默认浅比较）**：

```jsx
import React, { memo } from'react';
const ChildComponent = memo(({ value }) => {
  return (
    <div>{value}</div>
  );
});
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [value, setValue] = useState('Initial Value');
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        改变计数（不会影响ChildComponent）
      </button>
      <button onClick={() => setValue('New Value')}>
        改变值（会影响ChildComponent）
      </button>
      <ChildComponent value={value} />
    </div>
  );
}
```

在这个示例中，`ChildComponent`被`memo()`包裹。在`ParentComponent`中，有两个状态`count`和`value`，以及两个按钮用于改变这两个状态。`ChildComponent`接收`value`作为属性。当点击 “改变计数” 按钮时，`count`状态改变，`ParentComponent`重新渲染，但由于`ChildComponent`的属性`value`没有变化，`memo()`通过浅比较发现属性未变，所以`ChildComponent`不会重新渲染。当点击 “改变值” 按钮时，`value`状态改变，`ChildComponent`的属性发生变化，此时`ChildComponent`会重新渲染。

**示例（自定义比较函数）**：

```jsx
import React, { memo } from'react';
//
const compareProps = (prevProps, nextProps) => {
  // 比较两个对象的特定属性是否相等
  return prevProps.value === nextProps.value && prevProps.otherValue === nextProps.otherValue;
};
//
const CustomChildComponent = memo(({ value, otherValue }) => {
  return (
    <div>{value} - {otherValue}</div>
  );
}, compareProps);

function ParentComponentWithCustom() {
  const [count, setCount] = useState(0);
  const [value, setValue] = useState('Initial Value');
  const [otherValue, setOtherValue] = useState('Other Initial Value');
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        改变计数（不会影响CustomChildComponent）
      </button>
      <button onClick={() => setValue('New Value')}>
        改变值
      </button>
      <button onClick={() => setOtherValue('New Other Value')}>
        改变其他值
      </button>
      <CustomChildComponent value={value} otherValue={otherValue} />
    </div>
  );
}
```

  在这个示例中，`CustomChildComponent`使用了自定义的比较函数`compareProps`。这个函数比较了`value`和`otherValue`两个属性。在`ParentComponentWithCustom`中，通过三个按钮可以改变三个状态。只有当`value`和`otherValue`两个属性都发生变化时，`CustomChildComponent`才会重新渲染，通过这种方式更精细地控制了组件的重新渲染行为。