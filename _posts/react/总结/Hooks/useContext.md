
# Why
- **简化跨组件层级的数据共享**
    - **避免层层传递 Props**：在 React 应用中，当数据需要在多个不同层级的组件之间共享时，传统的方式是通过属性（Props）层层传递。这种方式在组件层级较深或者多个组件都需要相同数据时会变得非常繁琐。例如，在一个具有主题切换功能的应用中，主题相关的信息（如颜色主题、字体主题等）需要被应用中的多个组件使用，包括顶层的布局组件、中间的内容展示组件以及底层的按钮、文本等组件。如果使用 Props 传递，主题信息需要从顶层组件开始，经过中间的各个组件，一层一层地传递到需要使用该信息的底层组件，这使得代码变得臃肿且难以维护。`useContext`提供了一种跨层级共享数据的方式，通过创建一个 Context 并使用`useContext`，任何需要主题信息的组件都可以直接获取，而无需中间组件的层层传递。
    - **全局状态管理的便捷性**：对于一些全局状态（如用户认证状态、应用语言设置等），`useContext`可以方便地让多个组件访问这些状态。例如，在一个多语言应用中，语言设置状态可以放在一个 Context 中，各个组件通过`useContext`获取当前语言设置，从而根据语言来展示不同的文本内容。
- **增强组件的可复用性**
    - **解耦组件与数据提供源**：当组件使用`useContext`获取数据时，它不再依赖于特定的父组件来接收数据。这使得组件在不同的上下文中更易于复用。例如，一个显示用户信息的组件，如果通过`useContext`获取用户信息，那么它可以在应用的不同页面或者不同模块中使用，只要这些地方提供了相同的用户信息 Context，而不需要针对每个使用场景重新配置 Props 传递。

# What

`useContext`是 React 提供的一个 Hook，用于在函数组件中访问 React Context（上下文）的值。它允许组件订阅一个 Context 对象，并在组件渲染时获取该 Context 的当前值。

# How

 **基本步骤**：
- **创建 Context**：首先需要使用`React.createContext`创建一个 Context 对象。这个对象可以有一个默认值，用于在没有提供相应 Provider 时使用。例如，`const ThemeContext = React.createContext('light');`创建了一个名为`ThemeContext`的 Context，默认主题为`light`。
- **提供 Context 值（使用 Provider）**：在组件树的较高层级（通常是根组件或者接近根组件的层级），使用`Context.Provider`来提供 Context 的值。例如，在一个主题管理的应用中：

```jsx
function App() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={theme}>
      <Layout />
    </ThemeContext.Provider>
  );
}
```


- **在组件中使用`useContext`获取值**：在需要使用 Context 值的函数组件中，使用`useContext`来获取值。例如，一个按钮组件可以这样获取主题信息：

```jsx
function ButtonComponent() {
  const theme = useContext(ThemeContext);
  // 根据主题设置按钮的样式
  const buttonStyle = theme === 'light'? { backgroundColor: 'white' } : { backgroundColor: 'black' };
  return (
    <button style={buttonStyle}>Click me</button>
  );
}
```

- **示例说明**
    - 在这个示例中，`App`组件通过`ThemeContext.Provider`提供了主题值（`theme`）。`ButtonComponent`使用`useContext`获取这个主题值，并根据主题值来设置按钮的样式。这样，无论`ButtonComponent`在组件树中的位置有多深，只要它在`ThemeContext.Provider`的范围内，就可以获取到主题信息，实现了跨层级的数据共享和方便的样式设置。
