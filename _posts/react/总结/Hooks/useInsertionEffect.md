
# Why
- **支持 CSS-in-JS 库的样式注入**
    - **解决样式覆盖和优先级问题**：在使用 CSS-in-JS 库（如 styled-components）时，样式的注入顺序很关键。如果样式注入太晚，可能会导致样式覆盖问题或者样式优先级不符合预期。例如，当一个组件的样式依赖于另一个组件的样式注入顺序时，可能会出现样式冲突。`useInsertionEffect`允许 CSS-in-JS 库在 DOM 更新之前插入样式，确保样式能够按照正确的顺序注入，避免样式冲突和优先级混乱。
    - **优化样式加载性能**：在一些复杂的应用中，可能有大量的样式需要动态加载和更新。通过`useInsertionEffect`，CSS-in-JS 库可以在合适的时机（DOM 更新之前）高效地插入样式，使得样式能够及时生效，减少因为样式加载延迟而导致的视觉闪烁等性能问题。例如，在一个具有主题切换功能的应用中，当主题切换时，新的主题样式可以通过`useInsertionEffect`快速地注入到 DOM 中，让用户能够快速看到主题变化后的效果。
- **隔离样式注入和组件渲染逻辑**
    - **保持组件逻辑的纯净性**：在组件内部，主要的逻辑应该集中在数据处理、状态管理和 UI 渲染上。如果将样式注入逻辑与这些主要逻辑混合在一起，可能会使组件代码变得混乱。`useInsertionEffect`提供了一个专门的地方来处理样式注入，使得组件的主要逻辑和样式注入逻辑分离。例如，在一个包含多个嵌套组件的复杂应用中，每个组件可以使用`useInsertionEffect`来独立地处理自己的样式注入，而不会干扰组件的其他功能，提高了组件代码的可维护性。

# What

`useInsertionEffect`是 React 提供的一个 Hook，它主要用于*在 DOM 更新之前，让 CSS-in-JS 库有机会在 DOM 中插入样式*。这个 Hook 的执行时机非常早，在组件的布局和绘制之前，确保样式能够正确地应用于即将更新的 DOM 元素。

# How
**基本语法**：
```jsx
useInsertionEffect(() => { 
// 样式注入逻辑 
}, [dependency1, dependency2,...]);
```
- 第一个参数是一个函数，这个函数包含了样式注入的具体逻辑，比如调用 CSS-in-JS 库的样式注入方法。
- 第二个参数是一个依赖项数组，它用于控制样式注入函数的执行时机。如果依赖项数组中的任何一个元素发生变化，样式注入函数就会重新执行。

**示例**：

```jsx
import React, { useInsertionEffect } from'react';
import { insertStyles } from'styled-components';
function StyledComponent() {
  useInsertionEffect(() => {
    const styles = `
     .my-styled-component {
        color: red;
      }
    `;
    insertStyles(styles);
  }, []);
  return (
    <div className="my-styled-component">
      这是一个样式化的组件
    </div>
  );
}
```

**示例说明**：

在这个`StyledComponent`中，`useInsertionEffect`用于在 DOM 更新之前注入样式。在样式注入函数中，定义了一个简单的 CSS 样式字符串，然后使用`styled-components`库的`insertStyles`方法将样式注入到 DOM 中。依赖项数组为空（`[]`），这意味着样式注入函数只会在组件首次渲染时执行一次，确保了样式能够在组件的 DOM 元素更新之前被正确注入，使得组件能够正确地显示为红色文字。
