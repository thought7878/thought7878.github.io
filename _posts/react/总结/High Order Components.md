### React高阶组件（HOC）的核心概念  
`高阶组件（Higher-Order Component，HOC）`是 React 中**复用组件逻辑**的一种高级模式。它的本质是一个*函数*，*接收*一个组件并*返回*一个新的增强组件，类似于函数式编程中的*高阶函数*。

---

### 高阶组件的作用  
- **逻辑复用**：将通用逻辑（如数据获取、权限校验、日志记录）抽离成独立函数，避免代码重复。  
- **增强组件能力**：向组件注入额外 props 或修改行为（如添加样式、监听事件）。  
- **控制渲染**：根据条件决定是否渲染组件（如权限控制）。  

---

### 最简单的实现
#### 最简单的 HOC 示例  
```jsx  
// 定义一个高阶组件函数  
function withLogger(WrappedComponent) {  
  // 返回一个新组件  
  return function EnhancedComponent(props) {  
    // 添加日志逻辑  
    console.log("组件渲染:", WrappedComponent.name);  
    // 返回原始组件并透传 props  
    return <WrappedComponent {...props} />;  
  };  
}  

// 使用 HOC  
const Button = (props) => <button>{props.label}</button>;  
const ButtonWithLogger = withLogger(Button);  

// 渲染增强后的组件  
<ButtonWithLogger label="点击" />  
```

---

### 常见应用场景  

#### 场景一：注入额外 Props  
```jsx  
// 高阶组件：向组件注入用户信息  
function withUser(WrappedComponent) {  
  return function (props) {  
    const [user] = useState({ name: "Alice" });  
    // 将 user 作为新 prop 传递  
    return <WrappedComponent {...props} user={user} />;  
  };  
}  

// 使用  
const Profile = ({ user }) => <div>{user.name}</div>;  
const ProfileWithUser = withUser(Profile);  
```

#### 场景二：条件渲染控制（权限校验）
```jsx  
// 高阶组件：权限校验  
function withAuth(WrappedComponent) {  
  return function (props) {  
    const isAuthenticated = checkAuth(); // 权限检查逻辑  
    return isAuthenticated ? <WrappedComponent {...props} /> : <div>无权访问</div>;  
  };  
}  

// 使用  
const AdminPanel = () => <div>管理员面板</div>;  
const ProtectedAdminPanel = withAuth(AdminPanel);  
```

#### 场景三：数据获取与注入  
```jsx  
// 高阶组件：获取数据并传递  
function withDataFetching(url) {  
  return function (WrappedComponent) {  
    return class extends React.Component {  
      state = { data: null, loading: true };  

      componentDidMount() {  
        fetch(url)  
          .then((res) => res.json())  
          .then((data) => this.setState({ data, loading: false }));  
      }  

      render() {  
        return <WrappedComponent {...this.props} {...this.state} />;  
      }  
    };  
  };  
}  

// 使用  
const DataDisplay = ({ data, loading }) => (  
  loading ? <div>加载中...</div> : <div>{data}</div>  
);  
const DataDisplayWithFetch = withDataFetching("/api/data")(DataDisplay);  
```

---

### 高阶组件的链式调用  
HOC 可以通过链式调用组合多个增强功能：  
```jsx  
const EnhancedComponent = withRouter(  // 注入路由 props  
  withStyles(styles)(               // 注入样式  
    withAuth(                       // 权限校验  
      MyComponent  
    )  
  )  
);  
```

---

### 注意事项  

#### 透传 Refs  
若需在 HOC 中保留原始组件的 `ref`，需使用 `React.forwardRef`：  
```jsx  
function withLogger(WrappedComponent) {  
  return React.forwardRef((props, ref) => {  
    return <WrappedComponent {...props} ref={ref} />;  
  });  
}  
```

#### 避免 Props 命名冲突  
HOC 注入的 props 可能与原始组件的 props 重名，需明确命名约定：  
```jsx  
function withUser(WrappedComponent) {  
  return function (props) {  
    const injectedProps = { user: { name: "Alice" } };  
    return <WrappedComponent {...props} {...injectedProps} />;  
  };  
}  
```

#### 正确显示组件名称  
HOC 包裹后的组件在开发者工具中可能显示为匿名组件，可通过*设置 `displayName` 优化调试：*
```jsx  
function withLogger(WrappedComponent) {  
  function EnhancedComponent(props) {  
    /* ... */  
  }  
  EnhancedComponent.displayName = `WithLogger(${WrappedComponent.name})`;  
  return EnhancedComponent;  
}  
```

---

### 高阶组件 vs Hooks  
- **HOC** 更适用于*类组件*或需要包装组件树的场景（如添加 Provider）。  
- **Hooks** 更适合函数组件内的逻辑复用（如 `useEffect`, `useState`）。  

---

### 总结  
- **HOC 的核心价值**：***通过组合而非继承实现逻辑复用***。  
- **适用场景**：跨组件共享逻辑（如权限、数据获取、埋点）。  
- **关键原则**：保持 HOC 的纯净性（不修改原始组件，仅组合）。  
- **替代方案**：在函数组件中优先考虑自定义 Hooks。  

通过 HOC 可以大幅提升代码的可维护性，但需注意*避免过度抽象导致组件层级过深*！