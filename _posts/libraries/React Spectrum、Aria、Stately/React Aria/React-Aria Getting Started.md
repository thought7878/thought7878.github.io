# React Aria 网页总结

## 1. 什么是 React Aria
- 是一个无样式的 **React 组件和钩子库**，用于构建可访问、高质量的 UI 组件。
- **内置可访问性、国际化、交互及行为逻辑**，让开发者*专注于设计和样式，无需重复开发复杂功能*。
- *经过多设备、交互方式、辅助技术的严格测试*，确保所有用户的良好体验。


## 2. 安装方式
- 可通过 npm 或 yarn 等包管理器安装，命令：`yarn add react-aria-components`。
- 所有组件集成在单个包中，便于依赖管理。


## 3. 组件构建
- **组件基于可组合性设计的，每个组件通常与单个 DOM 元素一一对应，便于样式控制、布局调整、DOM 顺序管理。**
- 示例：*自定义 Select 组件*可通过组合 Label、Button、Popover、ListBox 等部分实现。


## 4. 样式方案
- 默认无内置样式，支持多种样式解决方案（如 vanilla CSS、Tailwind CSS、CSS-in-JS 等）。
- 组件*自带默认 CSS 类名和状态数据属性（如 `data-selected`、`data-focused`）用于样式控制*，也可**通过 `className` 覆盖默认类名**。
- 提供示例 CSS 代码，展示如何基于类名和状态属性设置样式。


## 5. Starter Kits
- 提供包含多种样式方案的入门套件，包含所有 React Aria 组件的示例实现。
- 支持暗模式、高对比度模式及所有 UI 状态，附带预配置的 Storybook，*可作为组件库的起点*。
- 包含 Vanilla CSS 和 Tailwind CSS 等版本，可下载使用。


## 6. 组件与钩子
- **组件分类**：涵盖 Buttons、Collections、Color、Date and Time、Forms、Navigation、Overlays 等多个类别，如 Button、Select、Dialog、Checkbox 等。
- **钩子**：*对应组件提供底层钩子*（如 `useButton`、`useSelect`），支持更灵活的自定义。
- *组件与钩子可混合使用，建议优先使用组件，需更高灵活性时采用钩子*。


## 7. 其他工具
- 包含交互工具（如 `useFocus`、`useHover`）、国际化工具（如 `I18nProvider`、`useLocale`）、服务端渲染工具（如 `SSRProvider`）等辅助功能。

---

# Getting Started 
入门指南

This page describes how to get started with React Aria.本页面介绍如何开始使用 React Aria。

## What is React Aria?
什么是React Aria？

---

