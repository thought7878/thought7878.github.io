在 React 生态中，**Headless（无头）组件**是一种设计模式：组件**只提供功能逻辑和状态管理，不包含任何 UI 渲染代码**。开发者可以完全自定义组件的外观，同时复用其核心逻辑。这种模式在构建高度可定制的 UI 组件库时非常有用。

---

### 核心概念
- **逻辑与 UI 分离**：Headless 组件*负责处理状态、交互逻辑*（如表单验证、数据获取、键盘事件等），但将 UI 渲染交给开发者。
- **灵活性**：开发者可以*完全控制 UI 的样式、结构*、交互细节。
- **可组合性**：通常通过 Hook 或 render props 提供状态和方法。

---

### 常见使用场景
1. **设计系统**：需要统一逻辑但允许不同 UI 主题时。
2. **复杂交互组件**：如表单、下拉菜单、模态框、数据表格等。
3. **跨平台开发**：逻辑复用，UI 根据平台适配（Web、React Native）。

---

### 实现 Headless 组件的两种方式

#### 1. 使用 Hook（推荐）
*通过自定义 Hook 暴露状态、方法，UI 由开发者自行实现*。

```jsx
// useCounter.js
import { useState } from 'react';

export function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(prev => prev + 1);
  const decrement = () => setCount(prev => prev - 1);
  const reset = () => setCount(0);

  return { count, increment, decrement, reset };
}
```

```jsx
// CounterUI.jsx
import { useCounter } from './useCounter';

export function Counter() {
  const { count, increment, decrement } = useCounter(5);

  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
}
```

#### 2. 使用 Render Props
通过 `children` 或 `render` 函数传递状态和方法。

```jsx
// CounterProvider.jsx
import { useState } from 'react';

export function CounterProvider({ children }) {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prev => prev + 1);
  const decrement = () => setCount(prev => prev - 1);

  return children({ count, increment, decrement });
}
```

```jsx
// 使用组件
<CounterProvider>
  {({ count, increment, decrement }) => (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  )}
</CounterProvider>
```

---

### 流行的 Headless 库

| 库名                                                         | 功能                  |
| ---------------------------------------------------------- | ------------------- |
| [React Hook Form](https://react-hook-form.com/)            | 表单验证                |
| [React Aria](https://react-spectrum.adobe.com/react-aria/) | 无障碍组件逻辑             |
| [Downshift](https://github.com/downshift-js/downshift)     | 交互式输入（如自动补全）        |
| [Headless UI](https://headlessui.dev/)                     | Tailwind CSS 官方无头组件 |
| [React Table](https://tanstack.com/table)                  | 数据表格                |

---

### 优点
- **完全控制 UI**：不受预设样式的限制。
- **主题无关**：*逻辑可复用*，无需依赖特定 UI 框架。
- **可维护性**：*逻辑与 UI 解耦，易于测试和维护*。

---

### 缺点
- **需要手动实现 UI**：相比完整组件库，开发工作量更大。
- **学习成本**：需要理解状态管理和交互逻辑的实现细节。

---

### 何时使用 Headless 组件？
- 需要与设计系统深度集成时。
- 现有 UI 库无法满足定制需求。
- 项目需要极致的性能优化（避免冗余渲染）。

---

### 示例：Headless 表单验证
```jsx
// useForm.js
import { useState } from 'react';

export function useForm(initialValues, validate) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate(values);
    setErrors(validationErrors);
    return Object.keys(validationErrors).length === 0;
  };

  return { values, errors, handleChange, handleSubmit };
}
```

```jsx
// 使用表单
function LoginForm() {
  const validate = (values) => {
    const errors = {};
    if (!values.email) errors.email = 'Email is required';
    if (!values.password) errors.password = 'Password is required';
    return errors;
  };

  const { values, errors, handleChange, handleSubmit } = useForm(
    { email: '', password: '' },
    validate
  );

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={values.email} onChange={handleChange} />
      {errors.email && <span>{errors.email}</span>}
      
      <input name="password" type="password" value={values.password} onChange={handleChange} />
      {errors.password && <span>{errors.password}</span>}
      
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

### 总结
Headless 组件是构建灵活 UI 的强大工具，适合需要深度定制的场景。通过分离逻辑与 UI，开发者可以复用经过验证的功能代码，同时保持设计的自由度。