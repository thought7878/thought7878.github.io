`React.ComponentPropsWithoutRef<T>` 是一个 TypeScript 实用工具类型，用于获取一个给定 React 组件的 props 类型，但不包括 `ref` 属性。

### 用途
当你需要访问一个组件的 props 类型，但不想包含 `ref` 属性时，可以使用 `React.ComponentPropsWithoutRef`。这在编写通用函数或高阶组件时非常有用，特别是在需要处理组件的 props 但又不想处理 `ref` 时。

### 语法
```typescript
React.ComponentPropsWithoutRef<T>
```

其中 `T` 是一个 React 元素类型。

### 示例
假设我们有一个自定义的 React 组件 `MyButton`，我们希望获取该组件的 props 类型，但不包括 `ref` 属性。

#### 自定义组件 `MyButton`
```jsx
import React from 'react';

interface MyButtonProps {
  label: string;
  onClick: () => void;
}

const MyButton: React.FC<MyButtonProps> = ({ label, onClick }) => {
  return (
    <button onClick={onClick}>
      {label}
    </button>
  );
};

export default MyButton;
```

#### 使用 `React.ComponentPropsWithoutRef`
假设我们需要创建一个函数，该函数接收 `MyButton` 的 props 作为参数，但不处理 `ref`。

```typescript
import React from 'react';
import MyButton from './MyButton';

function logButtonProps(props: React.ComponentPropsWithoutRef<typeof MyButton>) {
  console.log('Label:', props.label);
  console.log('Click handler:', props.onClick);
}

const buttonProps = {
  label: 'Click me',
  onClick: () => console.log('Button clicked'),
};

logButtonProps(buttonProps);
```

### 总结
`React.ComponentPropsWithoutRef<T>` 是一个 TypeScript 实用工具类型，用于获取一个给定 React 组件的 props 类型，但不包括 `ref` 属性。这在处理组件的 props 时非常有用，特别是当你需要确保类型安全的同时避免处理 `ref` 相关的逻辑。