在 React 18 中，JSX 会被 Babel 等编译工具转换为特定的 JavaScript 函数调用，具体取决于编译配置（经典运行时 `classic` 或自动运行时 `automatic`）。以下是详细说明：

---

### 1. React 17 之前的经典转换（Classic Runtime）
在 React 17 之前，*JSX 会被转换为 `React.createElement` 调用*。例如：
*大于两个参数：标签名/函数名、props、...children*
```jsx
// JSX
const element = <div className="container">Hello</div>;

// 编译后
const element = React.createElement(
  'div',
  { className: 'container' },
  'Hello'
);
```

- **特点**：需要手动导入 `React`，否则会报错。
- **Babel 配置**：默认使用经典模式（需显式配置 `@babel/preset-react`）。

---

### 2. React 17+ 的自动运行时转换（Automatic Runtime）
从 React 17 开始，引入了新的 JSX 转换方式，**无需手动导入 `React`**。*JSX 会被转换为从 `react/jsx-runtime` 引入的 `jsx` 或 `jsxs` 函数：*
*两个参数：标签名/函数名、props（含children）*

```jsx
// JSX
const element = <div className="container">Hello</div>;

// 编译后
import { jsx as _jsx } from 'react/jsx-runtime';
const element = _jsx('div', {
  className: 'container',
  // 含children
  children: 'Hello'
});
```

- **jsx vs jsxs**：
  - `jsx`：处理*单个子元素*或动态子元素。
  - `jsxs`（*带 "s"*）：处理静态*多个子元素*，优化渲染性能（如 `children` 是数组时）。
  
  例如：
  ```jsx
  // 多个静态子元素
  const list = (
    <ul>
      <li>A</li>
      <li>B</li>
    </ul>
  );

  // 编译后使用 jsxs
  import { jsxs as _jsxs } from 'react/jsx-runtime';
  const list = _jsxs('ul', {
    children: [
      _jsx('li', { children: 'A' }),
      _jsx('li', { children: 'B' })
    ]
  });
  ```

- **Babel 配置**：需在 `@babel/preset-react` 中设置 `runtime: 'automatic'`：
  ```json
  {
    "presets": [
      ["@babel/preset-react", {
        "runtime": "automatic" // 启用自动运行时
      }]
    ]
  }
  ```

---

### 3. React 18 的更新
React 18 延续了自动运行时的转换方式，但内部对 `jsx` 函数进行了优化：
- **支持 Concurrent Features**：新的转换逻辑更适配并发渲染（如 `Suspense`、流式 SSR）。
- **开发模式增强**：自动注入调试信息（如 `_jsxDEV` 函数），提供更详细的错误堆栈。

---

### 4. 编译后的代码示例
假设有以下 JSX：
```jsx
function App() {
  return (
    <div>
      <h1>Hello React 18</h1>
      <p>JSX Transformation</p>
    </div>
  );
}
```

**编译后（自动运行时）**：
```javascript
import { jsxs as _jsxs, jsx as _jsx } from 'react/jsx-runtime';

function App() {
  return _jsxs('div', {
    children: [
      _jsx('h1', { children: 'Hello React 18' }),
      _jsx('p', { children: 'JSX Transformation' })
    ]
  });
}
```

---

### 5. 关键总结
- **无需手动导入 React**：自动运行时模式下，Babel 自动从 `react/jsx-runtime` 引入所需函数。
- **性能优化**：`jsx`/`jsxs` 内部优化了子元素处理和静态树生成。
- **兼容性**：React 18 同时支持经典和自动运行时，但推荐使用自动运行时。

---

### 配置参考
确保 Babel 或 TypeScript 配置为自动运行时：
```json
// Babel
{
  "presets": [
    ["@babel/preset-react", { "runtime": "automatic" }]
  ]
}

// TypeScript (tsconfig.json)
{
  "compilerOptions": {
    "jsx": "react-jsx",        // React 17+ 自动模式
    "jsxImportSource": "react" // 默认从 'react' 导入
  }
}
```