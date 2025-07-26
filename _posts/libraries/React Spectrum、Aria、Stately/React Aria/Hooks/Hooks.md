### 网页总结：React Aria Hooks 文档

#### 1. 概述
- 网页类型：React Aria 相关的技术文档，主题为“React Aria Hooks（React Aria 钩子）”，主要介绍其使用方法、组件与钩子功能等。


#### 2. 核心内容分类
- **基础板块**：包含介绍（Introduction）、概念（Concepts，如可访问性、交互、国际化等）、指南（Guides，如样式、表单、客户端路由等）。
- **组件（Components）**：按功能分类，包括按钮（Buttons）、集合（Collections）、颜色（Color）、日期时间（Date and Time）、拖放（Drag and drop）、表单（Forms）、导航（Navigation）等13类，每类下有具体组件（如Buttons包含Button、ToggleButton等）。
- **钩子（Hooks）**：*与组件对应，提供各组件的钩子函数*，如*按钮类*有useButton、useToggleButton等；*集合类*有useGridList、useListBox等，支持组件的行为与可访问性实现。
- **交互（Interactions）**：包含*焦点*管理（如useFocus、useFocusVisible）、*拖放*（如useDrag、useDrop）等钩子。
- **工具（Utilities）**：含*国际化*（如I18nProvider、useLocale）、*服务端渲染*（如SSRProvider）、*通用工具*（如mergeProps、useId）等功能。


#### 3. 安装方法
- 支持通过npm或yarn安装，有两种方式：
  - 单包安装：`yarn add react-aria`，导入时直接从`react-aria`引入（如`import {useButton} from 'react-aria'`）。
  - 单独包安装：仅安装所需钩子，包位于`@react-aria` scope下（如`yarn add @react-aria/button`），导入时从对应包引入（如`import {useButton} from '@react-aria/button'`）。


#### 4. 使用方法
- **构建基础组件**：
  - React Aria 钩子提供**行为、可访问性**支持，不负责渲染，**需用户自定义DOM结构，并将钩子返回的DOM props传递给对应元素**（通过属性展开实现）。
  - 示例：用`useButton`构建按钮组件，*通过`buttonProps`实现交互，结合自定义样式即可得到支持鼠标、触摸、键盘等多端交互的可访问性按钮*。

- **构建有状态组件**：
  - 状态管理逻辑位于`react-stately`库，需导入对应状态钩子（如`useNumberFieldState`），并*将状态对象传递给React Aria钩子*（如`useNumberField`）。
  - 示例：数字字段组件结合`useNumberFieldState`（*状态管理*）和`useNumberField`（*行为与可访问性*），实现带输入验证、国际化格式的交互功能。


#### 5. 特点
- 专注于行为与可访问性，支持多端一致交互（鼠标、触摸、键盘、屏幕阅读器）。
- 样式完全由用户控制，支持CSS类、内联样式、CSS-in-JS等多种方式。
- 组件与钩子分类清晰，覆盖按钮、表单、日期、导航等多种场景，且支持国际化、服务端渲染等扩展需求。

---

# React Aria Hooks

This page describes how to get started with React Aria hooks.
本页面介绍如何开始使用 React Aria 钩子。

---

## Installation

