
# 总结

## 背景与问题
- 许多公司需从零构建自己的组件库，浏览器原生控件可定制性低，导致重复开发
- 现代视图库（如React）简化了开发，但实现 accessibility（无障碍）、国际化、多端交互等功能仍极具挑战
- JavaScript 已扩展到Web之外的平台（如React Native），跨平台组件开发需求增加
- 不同设计系统的组件行为逻辑相似（如按钮、复选框等），主要差异在于样式，存在共享行为和逻辑的可能性

## 核心架构
React Spectrum 将组件拆分为三个部分，借助 React Hooks 实现跨设计系统和平台的行为复用：

### 1. 状态钩子（State hook）
- *底层核心*，跨平台通用（Web、React Native等），与视图系统和设计系统无关
- *功能*：接收组件属性、管理状态、实现核心逻辑，返回状态读写接口
- *适用场景*：存在用户交互或可更新视觉状态的组件（如数据输入、展开/折叠），只读组件无需此部分

### 2. 行为钩子（Behavior hook）
- *中间层*，平台特定（依赖DOM或React Native等平台API），与设计系统无关
- *功能*：实现事件处理、无障碍支持、国际化等可跨设计系统共享的行为
- 依赖状态钩子，返回平台特定属性（如DOM props），包含ARIA语义和事件处理器（调用状态接口实现行为）
- 大多数组件需此部分（即使无交互，也需暴露无障碍语义），仅组合其他组件的组件可能无需

### 3. 组件（Component）
- *顶层*，属于具体设计系统，*整合状态和行为钩子*
- *功能*：提供主题和设计系统特定逻辑，渲染平台元素，应用样式（支持CSS类、CSS-in-JS等多种方式）
- *可拆分为无样式和有样式部分*：无样式组件处理钩子连接逻辑，提供可复用上下文和DOM结构，简化开发同时保留底层API访问灵活性

### 4. 状态钩子与行为钩子之间有什么关系？
状态钩子（State hook）与行为钩子（Behavior hook）是 React Spectrum 架构中紧密协作的两个核心层级，二者的关系可从依赖关系、数据交互和功能互补三个维度理解：

#### 1. 依赖关系：行为钩子基于状态钩子实现功能
行为钩子依赖状态钩子提供的核心逻辑和状态管理能力，是构建组件行为的基础。  
- 行为钩子在实现时，会直接调用状态钩子的接口（如状态读写方法）来完成交互逻辑。例如，一个按钮的行为钩子处理点击事件时，会调用状态钩子中的`onPress`方法来更新按钮的激活状态。  
- 没有状态钩子，行为钩子就失去了管理组件核心逻辑的底层支撑（尤其是交互类组件）。


#### 2. 数据交互：状态钩子提供数据，行为钩子处理交互并反馈
- **状态钩子**负责维护组件的核心数据（如输入框的文本值、下拉菜单的展开状态），并提供修改这些数据的方法（如`setValue`、`toggle`）。  
- **行为钩子**通过接收用户输入（如点击、键盘事件），调用状态钩子的方法来修改数据，同时将状态钩子的当前数据转化为平台特定的属性（如DOM的`value`属性、ARIA状态），传递给上层组件用于渲染。  

简言之，状态钩子是“数据中心”，行为钩子是“交互桥梁”，二者通过“状态读写接口”实现数据流转。


#### 3. 功能互补：共同支撑组件的跨平台复用与标准化
- 状态钩子专注于**跨平台的核心逻辑**（与视图层无关），确保不同平台上的组件行为逻辑一致（如表单验证规则、选择逻辑）。  
- 行为钩子专注于**平台特定的交互细节**（如Web的DOM事件、React Native的原生事件），确保组件在特定平台上的无障碍性、事件响应等符合平台规范。  

二者分工明确：状态钩子保证“逻辑一致”，行为钩子保证“平台适配”，共同实现了组件行为在不同设计系统和平台间的复用。

#### 总结
状态钩子是组件的“灵魂”，定义核心逻辑和数据；行为钩子是组件的“躯体”，将逻辑转化为平台可执行的交互行为。前者提供“ WHAT to do ”（做什么），后者解决“ HOW to do ”（怎么做），二者结合使 React Spectrum 既能跨平台复用核心逻辑，又能适配不同平台的交互规范。

---

## 实现方案
分为三个独立的npm包，支持增量采用和高度组合：

### 1. React Stately
- 实现组件的状态管理和核心逻辑，跨平台支持（Web、React Native等）
- 可独立使用，或与React Aria配合用于Web应用
- 其他平台暂无对应行为钩子，需直接使用

