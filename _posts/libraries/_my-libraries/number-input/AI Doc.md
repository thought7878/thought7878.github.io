### 概述


`NumberField` 是一个复合型组件，用于创建具有自定义按钮（增减）、标签和输入框功能的数字输入框。它基于 `react-aria` 和 `react-stately` 库构建，提供了高度可定制的用户体验和无障碍性。本组件集包括以下子组件：

- **NumberField**
- **NumberFieldInput**
- **NumberFieldLabel**
- **NumberFieldGroup**
- **NumberFieldIncrement**
- **NumberFieldDecrement**

### 安装与导入

确保已经安装了 `react-aria` 和 `react-stately` 相关依赖。


```bash
npm install react-aria react-stately
```

然后在你的组件文件中导入所需的模块：


```typescript

import { NumberField, NumberFieldInput, NumberFieldLabel, NumberFieldGroup, NumberFieldIncrement, NumberFieldDecrement } from 'your-component-library';

```

### 基本用法

```typescript
<NumberField name="myNumber" defaultValue={1}>   <NumberFieldInput />   <NumberFieldLabel>My Number</NumberFieldLabel>   <NumberFieldGroup>     <NumberFieldIncrement>+</NumberFieldIncrement>     <NumberFieldDecrement>-</NumberFieldDecrement>   </NumberFieldGroup> </NumberField>
```

### 组件特性

#### NumberField

- **name**: 输入框的 `name` 属性。
- **defaultValue**: 数字输入框的初始值。
- **btnPosition**: 按钮位置，可选 `'inside'` 或 `'outside'`，默认为 `'inside'`。
- **labelPosition**: 标签位置，可选 `'left'` 或 `'top'`，默认为 `'left'`。
- **locale**: 自定义区域设置。
- **className**: 外部容器的类名。

#### NumberFieldInput

- **className**: 输入框的额外类名。

#### NumberFieldLabel

- **className**: 标签的额外类名。

#### NumberFieldGroup

- **className**: 包裹按钮的容器的额外类名。

#### NumberFieldIncrement & NumberFieldDecrement

- **className**: 按钮的额外类名。

### 高级配置

- 使用 `NumberFieldGroup` 来包裹按钮，以实现更灵活的布局控制。
- 利用 `useNumberFieldContext` 钩子在自定义组件中获取 `NumberField` 上下文。

### 测试示例

参见上方单元测试代码段，演示了如何使用测试库来验证组件的基本功能，如渲染验证、点击增减按钮改变数值等。

### 注意事项

- 确保正确处理每个组件的上下文使用，特别是自定义组件集成时。
- 根据具体需求调整样式类名 (`cn` 函数使用需确保传入正确的样式库支持)。
- 对于复杂的国际化场景，考虑提供或覆盖默认的 `locale` 参数。

通过组合这些组件，开发者能够创建出既符合设计需求又具有良好无障碍性的数字输入界面。