**React Aria** is a library of unstyled React components and hooks that helps you build accessible, high quality UI components for your application or design system. It provides components for common UI patterns, with [accessibility](https://react-spectrum.adobe.com/react-aria/accessibility.html), [internationalization](https://react-spectrum.adobe.com/react-aria/internationalization.html), [interactions](https://react-spectrum.adobe.com/react-aria/interactions.html), and behavior built in, allowing you to focus on your unique design and styling rather than re-building these challenging aspects. React Aria has been meticulously tested across a wide variety of devices, interaction modalities, and assistive technologies to ensure the best experience possible for all users.
**React Aria**是一个无样式的React组件和钩子库，可帮助您为应用程序或设计系统构建易于访问的高质量用户界面组件。它为常见的用户界面模式提供组件，并内置了[可访问性](https://react-spectrum.adobe.com/react-aria/accessibility.html)、[国际化](https://react-spectrum.adobe.com/react-aria/internationalization.html)、[交互](https://react-spectrum.adobe.com/react-aria/interactions.html)和行为功能，使您能够专注于独特的设计和样式，而无需重新构建这些具有挑战性的方面。React Aria已在各种设备、交互方式和辅助技术上进行了精心测试，以确保为所有用户提供最佳体验。

## Installation

---

React Aria Components can be installed using a package manager like [npm](https://docs.npmjs.com/cli/npm) or [yarn](https://classic.yarnpkg.com/lang/en/).
可以使用像 [npm](https://docs.npmjs.com/cli/npm) 或 [yarn](https://classic.yarnpkg.com/lang/en/) 这样的包管理器来安装 React Aria 组件。

```
yarn add react-aria-components
```

All components are available in this one package for ease of dependency management.
为便于进行依赖管理，此单一软件包中包含所有组件。

## Building a component

---

Once installed, you can import and render the components you need. Each component may include several parts, as documented on the corresponding component page. The API is designed around composition, where each component generally has a 1:1 relationship with a single DOM element. This makes it easy to style every element, and control the layout and DOM order as needed to implement your design.
安装完成后，你就可以导入并渲染所需的组件。每个组件可能包含多个部分，具体内容可参考相应的组件页面文档。该API围绕组合设计，通常每个组件与单个DOM元素呈1:1的对应关系。这使得为每个元素设置样式变得轻而易举，并且可以根据实现设计的需要控制布局和DOM顺序。

This example renders a custom [Select](https://react-spectrum.adobe.com/react-aria/Select.html).
此示例呈现一个自定义的 [选择框](https://react-spectrum.adobe.com/react-aria/Select.html)。

```
import {Button, Label, ListBox, ListBoxItem, Popover, Select, SelectValue} from 'react-aria-components';

<Select>
  <Label>Favorite Animal</Label>
  <Button>
    <SelectValue />
    <span aria-hidden="true">▼</span>
  </Button>
  <Popover>
    <ListBox>
      <ListBoxItem>Cat</ListBoxItem>
      <ListBoxItem>Dog</ListBoxItem>
      <ListBoxItem>Kangaroo</ListBoxItem>
    </ListBox>
  </Popover>
</Select>
```

Favorite Animal 最喜欢的动物选择一个项目▼

CatDogKangaroo

### Styling
样式设置

React Aria Components do not include any styles by default, allowing you to build custom designs to fit your application or design system. It works with any styling solution, including vanilla CSS, Tailwind CSS, CSS-in-JS, etc.React Aria 组件默认不包含任何样式，这使你能够构建适合你的应用程序或设计系统的自定义设计。它可与任何样式解决方案配合使用，包括原生 CSS、Tailwind CSS、CSS-in-JS 等。

By default, all React Aria Components include CSS class names that you can use for styling, along with data attributes to represent states such as pressed, hovered, selected, etc.默认情况下，所有React Aria组件都包含可用于设置样式的CSS类名，以及用于表示按下、悬停、选中 等状态的数据属性。

```
.react-aria-ListBoxItem {
  color: black;

  &[data-selected] {
    background: slateblue;
    color: white;
  }
}
```

This is a quick way to get started, but you can also create your own custom classes using the `className` prop, which overrides the defaults. The full CSS for all of the components in above example is below:这是一种快速上手的方法，但你也可以使用 `className` 属性创建自己的自定义类，该属性会覆盖默认设置。上述示例中所有组件的完整 CSS 如下：

Show CSS显示CSS

Check out our [styling guide](https://react-spectrum.adobe.com/react-aria/styling.html) to learn more about styling, states, render props, working with Tailwind CSS, and animation.查看我们的[样式指南](https://react-spectrum.adobe.com/react-aria/styling.html)，以了解更多关于样式、状态、渲染属性、使用Tailwind CSS以及动画的信息。

## Starter kits[#](https://react-spectrum.adobe.com/react-aria/getting-started.html#starter-kits) 入门套件[#](https://react-spectrum.adobe.com/react-aria/getting-started.html#starter-kits)

---

To help kick-start your project, we offer starter kits that include example implementations of all React Aria components with various styling solutions. All components are fully styled, including support for dark mode, high contrast mode, and all UI states. Each starter comes with a pre-configured [Storybook](https://storybook.js.org/) that you can experiment with, or use as a starting point for your own component library.为帮助你快速启动项目，我们提供了入门套件，其中包含所有React Aria组件的示例实现以及各种样式解决方案。所有组件都经过全面设计，支持深色模式、高对比度模式以及所有用户界面状态。每个入门套件都带有预先配置好的 [Storybook](https://storybook.js.org/)，你可以进行试用，也可以将其作为你自己组件库的起点。

[

Vanilla CSS 原生CSS

Download ZIP 下载ZIP



](https://react-spectrum.adobe.com/react-aria-starter.2d9262c8b.zip "Vanilla CSS")[Preview 预览](https://react-spectrum.adobe.com/react-aria-starter/index.html)

[

Tailwind CSS

Download ZIP 下载ZIP



](https://react-spectrum.adobe.com/react-aria-tailwind-starter.2d9262c8b.zip "Tailwind CSS")[Preview 预览](https://react-spectrum.adobe.com/react-aria-tailwind-starter/index.html)

## Hooks[#](https://react-spectrum.adobe.com/react-aria/getting-started.html#hooks) 钩子[#](https://react-spectrum.adobe.com/react-aria/getting-started.html#hooks)

---

React Aria includes both components and hooks depending on the level of customization you need. The components provide a default DOM structure and styling API, and handle all of the glue code necessary to connect the hooks together on your behalf. If you need even more control, you can drop down to the lower-level hook-based API. Check out our [Hooks Introduction](https://react-spectrum.adobe.com/react-aria/hooks.html) to get started.React Aria 同时包含组件和钩子函数，具体取决于你所需的定制程度。这些组件提供了默认的 DOM 结构和样式 API，并会代你处理将各个钩子函数连接在一起所需的所有粘合代码。如果你需要更精细的控制，可以使用更低级别的基于钩子函数的 API。请查看我们的[钩子函数介绍](https://react-spectrum.adobe.com/react-aria/hooks.html)来开始使用。

The components and hooks also work together, allowing them to be mixed and matched depending on the level of customization required. We recommend starting with components, dropping down to hooks only when additional flexibility is needed. See our [Advanced Customization](https://react-spectrum.adobe.com/react-aria/advanced.html) guide to learn more about using components and hooks together.这些组件和钩子函数也能协同工作，可根据所需的定制程度进行混合搭配。我们建议从组件入手，仅在需要额外灵活性时再使用钩子函数。有关如何结合使用组件和钩子函数的更多信息，请参阅我们的[高级定制](https://react-spectrum.adobe.com/react-aria/advanced.html)指南。