React Aria can be installed using a package manager like [npm](https://docs.npmjs.com/cli/npm) or [yarn](https://classic.yarnpkg.com/lang/en/).
可以使用诸如 [npm](https://docs.npmjs.com/cli/npm) 或 [yarn](https://classic.yarnpkg.com/lang/en/) 之类的包管理器来安装 React Aria。

```bash
yarn add react-aria
```

If you prefer, you can also use our hooks from individually versioned packages. This allows you to only install the hooks you use, or more granularly manage their versions. The individual packages are published under the [@react-aria](https://www.npmjs.com/org/react-aria) scope on npm. For example:
如果你愿意，也可以从单独版本化的包中使用我们的钩子函数。这样你就可以只安装你使用的钩子函数，或者更细致地管理它们的版本。这些单独的包发布在 npm 上的 [@react-aria](https://www.npmjs.com/org/react-aria) 范围内。例如：

```bash
yarn add @react-aria/button
```

Once installed, hooks can be used from the monopackage or individual packages the same way.
一旦安装完成，钩子函数可以以相同的方式从单包或单个包中使用。

```tsx
// Monopackage
import {useButton} from 'react-aria';
```

```tsx
// Individual packages
import {useButton} from '@react-aria/button';
```

---

## Building a component

React Aria provides behavior and accessibility through React Hooks. Since it does not provide any rendering, you are responsible for defining the DOM structure for your component and passing the DOM props returned by each React Aria hook to the appropriate elements. This is powerful because it allows you to be in complete control over the DOM structure that you render. For example, you may need to add extra elements for styling or layout control. You also get complete control over how you style your components: you could use CSS classes, inline styles, CSS-in-JS, etc.
React Aria 通过 React Hook 提供行为和可访问性。由于它不提供任何渲染，因此你需要负责为组件定义 DOM 结构，并将每个 React Aria Hook 返回的 DOM 属性传递给相应的元素。这非常强大，因为它让你能够完全控制所渲染的 DOM 结构。例如，你可能需要添加额外的元素来进行样式设置或布局控制。你还可以完全控制组件的样式设置方式：你可以使用 CSS 类、内联样式、CSS-in-JS 等。

Start by importing the hook you wish to use, and calling it in your component. You'll typically pass through the props from your component, along with a [ref](https://react.dev/learn/referencing-values-with-refs#refs-and-the-dom) to the DOM node in some cases. The hook will return one or more sets of DOM props which you should pass through to the appropriate element. This can be done by [spreading the props](https://react.dev/learn/passing-props-to-a-component#forwarding-props-with-the-jsx-spread-syntax) returned from the hook onto the element that you render.
首先导入你想要使用的钩子函数，并在组件中调用它。通常情况下，你需要传递组件的属性，在某些情况下，还需要传递一个指向DOM节点的[引用](https://react.dev/learn/referencing-values-with-refs#refs-and-the-dom)。该钩子函数将返回一组或多组DOM属性，你应该将这些属性传递给相应的元素。这可以通过将钩子函数返回的属性[展开](https://react.dev/learn/passing-props-to-a-component#forwarding-props-with-the-jsx-spread-syntax)到你渲染的元素上来实现。

This example shows a very simple button component built with the [useButton](https://react-spectrum.adobe.com/react-aria/useButton.html) hook.
这个示例展示了一个使用 [useButton](https://react-spectrum.adobe.com/react-aria/useButton.html) 钩子构建的非常简单的按钮组件。

```tsx
import {useButton} from 'react-aria';

function Button(props) {
  let ref = React.useRef(null);
  let { buttonProps, isPressed } = useButton(props, ref);
  return (
    <button
      ref={ref}
      {...buttonProps}      style={{
        background: isPressed ? '#444' : '#666',
        color: 'white',
        padding: '6px 12px',
        borderRadius: 4,
        border: 'none'
      }}
    >
      {props.children}
    </button>
  );
}

<Button onPress={() => alert('Button pressed!')}>
  Press me
</Button>
```

Now you just need to add your own styling, and you have a fully accessible button component that works consistently across mouse, touch, keyboard, and screen readers with [high quality interactions](https://react-spectrum.adobe.com/blog/building-a-button-part-1.html)! See the [useButton](https://react-spectrum.adobe.com/react-aria/useButton.html) docs more examples.
现在你只需要添加自己的样式，就能拥有一个完全可访问的按钮组件，它在鼠标、触摸、键盘和屏幕阅读器上都能一致工作，且具有[高质量的交互效果](https://react-spectrum.adobe.com/blog/building-a-button-part-1.html)！查看[useButton](https://react-spectrum.adobe.com/react-aria/useButton.html)文档以获取更多示例。

---

## Stateful components

Many components are **stateless** — they display information to a user. Examples of stateless components include buttons, progress bars, and links. These components don't update as the user interacts with them.
许多组件是**无状态的**——它们向用户显示信息。无状态组件的示例包括按钮、进度条和链接。这些组件在用户与之交互时不会更新。

A **stateful** component has some state, and allows a user to interact with the component in order to update that state. Examples of stateful components include text fields, checkboxes, and selects.
有状态组件拥有一些状态，并允许用户与组件进行交互以更新该状态。有状态组件的示例包括文本字段、复选框和选择框。

React Aria separates the state management logic into a separate hook that lives in [react-stately](https://react-spectrum.adobe.com/react-stately/index.html). The state hook holds the state for the component, and provides an interface to read and update that state. This allows this logic to be reused across different platforms, e.g. in react-native. Read more about this on the [architecture](https://react-spectrum.adobe.com/architecture.html) page.
React Aria 将状态管理逻辑分离到一个单独的钩子中，该钩子位于 [react-stately](https://react-spectrum.adobe.com/react-stately/index.html) 中。状态钩子保存组件的状态，并提供一个接口来读取和更新该状态。这使得此逻辑可以在不同平台（例如 React Native）上复用。在 [架构](https://react-spectrum.adobe.com/architecture.html) 页面上了解更多相关信息。

To build a stateful component, you'll need to install and import the corresponding state hook from react-stately. Then, call the state hook from your component, and pass the resulting state object to the React Aria hook. You can also use the state in your rendering code to determine what visual state to display.
要构建有状态组件，你需要从react-stately安装并导入相应的状态钩子。然后，从你的组件中调用状态钩子，并将生成的状态对象传递给React Aria钩子。你还可以在渲染代码中使用该状态来确定要显示的视觉状态。

This example shows a number field component built with the [useNumberField](https://react-spectrum.adobe.com/react-aria/useNumberField.html) hook, along with the [useNumberFieldState](https://react-spectrum.adobe.com/react-stately/useNumberFieldState.html) hook from react-stately.
此示例展示了一个使用 [useNumberField](https://react-spectrum.adobe.com/react-aria/useNumberField.html) 钩子以及 react-stately 中的 [useNumberFieldState](https://react-spectrum.adobe.com/react-stately/useNumberFieldState.html) 钩子构建的数字字段组件。

```tsx
import {useNumberFieldState} from 'react-stately';
import {useLocale, useNumberField} from 'react-aria';

function NumberField(props) {
  let {locale} = useLocale();
  let state = useNumberFieldState({...props, locale});
  let inputRef = React.useRef(null);
  let {
    labelProps,
    groupProps,
    inputProps,
    incrementButtonProps,
    decrementButtonProps
  } = useNumberField(props, state, inputRef);

  return (
    <div>
      <label {...labelProps}>{props.label}</label>
      <div {...groupProps} style={{display: 'flex', gap: 4}}>
        <Button {...decrementButtonProps}>-</Button>
        <input {...inputProps} ref={inputRef} />
        <Button {...incrementButtonProps}>+</Button>
      </div>
    </div>
  );
}

<NumberField
  label="Price"
  defaultValue={6}
  formatOptions={{
    style: 'currency',
    currency: 'USD'
  }}
/>
```

This gives you a number field complete with input validation, internationalized formatting, accessibility, mobile keyboard support, and much more! See the [useNumberField](https://react-spectrum.adobe.com/react-aria/useNumberField.html) docs to learn more about all of the features.
这为你提供了一个数字字段，具备输入验证、国际化格式设置、可访问性、移动键盘支持等诸多功能！详细了解所有功能，请参阅 [useNumberField](https://react-spectrum.adobe.com/react-aria/useNumberField.html) 文档。