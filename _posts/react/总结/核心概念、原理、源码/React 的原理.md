
## 声明式编程
React 是如何构建 UI 的？

React 的**核心思想**是`声明式编程`，开发者*只需描述 UI 应该是什么样子*。*而不是命令式地操作 DOM*。开发者通过*编写组件来定义 UI 的结构和行为，React 负责将这些组件“翻译”成实际的 DOM 元素，并管理它们的状态变化*。

```jsx
// 声明式：描述 UI 状态
function Counter({ count }) {
  return <div>Count: {count}</div>;
}

// 对比命令式（传统 DOM 操作）
document.getElementById('counter').textContent = 'Count: ' + count;
```


### 声明式 vs 命令式

| 类型      | 描述                        | 示例                                                     |
| ------- | ------------------------- | ------------------------------------------------------ |
| **命令式** | 手动操作 DOM，告诉浏览器“怎么做”       | `document.getElementById("title").innerText = "Hello"` |
| **声明式** | 描述 UI 应该长什么样，React 决定怎么更新 | `<h1>Hello</h1>`                                       |

