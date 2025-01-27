在 React 中，**Ref（引用）** 是一种用于*直接访问 DOM 节点或保存可变值*（且不触发组件重新渲染）的机制。

**Ref 的主要用途包括：**

1. **直接操作 DOM 元素**（如聚焦输入框、测量元素尺寸）。
2. **保存与渲染无关的可变值**（如计时器 ID、缓存数据）。
3. **访问子组件的实例或 DOM 节点**（对于类组件或通过 `forwardRef` 包装的函数组件）。

---

### 1. 创建 Ref 的两种方式

#### (1) `useRef`（函数组件）
```jsx
import { useRef } from 'react';

function MyComponent() {
  const inputRef = useRef(null); // 初始化一个空的 Ref

  const handleClick = () => {
    inputRef.current.focus(); // 操作 DOM
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
}
```

#### (2) `createRef`（类组件）
```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.inputRef = React.createRef(); // 初始化 Ref
  }

  handleClick = () => {
    this.inputRef.current.focus();
  };

  render() {
    return (
      <div>
        <input ref={this.inputRef} type="text" />
        <button onClick={this.handleClick}>Focus Input</button>
      </div>
    );
  }
}
```

---

### 2. Ref 的常见用法

#### (1) 访问 DOM 元素
```jsx
function AutoFocusInput() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus(); // 组件挂载后自动聚焦
  }, []);

  return <input ref={inputRef} type="text" />;
}
```

#### (2) 保存可变值（不触发重新渲染）
```jsx
function Timer() {
  const intervalRef = useRef(null);

  const startTimer = () => {
    intervalRef.current = setInterval(() => {
      console.log("Timer running...");
    }, 1000);
  };

  const stopTimer = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}
```

#### (3) 访问子组件的 DOM 或实例（需 `forwardRef`）
```jsx
// 子组件（函数组件）
const ChildComponent = React.forwardRef((props, ref) => {
  return <input ref={ref} type="text" />;
});

// 父组件
function ParentComponent() {
  const childInputRef = useRef(null);

  const handleFocus = () => {
    childInputRef.current.focus();
  };

  return (
    <div>
      <ChildComponent ref={childInputRef} />
      <button onClick={handleFocus}>Focus Child Input</button>
    </div>
  );
}
```

---

### 3. 注意事项

#### (1) Ref 与 State 的区别
- **Ref**：修改 `ref.current` **不会触发重新渲染**，*适合存储与渲染无关的数据*。
- **State**：修改状态会触发重新渲染，*适合存储与 UI 相关的数据*。

#### (2) 不要滥用 Ref
- 优先使用 React 的声明式 API（如通过 `state` 控制 UI）。
- 仅在需要直接操作 DOM 或保存不涉及渲染的值时使用 Ref。

#### (3) 避免在渲染期间修改 Ref
```jsx
// ❌ 错误：在渲染期间修改 Ref
function BadExample() {
  const ref = useRef(0);
  ref.current++; // 可能导致不可预期的行为
  return <div>{ref.current}</div>;
}

// ✅ 正确：在事件或副作用中修改 Ref
function GoodExample() {
  const ref = useRef(0);

  useEffect(() => {
    ref.current++;
  }, []);

  return <div>{ref.current}</div>;
}
```

---

### 4. 其他 Ref 相关 API

#### (1) 回调 Ref（动态绑定 Ref）
```jsx
function CallbackRefExample() {
  const inputRef = useRef(null);

  const setRef = (element) => {
    inputRef.current = element; // 手动绑定 DOM 元素到 Ref
  };

  return <input ref={setRef} type="text" />;
}
```

#### (2) `useImperativeHandle`（控制暴露给父组件的 Ref）
```jsx
const ChildComponent = React.forwardRef((props, ref) => {
  const inputRef = useRef(null);

  // 仅暴露 focus 方法给父组件
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
  }));

  return <input ref={inputRef} type="text" />;
});

// 父组件
function ParentComponent() {
  const childRef = useRef(null);

  return (
    <div>
      <ChildComponent ref={childRef} />
      <button onClick={() => childRef.current.focus()}>Focus Child</button>
    </div>
  );
}
```

---

### 总结
- **Ref 的核心作用**：操作 DOM 和保存不触发渲染的可变值。
- **函数组件**优先使用 `useRef`，类组件使用 `createRef`。
- 通过 `forwardRef` 和 `useImperativeHandle` 可以更安全地*传递 Ref*。
- 避免在渲染期间直接修改 `ref.current`，*应在事件或副作用中操作*。