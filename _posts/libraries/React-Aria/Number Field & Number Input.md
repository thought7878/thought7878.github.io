# useNumberFieldState

`react-stately` 是一个由 Adobe 开发的库，它为 UI 组件提供了可复用的状态管理和行为逻辑。`react-stately` 并*不直接处理渲染逻辑，而是提供状态管理功能*，可以被不同的 UI 框架和库（如 React, Vue, Angular 等）使用。

`useNumberFieldState` 是 `react-stately` 中的一个 Hook，用于管理数字输入字段（Number Field）的状态。这个 Hook 通常与 `NumberField` 组件一起使用，该组件可以是任何框架的实现，比如 `@react-aria/numberfield` 提供了基于 ARIA 的 NumberField 的行为。

### `useNumberFieldState` 的主要功能包括：
1. **状态管理**：管理数值、最小值、最大值、步长等。
2. **验证**：确保输入的值在允许的范围内，并符合指定的格式。
3. **响应式更新**：当状态改变时触发更新。

### 使用示例：
```jsx
import { useNumberFieldState } from '@react-stately/numberfield';

function MyNumberField() {
  let state = useNumberFieldState({
    minValue: 0,
    maxValue: 100,
    defaultValue: 50
  });

  // ... 接下来你可以使用 state 来控制 NumberField 的行为
}
```

在这个例子中，`useNumberFieldState` 创建了一个数字输入字段的状态，设置了最小值、最大值和默认值。然后你可以使用返回的状态对象来控制和响应数值的变化。

请注意，为了完整地实现一个可交互的数字输入框，你还需要使用来自 `@react-aria/numberfield` 的 `useNumberField` Hook 来生成必要的 ARIA 属性，以及一些 DOM 元素来渲染实际的界面。

## NumberFieldStateOptions
`NumberFieldStateOptions` 是 `react-stately` 库中的一个类型，它定义了 `useNumberFieldState` Hook 的配置选项。这些选项用于初始化数字输入字段的状态，并控制其行为。

### `NumberFieldStateOptions` 类型通常包含以下属性：

1. **`value`**: (`number | undefined`) 当前数值。如果未设置，则使用 `defaultValue` 或 `minValue`。
2. **`defaultValue`**: (`number | undefined`) 默认数值，当 `value` 未设置时使用。
3. **`minValue`**: (`number | undefined`) 数值的最小限制。
4. **`maxValue`**: (`number | undefined`) 数值的最大限制。
5. **`step`**: (`number | undefined`) 数值变化的步长。
6. **`isDisabled`**: (`boolean`) 是否禁用输入字段。
7. **`isRequired`**: (`boolean`) 是否是必填项。
8. **`isValid`**: (`boolean | undefined`) 是否有效。如果未设置，则会根据 `minValue` 和 `maxValue` 进行验证。
9. **`formatOptions`**: (`Intl.NumberFormatOptions | undefined`) 国际化格式化选项，用于显示和解析数值。
10. **`parseOptions`**: (`Object | undefined`) 自定义解析选项，用于解析用户输入的字符串。
11. **`onChange`**: (`(value: number | undefined) => void`) 值变化时的回调函数。

### 示例代码：

```jsx
import { useNumberFieldState } from '@react-stately/numberfield';

function MyNumberField() {
  const state = useNumberFieldState({
    minValue: 0,
    maxValue: 100,
    step: 1,
    defaultValue: 50,
    isDisabled: false,
    isRequired: true,
    formatOptions: { style: 'decimal', minimumFractionDigits: 0, maximumFractionDigits: 0 },
  });

  // ... 使用 state 来控制 NumberField 的行为
}
```

在这个示例中，我们设置了 `minValue`、`maxValue` 和 `defaultValue`，并定义了一些其他属性，例如是否禁用、是否必填，以及如何格式化数值。

通过使用 `useNumberFieldState` 和相应的配置选项，你可以创建一个具有特定行为和格式的数字输入字段。这些配置选项使你能够根据需要定制输入字段的行为。