### 2. React Aria
- 为Web平台实现行为和无障碍支持，遵循W3C的ARIA规范
- 支持屏幕阅读器、键盘导航、鼠标和触摸交互，适配多设备和浏览器
- 提供30多种语言的国际化支持（含RTL布局、本地化格式等）
- 高度可定制：提供高层无样式组件和低层钩子，满足不同定制需求

### 3. React Spectrum
- 整合上述组件，实现Adobe特定样式，自适应多交互方式（鼠标、触摸、键盘）和屏幕尺寸
- 支持主题功能（深色模式、触摸设备响应式缩放等）
- 适合与Adobe软件集成，也可作为使用React Aria和React Stately构建设计系统的示例

## 目标与价值
- 简化跨设计系统的行为复用，同时保留设计完全定制权，避免代码冗余
- 提升应用质量，节省企业开发时间和成本，减少行业内重复劳动

---

# Architecture 架构

[原文](https://react-spectrum.adobe.com/architecture.html)

This page discusses the architecture of React Spectrum, and how behavior and core logic for many common components can be shared between design systems and across platforms.  
本页讨论React Spectrum的架构，以及如何在设计系统和跨平台之间共享许多常见组件的行为和核心逻辑。

## Introduction
简介


Today, many companies are implementing their own component libraries for their design systems from scratch. On the web, the primitives are very low level. There are very few built-in browser controls that are fully styleable, so each company needs to reimplement many of the same components from scratch. This has become easier to do with React and other modern view libraries, but implementing full support for accessibility, internationalization, keyboard, mouse, and touch interactions, and other features is still extraordinarily difficult.  
如今，许多公司都在从头开始为自己的设计系统实现自己的组件库。*在Web上，原语是非常低级的。很少有内置的浏览器控件是完全可样式化的，因此每个公司都需要从头开始重新实现许多相同的组件*。使用React和其他现代视图库，这变得更容易，*但实现对可访问性，国际化，键盘，鼠标和触摸交互以及其他功能的完全支持仍然非常困难*。

In addition, JavaScript has spread beyond only the web platform. Platforms like React Native allow React to render native widgets across mobile and desktop operating systems as well. This will continue to expand in the future as new platforms and interaction models are developed.  
此外，JavaScript已经不仅仅局限于Web平台。像React Native这样的平台允许React在移动的和桌面操作系统上渲染原生小部件。随着新的平台和交互模式的开发，这将在未来继续扩大。

While each design system is unique, there is often more in common between components than different. Most components typically found in a design system, like buttons, checkboxes, selects, and even tables, usually have very similar behavior and logic. There are even specifications that describe how many of the most common components should behave in terms of [accessibility semantics](https://www.w3.org/TR/wai-aria-1.2/) and [keyboard interactions](https://www.w3.org/TR/wai-aria-practices-1.2/). The main difference between design systems is the styling.  
虽然每个设计系统都是独特的，但组件之间的共同点往往多于不同点。*设计系统中的大多数组件，如按钮、复选框、选择，甚至表格，通常具有非常相似的行为和逻辑*。甚至还有规范描述了有多少最常见的组件应该在[可访问性语义](https://www.w3.org/TR/wai-aria-1.2/)和[键盘交互](https://www.w3.org/TR/wai-aria-practices-1.2/)方面表现出来。*设计系统之间的主要区别是样式*。

This opens up to the possibility of sharing much of the behavior and component logic between design systems and across platforms. For example, user interactions, accessibility, internationalization, and behavior can be reused, while allowing custom styling and rendering to live within individual design systems. This has the potential to improve the overall quality of applications, while saving companies money and time, and reducing duplicated effort across the industry.  
这为在设计系统之间和跨平台，共享大部分行为和组件逻辑提供了可能性。例如，**用户交互、可访问性、国际化和行为可以重用**，同时*允许在各个设计系统中使用自定义样式*和呈现。这有可能***提高应用程序的整体质量，同时节省公司的资金和时间，并减少整个行业的重复工作***。

## Architecture

In order to allow reusing component behavior between design systems, React Spectrum splits each component into three parts: state, behavior, and the rendered component.  
**为了允许在设计系统之间*重用组件行为*，React Spectrum*将每个组件分为三个部分*：*状态、行为、渲染组件***

- Component 
- Behavior Hook 
- State Hook 

Some components don't have all of these pieces. For example, some simple components do not require any state, and others are only compositions of other components, so each component has the pieces of this architecture that make sense for their purpose.  
*有些组件没有所有这些部件*。例如，一些简单的组件不需要任何状态，而其他组件只是其他组件的组合，因此每个组件都具有该架构中对其目的有意义的部分。

This architecture is made possible by [React Hooks](https://react.dev/reference/react/hooks), which enable the ability to reuse behavior between multiple components. Hooks allow accessing React features like state and effects from functions which can be called from any component. If you're unfamiliar with hooks, we recommend reading the documentation linked above first.  
*这种架构是通过[React Hooks](https://react.dev/reference/react/hooks)实现的，它能够在多个组件之间**重用行为***。Hooks允许在任何组件的函数中访问React特性，如状态和效果。如果您不熟悉钩子，我们建议您先阅读上面链接的文档。

![[libraries/React-Aria/media/f63b4c5431b415817350572245547d1e_MD5.png]]


### State hook
At the bottom is the state hook. This hook is shared across platforms — it could work on the web, react-native, or any other platform, and makes no assumptions about the view system it is running on. It also has no theme or design system specific logic.  
*如上图所示的最底部是state钩子*。**这个钩子是跨平台共享的**---它可以在网页、react-native 或其他任何平台上运行，并且不依赖于它所运行的视图系统。*它也没有主题或设计系统特定的逻辑*。

The state hook accepts common props from the component and provides state management. It implements the core logic for the component and returns an interface for reading and updating the component state.  
**状态钩子*接受组件的常见属性*并*提供状态管理*。它*实现了组件的核心逻辑*，并*返回一个用于读取和更新组件状态的接口*。**

```ts
let state = useNumberFieldState({ ...props, locale });
```

Not all components have a state hook. For example, many components are read-only — they display something to the user but don't allow them to change it. State hooks are found in interactive components that allow data entry, or have some kind of visual state that the user can update (e.g. expanding/collapsing).  
*并非所有组件都有状态钩子*。例如，*许多组件是只读的*——它们向用户显示某些内容，但不允许用户更改。状态钩子存在于**允许数据输入的交互式组件**中，或具有**用户可以更新的某种视觉状态**（例如*展开/折叠*）。

### Behavior hook

In the middle is the behavior hook. This hook is platform specific, and depends on the platform API (e.g. the DOM or react-native). It also has no theme or design system specific logic. It implements event handling, accessibility, internationalization, etc. — all the parts of a component that could be shared across multiple design systems.  
中间是行为钩子。**这个钩子是特定于平台的，并且依赖于平台API**（例如DOM或react-native）。**它也没有主题或设计系统特定的逻辑**。***它实现了事件处理、可访问性、国际化等***--一个组件的所有部分都可以在多个设计系统中共享。

The behavior hook uses the state hook in order to implement component behavior. It returns one or more sets of platform specific props (e.g. DOM props) that can be spread onto the elements rendered by the component. These include semantic properties like [ARIA](https://www.w3.org/TR/wai-aria-1.2/), and event handlers. The event handlers in turn call methods on the state interface to implement the behavior for the component.  
***行为钩子使用状态钩子来实现组件行为***。***它返回一组或多组平台特定的prop***（例如DOM prop），这些prop可以扩展到组件渲染的元素上。这些包括**像[ARIA](https://www.w3.org/TR/wai-aria-1.2/)这样的语义属性、事件处理程序**。**事件处理程序依次调用状态接口上的方法来实现组件的行为**。

Some components may not have any user interactions, but still have a behavior hook. Most components have some kind of semantics that they need to expose for accessibility (a form of behavior). The only components that won't have a behavior hook of their own are those that only compose together other components. 
有些组件*可能没有任何用户交互，但仍然有一个行为钩子*。大多数组件都*有某种语义，它们需要为可访问性（一种行为形式）而公开这些语义*。唯一没有自己的行为钩子的组件是那些*只将其他组件组合在一起的组件*。

### Component

At the top is the component, which lives in each design system and composes all of these pieces together. It provides the theme and design system specific logic, and renders the actual platform elements. It applies styles, which could be implemented in many different ways (e.g. CSS classes, CSS-in-JS, etc.).  
*最上面的是组件*，它存在于每个设计系统中，*并将所有这些部分组合在一起*。它*提供了主题和设计系统的特定逻辑*，并呈现了实际的平台元素。*它应用样式，可以以许多不同的方式实现*（例如CSS类，CSS-in-JS等）。

The component uses props returned by the behavior hook and state from the state hook to implement the visual appearance. It spreads props returned from the behavior hook onto elements that it renders to apply semantics and interactions, and can use the state from the state hook to adjust its visual appearance. This allows complete control over the rendering of the component, including things like adding extra elements as needed for styling or layout control.  
***组件使用行为钩子返回的属性、状态钩子返回的状态，来实现可视外观***。**它将从行为钩子返回的props传播到它渲染的元素上，来应用语义和交互**，并可以**使用状态钩子的状态来调整其视觉外观**。这允许**对组件的呈现进行完全控制，包括添加样式或布局控制所需的额外元素**。

The component itself may be split into unstyled and styled parts. Unstyled components compose together hooks and abstract away the glue code necessary to connect them together by providing reusable contexts, DOM structure, and styling APIs. This simplifies design system components while retaining the flexibility to drop down to lower-level APIs when needed.  
组件本身可以拆分为未设置样式和已设置样式的部分。*无样式的组件通过提供可重用的上下文、DOM结构和样式化API将钩子组合在一起，并抽象出将它们连接在一起所需的粘合代码*。*这简化了设计系统组件，同时保留了在需要时下拉到较低级别API的灵活性*。

## Implementation

---

![[_posts/libraries/React-Aria/media/40ae633ac5c843cc7c0001bbb4e9e9c9_MD5.jpeg]]

Our implementation of this architecture splits each piece into three npm scopes, with individually versioned packages for each component. This allows you **incrementally adopt** this approach in your own design system and reuse as much or as little as makes sense for your usecases.  
*我们对该架构的实现将每个部分分成三个 npm 范围*，每个组件都有单独的版本化包。这允许您在自己的设计系统中**逐步采用**这种方法，并根据您的用例尽可能多或少地重用。

Within a component, the hooks are designed to be **highly composable**, with individual features split into many hooks. This allows you to combine them together and achieve your design with only the features you need in your design system.  
*在一个组件中，钩子被设计成**高度可组合的**，单个功能被分成许多钩子*。这使您可以*将它们组合在一起*，并*仅使用设计系统中所需的功能实现设计*。

The overall goal for the project is to make reusing behavior across design systems as easy as possible, while allowing full design customizability and avoiding code bloat.  
**该项目的总体目标**是使跨设计系统的重用行为尽可能容易，同时允许完全的设计可定制性并避免代码膨胀。

### React Stately

[React Stately](https://react-spectrum.adobe.com/react-stately/index.html) implements state management and core logic for each component. It handles complex logic for things like [collections](https://react-spectrum.adobe.com/react-stately/collections.html) and [selection](https://react-spectrum.adobe.com/react-stately/selection.html) in a fully cross-platform way that you could reuse on the web, in react-native, etc.  
[React Stately](https://react-spectrum.adobe.com/react-stately/index.html)**为每个组件实现状态管理和核心逻辑**。它以完全跨平台的方式处理[集合](https://react-spectrum.adobe.com/react-stately/collections.html)和[选择](https://react-spectrum.adobe.com/react-stately/selection.html)等复杂逻辑，您可以在Web上，在react-native等中重用。

React Stately hooks can be used independently in your own components, or paired with React Aria hooks to get more of the behavior and user interactions for web applications out of the box. We do not yet have behavior hooks for other platforms however, so if you're working in react-native or another view system, you'll need to use React Stately directly.  
*React Stately钩子可以在你自己的组件中独立使用，也可以与React Aria钩子配对使用，以获得更多的Web应用程序的行为和用户交互*。但是，我们还没有针对其他平台的行为钩子，所以如果你在react-native或其他视图系统中工作，你需要直接使用React Stately。

Read more about React Stately and [get started](https://react-spectrum.adobe.com/react-stately/getting-started.html) by reading the docs.  
阅读更多关于React Stately的内容，[并](https://react-spectrum.adobe.com/react-stately/getting-started.html)从阅读文档开始。

### React Aria

[React Aria](https://react-spectrum.adobe.com/react-aria/index.html) implements behavior and [accessibility](https://react-spectrum.adobe.com/react-aria/accessibility.html) for the web according to the W3C's [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/). It includes full screen reader and keyboard navigation support, along with mouse and touch [interactions](https://react-spectrum.adobe.com/react-aria/interactions.html) that have been tested across a wide variety of devices and browsers. It also implements [internationalization](https://react-spectrum.adobe.com/react-aria/internationalization.html) for over 30 languages, including right-to-left specific behavior, localized date and number formatting, and more.  
*[React Aria](https://react-spectrum.adobe.com/react-aria/index.html)根据W3C的[ARIA创作实践指南](https://www.w3.org/WAI/ARIA/apg/)实现Web的行为、[可访问性](https://react-spectrum.adobe.com/react-aria/accessibility.html)*。它包括*全屏阅读器、键盘导航支持，鼠标和触摸[交互](https://react-spectrum.adobe.com/react-aria/interactions.html)*，这些交互已经在各种设备和浏览器上进行了测试。它还*实现了30多种语言的[国际化](https://react-spectrum.adobe.com/react-aria/internationalization.html)*，包括从右到左的特定行为，本地化的*日期、数字格式*等等。

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