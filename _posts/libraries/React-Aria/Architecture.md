# Architecture 架构

[原文](https://react-spectrum.adobe.com/architecture.html)

This page discusses the architecture of React Spectrum, and how behavior and core logic for many common components can be shared between design systems and across platforms.  
本页讨论React Spectrum的架构，以及如何在设计系统和跨平台之间共享许多常见组件的行为和核心逻辑。

## Introduction
简介


Today, many companies are implementing their own component libraries for their design systems from scratch. On the web, the primitives are very low level. There are very few built-in browser controls that are fully styleable, so each company needs to reimplement many of the same components from scratch. This has become easier to do with React and other modern view libraries, but implementing full support for accessibility, internationalization, keyboard, mouse, and touch interactions, and other features is still extraordinarily difficult.  
如今，许多公司都在从头开始为自己的设计系统实现自己的组件库。在Web上，原语是非常低级的。很少有内置的浏览器控件是完全可样式化的，因此每个公司都需要从头开始重新实现许多相同的组件。使用React和其他现代视图库，这变得更容易，但实现对可访问性，国际化，键盘，鼠标和触摸交互以及其他功能的完全支持仍然非常困难。

In addition, JavaScript has spread beyond only the web platform. Platforms like React Native allow React to render native widgets across mobile and desktop operating systems as well. This will continue to expand in the future as new platforms and interaction models are developed.  
此外，JavaScript已经不仅仅局限于Web平台。像React Native这样的平台允许React在移动的和桌面操作系统上渲染原生小部件。随着新的平台和交互模式的开发，这将在未来继续扩大。

While each design system is unique, there is often more in common between components than different. Most components typically found in a design system, like buttons, checkboxes, selects, and even tables, usually have very similar behavior and logic. There are even specifications that describe how many of the most common components should behave in terms of [accessibility semantics](https://www.w3.org/TR/wai-aria-1.2/) and [keyboard interactions](https://www.w3.org/TR/wai-aria-practices-1.2/). The main difference between design systems is the styling.  
虽然每个设计系统都是独特的，但组件之间的共同点往往多于不同点。设计系统中的大多数组件，如按钮、复选框、选择，甚至表格，通常具有非常相似的行为和逻辑。甚至还有规范描述了有多少最常见的组件应该在[可访问性语义](https://www.w3.org/TR/wai-aria-1.2/)和[键盘交互](https://www.w3.org/TR/wai-aria-practices-1.2/)方面表现出来。设计系统之间的主要区别是样式。

This opens up to the possibility of sharing much of the behavior and component logic between design systems and across platforms. For example, user interactions, accessibility, internationalization, and behavior can be reused, while allowing custom styling and rendering to live within individual design systems. This has the potential to improve the overall quality of applications, while saving companies money and time, and reducing duplicated effort across the industry.  
这为在设计系统之间和跨平台共享大部分行为和组件逻辑提供了可能性。例如，用户交互、可访问性、国际化和行为可以重用，同时允许在各个设计系统中使用自定义样式和呈现。这有可能提高应用程序的整体质量，同时节省公司的资金和时间，并减少整个行业的重复工作。

## Architecture

In order to allow reusing component behavior between design systems, React Spectrum splits each component into three parts: state, behavior, and the rendered component.  
**为了允许在设计系统之间重用组件行为，React Spectrum将每个组件分为三个部分：状态、行为和渲染组件**

- Component 
- Behavior Hook 
- State Hook 

Some components don't have all of these pieces. For example, some simple components do not require any state, and others are only compositions of other components, so each component has the pieces of this architecture that make sense for their purpose.  
*有些组件没有所有这些部件*。例如，一些简单的组件不需要任何状态，而其他组件只是其他组件的组合，因此每个组件都具有该架构中对其目的有意义的部分。

This architecture is made possible by [React Hooks](https://react.dev/reference/react/hooks), which enable the ability to reuse behavior between multiple components. Hooks allow accessing React features like state and effects from functions which can be called from any component. If you're unfamiliar with hooks, we recommend reading the documentation linked above first.  
*这种架构是通过[React Hooks](https://react.dev/reference/react/hooks)实现的，它能够在多个组件之间重用行为*。Hooks允许从任何组件调用的函数中访问React特性，如状态和效果。如果您不熟悉钩子，我们建议您先阅读上面链接的文档。

![[libraries/React-Aria/media/f63b4c5431b415817350572245547d1e_MD5.png]]


### State hook
At the bottom is the state hook. This hook is shared across platforms — it could work on the web, react-native, or any other platform, and makes no assumptions about the view system it is running on. It also has no theme or design system specific logic.  
*如上图所示的最底部是state钩子*。*这个钩子是跨平台共享的*-它可以在web、react-native或任何其他平台上工作，并且不假设它运行的视图系统。它也没有主题或设计系统特定的逻辑。

The state hook accepts common props from the component and provides state management. It implements the core logic for the component and returns an interface for reading and updating the component state.  
**状态钩子接受来自组件的公共属性并提供状态管理。它实现组件的核心逻辑，并返回用于阅读和更新组件状态的接口。**

```ts
let state = useNumberFieldState({ ...props, locale });
```

Not all components have a state hook. For example, many components are read-only — they display something to the user but don't allow them to change it. State hooks are found in interactive components that allow data entry, or have some kind of visual state that the user can update (e.g. expanding/collapsing).  
*并非所有组件都有状态勾子*。例如，许多组件都是只读的--它们向用户显示某些内容，但不允许用户更改它。状态钩子出现在允许数据输入的交互式组件中，或者具有某种用户可以更新的可视状态（例如展开/折叠）。

### Behavior hook

In the middle is the behavior hook. This hook is platform specific, and depends on the platform API (e.g. the DOM or react-native). It also has no theme or design system specific logic. It implements event handling, accessibility, internationalization, etc. — all the parts of a component that could be shared across multiple design systems.  
中间是行为钩子。**这个钩子是特定于平台的，并且依赖于平台API**（例如DOM或react-native）。**它也没有主题或设计系统特定的逻辑**。**它实现了事件处理、可访问性、国际化等**--一个组件的所有部分都可以在多个设计系统中共享。

The behavior hook uses the state hook in order to implement component behavior. It returns one or more sets of platform specific props (e.g. DOM props) that can be spread onto the elements rendered by the component. These include semantic properties like [ARIA](https://www.w3.org/TR/wai-aria-1.2/), and event handlers. The event handlers in turn call methods on the state interface to implement the behavior for the component.  
**行为钩子使用状态钩子来实现组件行为**。*它返回一组或多组平台特定的prop*（例如DOM prop），这些prop可以扩展到组件渲染的元素上。这些包括像[ARIA](https://www.w3.org/TR/wai-aria-1.2/)这样的语义属性和事件处理程序。事件处理程序依次调用状态接口上的方法来实现组件的行为。

Some components may not have any user interactions, but still have a behavior hook. Most components have some kind of semantics that they need to expose for accessibility (a form of behavior). The only components that won't have a behavior hook of their own are those that only compose together other components.  
有些组件可能没有任何用户交互，但仍然有一个行为挂钩。大多数组件都有某种语义，它们需要为可访问性（一种行为形式）而公开这些语义。唯一没有自己的行为钩子的组件是那些只将其他组件组合在一起的组件。

### Component

At the top is the component, which lives in each design system and composes all of these pieces together. It provides the theme and design system specific logic, and renders the actual platform elements. It applies styles, which could be implemented in many different ways (e.g. CSS classes, CSS-in-JS, etc.).  
最上面的是组件，它存在于每个设计系统中，并将所有这些部分组合在一起。它提供了主题和设计系统的特定逻辑，并呈现了实际的平台元素。它应用样式，可以以许多不同的方式实现（例如CSS类，CSS-in-JS等）。

The component uses props returned by the behavior hook and state from the state hook to implement the visual appearance. It spreads props returned from the behavior hook onto elements that it renders to apply semantics and interactions, and can use the state from the state hook to adjust its visual appearance. This allows complete control over the rendering of the component, including things like adding extra elements as needed for styling or layout control.  
组件使用行为钩子返回的属性和状态钩子返回的状态来实现可视外观。它将从行为钩子返回的props传播到它呈现的元素上，以应用语义和交互，并可以使用状态钩子的状态来调整其视觉外观。这允许对组件的呈现进行完全控制，包括添加样式或布局控制所需的额外元素。

The component itself may be split into unstyled and styled parts. Unstyled components compose together hooks and abstract away the glue code necessary to connect them together by providing reusable contexts, DOM structure, and styling APIs. This simplifies design system components while retaining the flexibility to drop down to lower-level APIs when needed.  
组件本身可以拆分为未设置样式和已设置样式的部分。无样式的组件通过提供可重用的上下文、DOM结构和样式化API将钩子组合在一起，并抽象出将它们连接在一起所需的粘合代码。这简化了设计系统组件，同时保留了在需要时下拉到较低级别API的灵活性。

## Implementation

---

React Spectrum 反应光谱

React Aria

React Stately React庄严

Our implementation of this architecture splits each piece into three npm scopes, with individually versioned packages for each component. This allows you **incrementally adopt** this approach in your own design system and reuse as much or as little as makes sense for your usecases.  
我们对这个架构的实现将每个部分分成三个npm作用域，每个组件都有单独的版本化包。这允许您在自己的设计系统中**逐步采用**这种方法，并根据您的用例尽可能多或少地重用。

Within a component, the hooks are designed to be **highly composable**, with individual features split into many hooks. This allows you to combine them together and achieve your design with only the features you need in your design system.  
在一个组件中，钩子被设计成**高度可组合的**，单个功能被分成许多钩子。这使您可以将它们联合收割机组合在一起，并仅使用设计系统中所需的功能实现设计。

The overall goal for the project is to make reusing behavior across design systems as easy as possible, while allowing full design customizability and avoiding code bloat.  
该项目的总体目标是使跨设计系统的重用行为尽可能容易，同时允许完全的设计可定制性并避免代码膨胀。

### React Stately

[React Stately](https://react-spectrum.adobe.com/react-stately/index.html) implements state management and core logic for each component. It handles complex logic for things like [collections](https://react-spectrum.adobe.com/react-stately/collections.html) and [selection](https://react-spectrum.adobe.com/react-stately/selection.html) in a fully cross-platform way that you could reuse on the web, in react-native, etc.  
[React Stately](https://react-spectrum.adobe.com/react-stately/index.html)为每个组件实现状态管理和核心逻辑。它以完全跨平台的方式处理[集合](https://react-spectrum.adobe.com/react-stately/collections.html)和[选择](https://react-spectrum.adobe.com/react-stately/selection.html)等复杂逻辑，您可以在Web上，在react-native等中重用。

React Stately hooks can be used independently in your own components, or paired with React Aria hooks to get more of the behavior and user interactions for web applications out of the box. We do not yet have behavior hooks for other platforms however, so if you're working in react-native or another view system, you'll need to use React Stately directly.  
React Stately钩子可以在你自己的组件中独立使用，也可以与React Aria钩子配对使用，以获得更多的Web应用程序的行为和用户交互。但是，我们还没有针对其他平台的行为钩子，所以如果你在react-native或其他视图系统中工作，你需要直接使用React Stately。

Read more about React Stately and [get started](https://react-spectrum.adobe.com/react-stately/getting-started.html) by reading the docs.  
阅读更多关于React Stately的内容，[并](https://react-spectrum.adobe.com/react-stately/getting-started.html)从阅读文档开始。

### React Aria

[React Aria](https://react-spectrum.adobe.com/react-aria/index.html) implements behavior and [accessibility](https://react-spectrum.adobe.com/react-aria/accessibility.html) for the web according to the W3C's [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/). It includes full screen reader and keyboard navigation support, along with mouse and touch [interactions](https://react-spectrum.adobe.com/react-aria/interactions.html) that have been tested across a wide variety of devices and browsers. It also implements [internationalization](https://react-spectrum.adobe.com/react-aria/internationalization.html) for over 30 languages, including right-to-left specific behavior, localized date and number formatting, and more.  
*[React Aria](https://react-spectrum.adobe.com/react-aria/index.html)根据W3C的[ARIA创作实践指南](https://www.w3.org/WAI/ARIA/apg/)实现Web的行为和[可访问性](https://react-spectrum.adobe.com/react-aria/accessibility.html)*。它包括*全屏阅读器和键盘导航支持，鼠标和触摸[交互](https://react-spectrum.adobe.com/react-aria/interactions.html)*，这些交互已经在各种设备和浏览器上进行了测试。它还*实现了30多种语言的[国际化](https://react-spectrum.adobe.com/react-aria/internationalization.html)*，包括从右到左的特定行为，本地化的日期和数字格式等等。

Most importantly, React Aria is **fully customizable**. It offers both high-level unstyled components and low-level hooks depending on the level of customization you need. The components provide a default DOM structure and styling API, and handle all of the glue code necessary to connect the hooks together on your behalf. If you need even more control, you can drop down to the lower-level hook-based API, which enables [advanced customization](https://react-spectrum.adobe.com/react-aria/advanced.html) use cases such as overriding DOM elements, intercepting events, customizing behavior, and more.  
最重要的是，React Aria**完全可定制**。**它根据您需要的定制级别提供高级未样式化组件和低级挂钩**。*这些组件提供默认的DOM结构和样式化API*，并处理代表您将挂钩连接在一起所需的所有粘合代码。*如果您需要更多的控制，您可以下拉到较低级别的基于钩子的API，它支持[高级定制](https://react-spectrum.adobe.com/react-aria/advanced.html)用例，例如覆盖DOM元素、拦截事件、定制行为等*。

[Read more](https://react-spectrum.adobe.com/react-aria/why.html) about React Aria and the problems it solves, and check out the docs to [get started](https://react-spectrum.adobe.com/react-aria/getting-started.html) building your own design system.  
[阅读更多](https://react-spectrum.adobe.com/react-aria/why.html)关于React Aria及其解决的问题，并查看文档以[开始](https://react-spectrum.adobe.com/react-aria/getting-started.html)构建自己的设计系统。

### React Spectrum

[React Spectrum](https://react-spectrum.adobe.com/react-spectrum/index.html) puts all of these pieces together and implements the Adobe-specific styling. It's designed to be adaptive, and works across mouse, touch, and keyboard interactions, on devices of any screen size. It supports [theming](https://react-spectrum.adobe.com/react-spectrum/theming.html), including automatic support for dark mode and responsive scaling for large hit targets on touch devices.  
[React Spectrum](https://react-spectrum.adobe.com/react-spectrum/index.html)将所有这些部分放在一起，并实现了特定于JavaScript的样式。它的设计是自适应的，可以在任何屏幕尺寸的设备上进行鼠标、触摸和键盘交互。它支持[主题化](https://react-spectrum.adobe.com/react-spectrum/theming.html)，包括自动支持黑暗模式和响应缩放触摸设备上的大型命中目标。

If you're integrating with Adobe software, React Spectrum is a great way to make your UI consistent. It's also a really great example of how to use React Aria and React Stately to build a full design system, so if you're developing your own component library, you can check out the [source code](https://github.com/adobe/react-spectrum) for inspiration.  
如果您正在与Adobe软件集成，React Spectrum是使您的UI保持一致的好方法。这也是如何使用React Aria和React Stately构建完整设计系统的一个非常好的例子，所以如果你正在开发自己的组件库，你可以查看[源代码](https://github.com/adobe/react-spectrum)以获得灵感。

[Get started](https://react-spectrum.adobe.com/react-spectrum/getting-started.html) building an application with React Spectrum, and read the docs for each component to learn more.  
[开始](https://react-spectrum.adobe.com/react-spectrum/getting-started.html)使用React Spectrum构建应用程序，并阅读每个组件的文档以了解更多信息。

## Learn more

---

The following talk from React Europe discusses how this project came to be, its architecture, high quality cross-device interactions, and how we can share behavior between design systems.  
下面来自React Europe的演讲讨论了这个项目是如何产生的，它的架构，高质量的跨设备交互，以及我们如何在设计系统之间共享行为。