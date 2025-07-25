# 总结
该网页是关于React Aria的介绍，核心围绕“为什么选择React Aria”展开，主要内容如下：

### 一、开发背景与动机
1. 设计系统普及，企业多从零构建组件库，*但实现全可访问性及跨设备交互极具挑战*。
2. 网页平台特性导致：*浏览器内置UI控件少且难定制，开发者重复造轮子*；*基础元素（如`<div>`）缺乏语义，易导致可访问性缺失*，即使使用ARIA补充语义，仍需从零实现组件行为，跨设备适配复杂。
3. *多数团队资源有限，难以优先实现可访问性、国际化、全键盘导航等功能*，导致Web应用体验欠佳，被认为劣于原生应用。


### 二、React Aria核心价值
1. *分离常见UI组件的行为与可访问性实现，提供无样式的React组件和钩子*，支持在设计系统与应用间*复用*。
2. 开发者可*完全掌控组件渲染（DOM结构）与样式*，同时免费获得行为、可访问性、国际化等核心功能。
3. 基于内置语义、行为、交互的元素构建组件，加速开发流程，确保组件在鼠标、触摸、键盘交互及辅助技术（如屏幕阅读器）中均能良好运行。


### 三、定制化能力
1. 默认无样式，支持任何样式解决方案，适配不同应用或设计系统的自定义设计。
2. 组件内置CSS类名、状态、渲染属性，简化样式设计。
3. 结合高级组件（提供易用API）与低级钩子（支持高级定制），可*按需自定义DOM结构、拦截事件、覆盖内部状态*管理等，平衡简单性与灵活性。


### 四、主要内容板块
包含丰富的组件（按钮、集合、颜色、日期时间、拖放、表单、导航、弹窗、选择器等）、对应组件的钩子（如`useButton`、`useCalendar`等）、交互工具（焦点管理、拖放相关钩子等）及工具类（国际化、服务器端渲染、属性合并等工具）。


### 五、扩展信息
可通过架构页面了解React Aria所属架构，也可参考React Europe的演讲了解其诞生、架构、跨设备交互及设计系统间行为共享等内容。

---

# Why React Aria? 
为什么选择React Aria？

This page discusses why React Aria exists, and the problems it solves.本页面探讨了React Aria存在的原因以及它解决的问题。

## Motivation
动机

---

Design systems are now more popular than ever, and many companies both large and small are implementing their own component libraries from scratch. Modern view libraries like React allow teams to build and maintain these components more easily than ever before, but it is still **extraordinarily difficult** to do so in a fully accessible way with interactions that work across many types of devices.
设计系统如今比以往任何时候都更受欢迎，许多大大小小的公司都在从零开始构建自己的组件库。像React这样的现代视图库让团队能够比以往任何时候都更轻松地构建和维护这些组件，但要以一种完全可访问的方式实现这一点，并且确保交互在多种类型的设备上都能正常运行，仍然极其困难。

The web is a very unique platform. It runs across a very wide variety of devices and interaction models, and because there is no well defined platform aesthetic, every company usually needs to style each component in their design system to match their brand. There are very few built-in UI controls in the browser, and those that do exist are very hard to style. This leads to web developers at every company needing to rebuild every control from scratch. This represents **millions of dollars** of investment for each company to **duplicate work** that many other companies are also doing.
Web 是一个非常独特的平台。它运行在各种各样的设备和交互模式上，而且由于没有明确的平台美学标准，每个公司通常都需要对其设计系统中的每个组件进行样式设计，以匹配其品牌形象。浏览器中内置的用户界面控件非常少，而且即使存在的那些也很难进行样式设置。这就导致每个公司的网页开发者都需要从头重新构建每个控件。这意味着每个公司都要投入数百万美元来重复做许多其他公司也在做的工作。

