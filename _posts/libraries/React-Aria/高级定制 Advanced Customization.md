[原文](https://react-spectrum.adobe.com/react-aria/advanced.html)

React Aria Components is built using a flexible and composable API that you can extend to build new patterns. If you need even more customizability, drop down to the lower level Hook-based API for even more control over rendering and behavior. Mix and match as needed.  
React Aria 组件使用灵活且可组合的 API 构建，您可以扩展该 API 以构建新模式。如果您需要更多的可定制性，请下拉到较低级别的基于 Hook 的 API，以便对渲染和行为进行更多的控制。根据需要混合和匹配。

## Contexts
---

The React Aria Components API is designed around composition. Components are reused between patterns to build larger composite components. For example, there is no dedicated `NumberFieldIncrementButton` or `SelectPopover` component. Instead, the standalone [Button](https://react-spectrum.adobe.com/react-aria/Button.html) and [Popover](https://react-spectrum.adobe.com/react-aria/Popover.html) components are reused within [NumberField](https://react-spectrum.adobe.com/react-aria/NumberField.html) and [Select](https://react-spectrum.adobe.com/react-aria/Select.html). This reduces the amount of duplicate styling code you need to write and maintain, and provides powerful composition capabilities you can use in your own components.  
React Aria Components API 是**围绕合成而设计的**。**组件在模式之间重用，以构建更大的复合组件**。*例如*，没有专用的 `NumberFieldIncrementButton` 或 `SelectPopover` 组件。*相反，独立的 Button 和 Popover 组件在 NumberField 和 Select 中重用*。***这减少了需要编写和维护的重复代码的数量，并提供了强大的组合功能***。

```tsx
<NumberField>
  <Label>Width</Label>
  <Group>
    <Input />
    <Button slot="increment">+</Button>
    <Button slot="decrement">-</Button>
  </Group>
</NumberField>
```

React Aria Components automatically provide behavior to their children by passing event handlers and other attributes via context. For example, the increment and decrement buttons in a `NumberField` receive `onPress` handlers that update the value. Keeping each element of a component separate enables full styling, layout, and DOM structure control, and contexts ensure that accessibility and behavior are taken care of on your behalf.  
**React Aria 组件通过 context 传递事件处理程序和其他属性，自动为其子组件提供行为**。*例如，`NumberField` 中的递增、递减按钮，接收更新值的 `onPress`*。**保持组件的每个元素独立，可以实现完整的样式、布局和 DOM 结构控制**，*上下文 context 可以确保可访问性和行为得到妥善处理*。

This architecture also enables you to reuse React Aria Components in your own custom patterns, or even replace one part of a component with your own custom implementation without rebuilding the whole pattern from scratch.  
**这种架构**还使您能够*在自己的自定义模式中重用 React Aria 组件，甚至可以使用自己的自定义实现替换组件的一部分，而无需从头开始重建整个模式*。

### Custom patterns
自定义模式

Each React Aria Component exports a corresponding context that you can use to build your own compositional APIs similar to the built-in components. You can send any prop or ref via context that you could pass to the corresponding component. The local props and ref on the component are merged with the ones passed via context, with the local props taking precedence (following the rules documented in [mergeProps](https://react-spectrum.adobe.com/react-aria/mergeProps.html)).  
**每个 React Aria 组件都导出一个对应的上下文，您可以使用它来构建自己的组合 API，类似于内置组件**。您可以通过上下文发送任何 prop 或 ref，并将其传递给相应的组件。**组件上的本地 props 和 ref 与通过上下文传递的 props 和 ref 合并，本地 props 优先**（遵循 mergeProps 中记录的规则）。

This example shows a `FieldGroup` component that renders a group of text fields. The entire group can be marked as disabled via the `isDisabled` prop, which is passed to all child text fields via the `TextFieldContext` provider.  
此示例显示一个呈现一组文本字段的 `FieldGroup` 组件。可以*通过 `isDisabled` 属性将整个组标记为禁用*，该属性通过 `TextFieldContext` provider *传递给所有子文本字段*。

```tsx
import {TextFieldContext} from 'react-aria-components';

interface FieldGroupProps {
  children?: React.ReactNode,
  isDisabled?: boolean
}

function FieldGroup({children, isDisabled}: FieldGroupProps) {
  return (
    <TextFieldContext.Provider value={{isDisabled}}>      
	    {children}
    </TextFieldContext.Provider>
  );
}
```

Any `TextField` component you place inside a `FieldGroup` will automatically receive the `isDisabled` prop from the group, including those that are deeply nested inside other components.  
放置在 `FieldGroup` 中的任何 `TextField` 组件*将自动接收 `isDisabled` 属性，包括那些深深嵌套在其他组件中的组件*。

```tsx
<FieldGroup isDisabled={isSubmitting}>
  <MyTextField label="Name" />
  <MyTextField label="Email" />
  <CreditCardFields />
</FieldGroup>
```

The contexts consumed by each component are listed in the Advanced Customization section of their documentation, along with examples of some potential use cases.  
每个组件使用的上下文都列在其文档的高级自定义部分中，沿着一些潜在用例的示例。

### Slots
插槽

Some patterns include multiple instances of the same component. These use the `slot` prop to distinguish each instance. Slots are named children within a component that can receive separate behaviors and [styles](https://react-spectrum.adobe.com/react-aria/styling.html#slots). Separate props can be sent to slots by providing an object with keys for each slot name to the component's context provider.  
某些模式包括同一组件的多个实例。它们使用 `slot`prop 来区分每个实例。插槽是组件中的命名子项，可以接收单独的行为和[样式](https://react-spectrum.adobe.com/react-aria/styling.html#slots) 。通过向组件的上下文提供者提供一个对象，该对象带有每个插槽名称的键，可以将单独的 prop 发送到插槽。

This example shows a `Stepper` component with slots for its increment and decrement buttons.  
此示例显示了一个 `Stepper` 组件，该组件具有用于其递增和递减按钮的插槽。

```tsx
function Stepper({children}) {
  let [value, setValue] = React.useState(0);

  return (
    <ButtonContext.Provider
      value={{
        slots: {
          increment: {
            onPress: () => setValue(value + 1)
          },
          decrement: {
            onPress: () => setValue(value - 1)
          }
        }
      }}>
      {children}
    </ButtonContext.Provider>
  );
}

<Stepper>
  <Button slot="increment">⬆</Button>
  <Button slot="decrement">⬇</Button>
</Stepper>
```

The slots provided by each built-in React Aria component are shown in the Anatomy section of their documentation.  
每个内置 React Aria 组件提供的插槽都显示在其文档的解剖部分中。

#### Default slot
默认插槽

The default slot is used to provide props to a component without specifying a slot name. This approach allows you to assign a default slot to a component for its default use case and enables you to specify a slot name for a specific use case.  
默认插槽用于向组件提供 props，而无需指定插槽名称。这种方法允许您为组件的默认用例分配一个默认插槽，并允许您为特定用例指定插槽名称。

This example shows a custom component that passes a specific class name to a standard button child and to a button child with a slot named "end".  
此示例显示了一个自定义组件，该组件将特定的类名传递给标准按钮子级和具有名为“end”的槽的按钮子级。

```tsx
import {Button, ButtonContext, DEFAULT_SLOT} from 'react-aria-components';

function MyCustomComponent({children}) {
  return (
    <ButtonContext.Provider
      value={{
        slots: {
          [DEFAULT_SLOT]: {
            className: "left-button"
          },
          end: {
            className: "right-button"
          }
        }
      }}>
      {children}
    </ButtonContext.Provider>
  );
}

<MyCustomComponent>
  {/* Consumes the props passed to the default slot */}
  <Button>Click me</Button>
</MyCustomComponent>

<MyCustomComponent>
  {/* Consumes the props passed to the "end" slot */}
  <Button slot="end">Click me</Button>
</MyCustomComponent>
```

### Provider

In complex components, you may need to provide many contexts. The `Provider` component is a utility that makes it easier to provide multiple React contexts without manually nesting them. This can be achieved by passing pairs of contexts and values as an array to the `values` prop.  
在复杂的组件中，您可能需要提供许多上下文。`Provider` 组件是一个实用程序，可以更轻松地提供多个 React 上下文，而无需手动嵌套它们。这可以通过将上下文和值对作为数组传递给`值` prop 来实现。

```tsx
import {Provider, ButtonContext, InputContext} from 'react-aria-components';

<Provider
  values={[
    [ButtonContext, {/* ... */}],
    [InputContext, {/* ... */}]
  ]}>
  {/* ... */}
</Provider>
```

This is equivalent to:  
这相当于：

```tsx
<ButtonContext.Provider value={{/* ... */}}>
  <InputContext.Provider value={{/* ... */}}>
    {/* ... */}
  </InputContext.Provider>
</ButtonContext.Provider>
```

### Consuming contexts

You can also consume from contexts provided by React Aria Components in your own custom components. This allows you to replace a component used as part of a larger pattern with a custom implementation. For example, you could consume from `LabelContext` in an existing styled label component to make it compatible with React Aria Components.  
您还可以在自己的自定义组件中使用 React Aria Components 提供的上下文。这使您可以用自定义实现替换作为较大模式一部分使用的组件。例如，你可以在现有的样式标签组件中使用 `LabelContext`，使其与 React Aria 组件兼容。

#### useContextProps

The 

`useContextProps`

 hook merges the local props and ref with the ones provided via context by a parent component. The local props always take precedence over the context values (following the rules documented in [mergeProps](https://react-spectrum.adobe.com/react-aria/mergeProps.html)). `useContextProps` supports the [slot](https://react-spectrum.adobe.com/react-aria/advanced.html#slots) prop to indicate which value to consume from context.  
`   useContextProps   ` 钩子将本地 props 和 ref 与父组件通过上下文提供的 props 和 ref 合并。本地 props 总是优先于上下文值（遵循 [mergeProps](https://react-spectrum.adobe.com/react-aria/mergeProps.html) 中记录的规则）。`useContextProps` 支持 [slot](https://react-spectrum.adobe.com/react-aria/advanced.html#slots)prop 来指示从上下文中使用哪个值。

```tsx
import type {LabelProps} from 'react-aria-components';
import {LabelContext, useContextProps} from 'react-aria-components';

const MyCustomLabel = React.forwardRef(
  (props: LabelProps, ref: React.ForwardedRef<HTMLLabelElement>) => {
    // Merge the local props and ref with the ones provided via context.
    [props, ref] = useContextProps(props, ref, LabelContext);
    // ... your existing Label component
    return <label {...props} ref={ref} />;
  }
);
```

Since it consumes from `LabelContext`, `MyCustomLabel` can be used within any React Aria component instead of the built-in `Label`.  
由于它从 `LabelContext 中`消耗，`MyCustomLabel` 可以在任何 React Aria 组件中使用，而不是内置的 `Label`。

```tsx
<TextField>
  <MyCustomLabel>Name</MyCustomLabel>  <Input />
</TextField>
```

#### useSlottedContext

To consume a context without merging with existing props, use the 

`useSlottedContext`

 hook. This works like React's `useContext`, and also accepts an optional slot argument to identify which slot name to consume.  
要使用上下文而不与现有 props 合并，请使用 `   useSlottedContext   ` 钩子。它的工作方式类似于 React 的 `useContext`，也接受一个可选的 slot 参数来标识要使用的 slot 名称。

```tsx
import {useSlottedContext} from 'react-aria-components';

// Consume the un-slotted value.
let buttonContext = useSlottedContext(ButtonContext);

// Consume the value for a specific slot name.
let incrementButtonContext = useSlottedContext(ButtonContext, 'increment');
```

### Accessing state
访问状态

Most React Aria Components compose other standalone components in their children to build larger patterns. However, some components are made up of more tightly coupled children. For example, [Calendar](https://react-spectrum.adobe.com/react-aria/Calendar.html) includes children such as `CalendarGrid` and `CalendarCell` that cannot be used standalone, and must appear within a `Calendar` or `RangeCalendar`. These components access the state from their parent via context.  
大多数 React Aria 组件在其子组件中组合其他独立组件以构建更大的模式。但是，某些组件由更紧密耦合的子级组成。例如，[Calendar](https://react-spectrum.adobe.com/react-aria/Calendar.html) 包括不能单独使用的子项（如 `CalendarGrid` 和 `CalendarCell）`，并且必须出现在 `Calendar` 或 `RangeCalendar` 中。这些组件通过上下文从父组件访问状态。

You can access the state from a parent component via the same contexts in order to build your own custom children. This example shows a `CalendarValue` component that displays the currently selected date from a calendar as a formatted string.  
您可以通过相同的上下文从父组件访问状态，以便构建自己的自定义子组件。此示例显示一个 `CalendarValue` 组件，该组件将日历中当前选定的日期显示为格式化字符串。

```tsx
import {CalendarStateContext} from 'react-aria-components';
import {useDateFormatter} from 'react-aria';
import {getLocalTimeZone} from '@internationalized/date';

function CalendarValue() {
  let state = React.useContext(CalendarStateContext)!;  let date = state.value?.toDate(getLocalTimeZone());
  let {format} = useDateFormatter();
  let formatted = date ? format(date) : 'None';
  return `Selected date: ${formatted}`;
}
```

This enables a `<CalendarValue>` to be placed inside a `<Calendar>` to display the current value.  
这使 `<CalendarValue>` 能够放置在 `<Calendar>` 中以显示当前值。

```tsx
<Calendar>
  {/* ... */}
  <CalendarValue /></Calendar>
```

The state interfaces and their associated contexts supported by each component are listed in the Advanced Customization section of their documentation.  
每个组件支持的状态接口及其关联上下文在其文档的“高级自定义”部分中列出。

## Hooks

---

If you need to customize things even further, such as overriding behavior, intercepting events, or customizing DOM structure, you can drop down to the lower level Hook-based API. Hooks only provide behavior and leave all rendering to you. This gives you more control and flexibility, but requires additional glue code to set up.  
如果您需要进一步自定义，例如重写行为、拦截事件或自定义 DOM 结构，则可以下拉到较低级别的基于 Hook 的 API。钩子只提供行为，而将所有渲染留给您。这为您提供了更多的控制和灵活性，但需要额外的粘合代码来设置。

React Aria Components and Hooks can be used together, allowing you to mix and match depending on the level of customization you require. We recommend starting with the component API by default, and only dropping down to hooks when you need to customize something that the component API does not allow.  
React Aria 组件和 Hooks 可以一起使用，允许您根据所需的自定义级别进行混合和匹配。我们建议默认情况下从组件 API 开始，只有当您需要自定义组件 API 不允许的内容时，才下拉到钩子。

Some potential use cases for Hooks are:  
Hooks 的一些潜在用例是：

- Overriding which DOM element a component renders  
    重写组件呈现的 DOM 元素
- Rendering a subset of the children (e.g. virtualized scrolling)  
    渲染子项的子集（例如虚拟滚动）
- Intercepting a DOM event to apply conditional logic  
    拦截 DOM 事件以应用条件逻辑
- Overriding internal state management behavior  
    覆盖内部状态管理行为
- Customizing overlay positioning  
    自定义叠加定位
- Removing unused features to reduce bundle size  
    删除未使用的功能以减少捆绑包大小

### Setup

As described [above](https://react-spectrum.adobe.com/react-aria/advanced.html#contexts), each React Aria component exports a corresponding context. You can build a custom implementation of a component using Hooks by consuming from the relevant context with 

`useContextProps`

.  
[如上](https://react-spectrum.adobe.com/react-aria/advanced.html#contexts)所述，每个 React Aria 组件都导出相应的上下文。您可以通过使用 `   useContextProps   ` 从相关上下文进行消费，使用 Hooks 构建组件的自定义实现。

This example shows how a custom checkbox could be set up using `CheckboxContext` from `react-aria-components` and the [useCheckbox](https://react-spectrum.adobe.com/react-aria/useCheckbox.html) hook from `react-aria`.  
这个例子展示了如何使用 `react-aria-components` 中`的 CheckboxContext` 和 `react-aria` 中的 [useCheckbox](https://react-spectrum.adobe.com/react-aria/useCheckbox.html) 钩子来设置自定义复选框。

```tsx
import type {CheckboxProps} from 'react-aria-components';
import {CheckboxContext, useContextProps} from 'react-aria-components';
import {useToggleState} from 'react-stately';
import {useCheckbox} from 'react-aria';

const MyCheckbox = React.forwardRef(
  (props: CheckboxProps, ref: React.ForwardedRef<HTMLInputElement>) => {
    // Merge the local props and ref with the ones provided via context.
    [props, ref] = useContextProps(props, ref, CheckboxContext);
    // Follow the hook docs and implement your customizations...
    let state = useToggleState(props);
    let { inputProps } = useCheckbox(props, state, ref);
    return <input {...inputProps} ref={ref} />;
  }
);
```

Since `MyCheckbox` consumes from `CheckboxContext` it can be used within other React Aria Components in place of the built-in `Checkbox`, such as within a [Table](https://react-spectrum.adobe.com/react-aria/Table.html) or [GridList](https://react-spectrum.adobe.com/react-aria/GridList.html). This lets you provide a custom checkbox implementation without rewriting all other React Aria Components you might use it in.  
由于 `MyCheckbox` 从 `CheckboxContext` 消耗，因此可以在其他 React Aria 组件中使用它来代替内置的 `Checkbox`，例如在 [Table](https://react-spectrum.adobe.com/react-aria/Table.html) 或 [GridList](https://react-spectrum.adobe.com/react-aria/GridList.html) 中。这允许您提供自定义复选框实现，而无需重写您可能使用它的所有其他 React Aria 组件。

```tsx
<GridList>
  <GridListItem>
    <MyCheckbox slot="selection" />    {/* ... */}
  </GridListItem>
</GridList>
```

### Reusing children

You can also provide values for React Aria Components from a Hook-based implementation. This allows you to customize the parent component of a larger pattern, while reusing the existing implementations of the child elements from React Aria Components.  
您还可以从基于 Hook 的实现中为 React Aria Components 提供值。这允许您自定义更大模式的父组件，同时重用 React Aria 组件中子元素的现有实现。

This example shows how a custom number field could be set up. First, follow the docs for [useNumberField](https://react-spectrum.adobe.com/react-aria/useNumberField.html), and then use [Provider](https://react-spectrum.adobe.com/react-aria/advanced.html#provider) to send values returned by the hook to each of the child elements via their corresponding contexts.  
此示例显示如何设置自定义数字字段。首先，遵循 [useNumberField](https://react-spectrum.adobe.com/react-aria/useNumberField.html) 的文档，然后使用 [Provider](https://react-spectrum.adobe.com/react-aria/advanced.html#provider) 将钩子返回的值通过它们对应的上下文发送到每个子元素。

```tsx
import type {NumberFieldProps} from 'react-aria-components';
import {ButtonContext, GroupContext, InputContext, LabelContext, Provider} from 'react-aria-components';
import {useNumberFieldState} from 'react-stately';
import {useLocale, useNumberField} from 'react-aria';

function CustomNumberField(props: NumberFieldProps) {
  // Follow the hook docs...
  let { locale } = useLocale();
  let state = useNumberFieldState({ ...props, locale });
  let ref = useRef<HTMLInputElement>(null);
  let {
    labelProps,
    groupProps,
    inputProps,
    incrementButtonProps,
    decrementButtonProps
  } = useNumberField(props, state, ref);

  // Provide values for the child components via context.
  return (
    <Provider
      values={[
        [GroupContext, groupProps],
        [InputContext, { ...inputProps, ref }],
        [LabelContext, labelProps],
        [ButtonContext, {
          slots: {
            increment: incrementButtonProps,
            decrement: decrementButtonProps
          }
        }]
      ]}
    >
      {props.children}
    </Provider>  );
}
```

Because `CustomNumberField` provides values for the `Group`, `Input`, `Label`, and `Button` components via context, the implementations from React Aria Components can be reused.  
由于 `CustomNumberField` 通过上下文为 `Group`、`Input`、`Label` 和 `Button` 组件提供了值，因此可以重用 React Aria 组件中的实现。

```tsx
<CustomNumberField>
  <Label>Width</Label>
  <Group>
    <Input />
    <Button slot="increment">+</Button>
    <Button slot="decrement">-</Button>
  </Group>
</CustomNumberField>
```

### Examples

The contexts provided and consumed by each component, along with the corresponding hooks, are listed in the Advanced Customization section of their documentation. The corresponding hook docs cover the implementation and APIs of each component in detail.  
每个组件提供和使用的上下文以及相应的挂钩沿着在其文档的高级自定义部分中列出。相应的 hook 文档详细介绍了每个组件的实现和 API。

The [source code](https://github.com/adobe/react-spectrum/tree/main/packages/react-aria-components/src) of React Aria Components can also be a good resource when building a custom implementation of a component. This may help you understand how all of the hooks and contexts fit together. You can also start by copy and pasting the source for a component from React Aria Components into your project, using this as a starting point to make your customizations.  
在构建组件的自定义实现时，React Aria Components 的[源代码](https://github.com/adobe/react-spectrum/tree/main/packages/react-aria-components/src)也是一个很好的资源。这可以帮助你理解所有的钩子和上下文是如何组合在一起的。您还可以从 React Aria Components 中复制并粘贴组件的源代码到您的项目中，以此为起点进行自定义。