The fully styleable primitives that the web offers (e.g. `<div>`) are quite powerful, but they lack semantic meaning. This means that **accessibility is often missing** because assistive technology cannot make sense of the div soup that we use to implement our components. Even if you use [ARIA](https://www.w3.org/TR/wai-aria-1.2/) to provide semantics, you still need to implement all of the behavior for each component from scratch using JavaScript, and this can be tricky to do across many devices and interaction models.
Web 提供的完全可设置样式的基本元素（例如 `<div>`）功能相当强大，但它们缺乏语义。这意味着**通常缺乏可访问性**，因为辅助技术无法理解我们用于实现组件的混乱的div结构。即使你使用[ARIA](https://www.w3.org/TR/wai-aria-1.2/)来提供语义，你仍然需要使用JavaScript从头开始为每个组件实现所有行为，而要在众多设备和交互模式下做到这一点并非易事。

Unfortunately, many companies and teams don't have the resources or time to prioritize features like accessibility, internationalization, full keyboard navigation, touch interactions, and more. This leads to many web apps having sub-par accessibility and interactions, and contributes to the perception of the web as an inferior app platform compared to native apps.
不幸的是，许多公司和团队没有资源或时间来优先考虑诸如无障碍访问、国际化、全键盘导航、触摸交互等功能。这导致许多网络应用程序的无障碍访问和交互性欠佳，并使人们认为与原生应用程序相比，网络是一个较差的应用程序平台。

## React Aria

---

React Aria separates the behavior and accessibility implementation for many common UI components out into unstyled React components and hooks, which enables them to be reused easily between design systems and applications. You remain in complete control over the rendering and styling aspects of your components, but get most of the [behavior](https://react-spectrum.adobe.com/react-aria/interactions.html), [accessibility](https://react-spectrum.adobe.com/react-aria/accessibility.html), [internationalization](https://react-spectrum.adobe.com/react-aria/internationalization.html), and much more for free.
React Aria 将许多常见 UI 组件的行为和可访问性实现分离为无样式的 React 组件和钩子函数，这使得它们可以在设计系统和应用程序之间轻松复用。你可以完全掌控组件的渲染和样式方面，但能免费获得大部分的[行为](https://react-spectrum.adobe.com/react-aria/interactions.html)、[可访问性](https://react-spectrum.adobe.com/react-aria/accessibility.html)、[国际化](https://react-spectrum.adobe.com/react-aria/internationalization.html)等功能。

Rather than starting with only divs and building everything yourself, you start with elements that have semantic meaning, behavior, and interactions built in. This allows you to **build components more quickly**, and ensures that they work well across mouse, touch, and keyboard interactions, as well as with screen readers and other assistive technology.
与仅从div元素入手并自行构建所有内容不同，你可以从具有内置语义、行为和交互的元素开始。这使你能够**更快地构建组件**，并确保它们在鼠标、触摸和键盘交互以及屏幕阅读器和其他辅助技术中都能良好运行。

## Customization
定制化

---

The main reason developers need to create their own component libraries from scratch is styling. Built-in browser UI controls are not customizable enough, and in many cases do not exist at all. React Aria does not include any styles by default, allowing you to build custom designs to fit your application or design system using any styling solution. React Aria components include built-in CSS class names, states, and render props to make styling a breeze. See our [styling](https://react-spectrum.adobe.com/react-aria/styling.html) guide to learn more.
开发人员需要从头创建自己的组件库的主要原因是样式设计。浏览器内置的用户界面控件可定制性不足，而且在很多情况下根本不存在。React Aria 默认不包含任何样式，允许你使用任何样式解决方案构建适合你的应用程序或设计系统的自定义设计。React Aria 组件包含内置的 CSS 类名、状态和渲染属性，使样式设计轻而易举。 更多信息请参阅我们的[样式设计](https://react-spectrum.adobe.com/react-aria/styling.html)指南。

Most components have behavior that is consistent even across design systems. The [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/) published by the W3C specifies the expected keyboard and accessibility behavior for most common components. In addition, touch and mouse behavior is usually quite consistent regardless of the design. A button is still a button, even if it looks a little different.
大多数组件的行为即使在不同的设计系统中也是一致的。万维网联盟（W3C）发布的《[ARIA创作实践指南](https://www.w3.org/WAI/ARIA/apg/)》规定了大多数常见组件预期的键盘和可访问性操作。此外，触摸和鼠标操作通常也相当一致，不受设计影响。即使外观略有不同，按钮仍然是按钮。

React Aria offers a unique combination of high-level components that provide an easy to use API, and low-level hooks that enable advanced customization use cases. This allows you to customize the DOM structure, intercept events to implement custom behavior, override internal state management, and much more when you need to, while keeping things simple when you don't. Check out our [advanced customization](https://react-spectrum.adobe.com/react-aria/advanced.html) guide to learn more.
React Aria 提供了高级组件和低级钩子的独特组合，高级组件提供易于使用的 API，低级钩子则支持高级自定义用例。这使你能够在需要时自定义 DOM 结构、拦截事件以实现自定义行为、覆盖内部状态管理等等，而在不需要时保持简单。查看我们的 [高级自定义](https://react-spectrum.adobe.com/react-aria/advanced.html) 指南以了解更多信息。

## Learn more
了解更多

---

You can learn more about the architecture that React Aria is a part of on the [architecture](https://react-spectrum.adobe.com/architecture.html) page.
你可以在 [架构](https://react-spectrum.adobe.com/architecture.html) 页面上了解更多关于 React Aria 所属的架构信息。

In addition, the following talk from React Europe discusses how React Aria came to be, its architecture, high quality cross-device interactions, and how we can share behavior between design systems.
此外，React Europe的以下演讲探讨了React Aria的诞生过程、架构、高质量的跨设备交互，以及我们如何在设计系统之间共享行为。