# [The anatomy of shadcn/ui](https://manupa.dev/blog/anatomy-of-shadcn-ui)

December 11, 2023

If you were roaming around in the JavaScript ecosystem this year you might have come across this interesting UI library called shadcn/ui. Instead of being distributed as a npm package, the shadcn/ui components are delivered through a CLI that puts the source code of the components into your project itself. The creator mentions the reason for this decision in the official website for shadcn/ui,  
如果您今年在 JavaScript 生态系统中漫游，您可能会遇到这个有趣的 UI 库，名为 shadcn/ui。 shadcn/ui 组件不是作为 npm 包分发，而是通过 CLI 交付，该 CLI 将组件的源代码放入项目本身。创建者在 shadcn/ui 的官网提到了这个决定的原因，

> **Why copy/paste and not packaged as a dependency?  
> 为什么复制/粘贴而不打包为依赖项？**
> 
> The idea behind this is to give you ownership and control over the code, allowing you to decide how the components are built and styled.  
> 其背后的想法是赋予您对代码的所有权和控制权，允许您决定如何构建组件和设计样式。
> 
> Start with some sensible defaults, then customize the components to your needs.  
> 从一些合理的默认值开始，然后根据您的需要自定义组件。
> 
> One of the drawbacks of packaging the components in an npm package is that the style is coupled with the implementation. _The design of your components should be separate from their implementation._  
> 将组件打包在 npm 包中的缺点之一是样式与实现耦合。_组件的设计应该与其实现分开。_

In essence, shadcn/ui is not just another component library but a mechanism to declare a design system as code.  
本质上，shadcn/ui 不仅仅是另一个组件库，而是一种将设计系统声明为代码的机制。

My intention with this article is to explore the architecture and implementation of shadcn/ui to review how it has been designed achieve the before mentioned goals.  
我写这篇文章的目的是探索 shadcn/ui 的架构和实现，以回顾它是如何设计来实现前面提到的目标的。

If you haven't tried out shadcn/ui yet I would suggest visiting the [shadcn/ui docs](https://ui.shadcn.com/) and play around a bit with it to get the most out of this article.  
如果您还没有尝试过 shadcn/ui，我建议您访问[shadcn/ui 文档](https://ui.shadcn.com/)并尝试一下，以充分利用本文。

## Prologue 序幕

Any user interface can be broken down into a set of primitive reusable components and their compositions. We can identify any given UI component to be constituent of its own behavior set and a visual presentation of a given style.  
**任何用户界面都可以分解为一组原始的可重用组件及其组合**。我们可以将任何给定的 UI 组件识别为其*自身行为*集的组成部分以及*给定样式的视觉呈现*。

### Behavior 行为

Except from purely presentational UI components, UI components should be aware of user interactions that can be performed on them and should react accordingly. The foundations necessary for these behaviors are built-in to the native browser elements and available for us to utilize. But in modern user interfaces we need to present components that contains behaviors that cannot be satisfied by native browser elements only. (Tabs, Accordions, DatePickers etc.) This warrants the need to build custom components that looks and behave as we conceptualize.  
除了纯粹的演示性 UI 组件之外，UI 组件还应该了解*可以在其上执行的用户交互，并做出相应的反应*。这些行为所需的基础内置于本机浏览器元素中，可供我们使用。*但在现代用户界面中，我们需要呈现的组件包含仅由本机浏览器元素无法满足的行为。 （选项卡、手风琴、日期选择器等）这就需要构建外观和行为都符合我们概念的自定义组件*。

Building custom components is usually not difficult to implement at the surface level using modern UI frameworks. But most of the time these implementations of custom components tend to overlook some very important aspects of the behavior of a UI component. This includes behaviors such as focus/blur state awareness, keyboard navigation and adhering to WAI-ARIA design principles. Even though behaviors are very important to enable accessibility in our user interfaces, getting these behaviors right according to W3C specifications is a really hard task and could significantly slow down product development.  
使用现代 UI 框架在表面级别构建自定义组件通常并不困难。*但大多数时候，这些自定义组件的实现往往会忽略 UI 组件行为的一些非常重要的方面*。这包括焦点/模糊状态感知、键盘导航和遵守 WAI-ARIA 设计原则等行为。尽管行为对于实现用户界面的可访问性非常重要，但根据 W3C 规范正确实现这些行为是一项非常艰巨的任务，并且可能会显着减慢产品开发速度。

Given the fast moving culture of modern software development, it is difficult to factor in accessibility guidelines into custom component development for front-end teams. One approach a company could follow to mitigate this would be to develop a set of unstyled base components that already implements these behaviors and use them in all projects. But each team should be able to extend and style these components effortlessly to fit the visual design of their project.  
*鉴于现代软件开发的快速发展文化，很难将可访问性指南纳入前端团队的自定义组件开发中*。*公司可以采取的缓解这种情况的一种方法是开发一组无样式的基本组件，这些组件已经实现了这些行为*并在所有项目中使用它们。但每个团队都应该能够轻松地扩展这些组件并设计它们的样式，以适应其项目的视觉设计。

These reusable components that are unstyled but encapsulate their behavior set are known as **Headless UI** components. Often these can be designed to expose a API surface to read and control their internal state. This concept is one of the major architectural elements of shadcn/ui.  
*这些无样式但封装其行为集的可重用组件*称为**无头 UI组件**。通常，这些可以被设计为公开 API 表面以读取和控制其内部状态。这个概念是 shadcn/ui 的主要架构元素之一。

### Style 风格

The most tangible aspect of UI components are their visual presentation. All Components have a default style based on the overall visual theme of the project. The visual elements of a component is two-fold. First is the structural aspect of the component. Properties such as border radius, dimensions, spacing, font-sizes and font-weights contributes to this aspect. The other aspect is the visual style. Properties such as foreground and background colors, outline, border contributes to this aspect.  
UI 组件最有形的方面是它们的视觉呈现。*所有组件都有一个基于项目整体视觉主题的默认样式*。**组件的视觉元素有两个方面。首先是组件的结构方面：** 边框半径、尺寸、间距、字体大小和字体粗细等属性有助于这方面。**另一方面是视觉风格：** 前景色和背景色、轮廓、边框等属性有助于这方面。

Based on user interactions and application state, a UI component can be in different states. The visual style of a component should reflect the current state of the component and it should provide feedback to the users when they interact with it. Therefore different variations of the same UI component should be created order to accomplish this. These variations, often known as _variants_ are built by adjusting the structure and visual style of a component to communicate its state.  
*基于用户交互和应用程序状态，UI 组件可以处于不同的状态*。*组件的视觉样式应该反映组件的当前状态，并且应该在用户与之交互时向用户提供反馈*。**因此，应创建同一 UI 组件的不同变体才能实现此目的**。这些**变体**（通常称为_变体）_是通过调整组件的结构和视觉样式来传达其状态而构建的。

During the development lifecycle of a software application, a design team captures the visual theme, components and variants when developing high-fidelity mockups for the application. They would also document different intended behaviors of components as well. This type of a collective design documentation for a given software is usually known as the _Design System_.  
在软件应用程序的开发生命周期中，设计团队在为应用程序开发高保真模型时捕获视觉主题、组件和变体。他们还将记录组件的不同预期行为。给定软件的这种类型的集体设计文档通常称为**设计系统**。

Given a design system, the responsibility of a front-end team would be to express it in code. They should capture the global variables of the visual theme, reusable components and their variants. The main benefit of this approach is that any change done in the future to the design system can be efficiently reflected in code. This would unlock a friction-less workflow between the design and development teams.  
给定一个设计系统，前端团队的职责就是用代码来表达它。他们应该捕获视觉主题、可重用组件及其变体的全局变量。这种方法的主要好处是，将来对设计系统所做的任何更改都可以有效地反映在代码中。这将开启设计和开发团队之间无摩擦的工作流程。

## Architecture Overview 架构概述

As we have discussed before shadcn/ui is a mechanism by which design systems can be expressed in code. It enables a front-end team to take a design system and transfer it a format that can be utilized in the development process. I think that the architecture that enables this workflow is worthy of our review.  
正如我们之前讨论的，shadcn/ui 是一种可以用代码表达设计系统的机制。它使前端团队能够采用设计系统并将其转换为可在开发过程中使用的格式。我认为实现此工作流程的架构值得我们回顾。

We are able to generalize the design of all shadcn/ui components to the following architecture.  
我们能够将所有 shadcn/ui 组件的设计推广到以下架构。

![[libraries/shadcn/media/377288bd74ae430dc0b007607dfdcea7_MD5.png]]

shadcn/ui is built on the core principle that states _The design of your components should be separate from their implementation._ Therefore every component in shadcn/ui has a 2 layered architecture. Namely,  
shadcn/ui 建立在核心原则之上，即组件的设计应该与其实现分开。**因此，shadcn/ui 中的每个组件都有一个 2 层架构。即，**

1. Structure and behavior layer  结构与行为层
2. Style layer 样式层

### Structure and behavior layer  结构与行为层

In the structure and behavior layer, the components are implemented in their headless representations. As we discussed in the prologue, this means their structural composition and core behaviors are encapsulated within this representation. Even the difficult considerations such as keyboard navigation and WAI-ARIA adherence are also implemented by each component in this layer.  
*在结构和行为层中，组件以其无头表示形式实现*。正如我们在序言中所讨论的，**这意味着它们的结构组成和核心行为都封装在这种表示中**。*即使是键盘导航和 WAI-ARIA 遵守等困难的考虑因素也由该层中的每个组件实现*。

shadcn/ui has utilized some well established headless ui libraries for the components that cannot be implemented with only the native browser elements. Radix UI is one such key headless UI library that can be found in the shadcn/ui codebase. Several commonly used components like the Accordion, Popover, Tabs etc... are built on top of their Radix UI implementations.  
shadcn/ui 使用了一些完善的无头 ui 库来处理仅使用本机浏览器元素无法实现的组件。 *Radix UI 就是这样一个关键的无头 UI 库*，可以在 shadcn/ui 代码库中找到。几个常用的组件（如 Accordion、Popover、Tabs 等）都构建在其 Radix UI 实现之上。

![[libraries/shadcn/media/587c49c1e088d83acf91c3a5accc3a64_MD5.png]]

Native browser elements and Radix UI components are enough to satisfy most of the component requirements. But there are situations that warrant the usage of specialized headless UI libraries.  
原生浏览器元素和 Radix UI 组件足以满足大部分组件需求。但有些情况需要使用专门的无头 UI 库。

One such situation is form handling. For this purpose, shadcn/ui provides a `Form` component that is built on top of **React Hook Form** headless form library which handles the form state management requirements. shadcn/ui takes the primitives given by React Hook Form and wraps over them in a composable manner.  
*其中一种情况是表单处理*。为此，shadcn/ui 提供了一个基于**React Hook Form**无头表单库构建的`Form`组件，用于处理表单状态管理需求。 shadcn/ui 采用 React Hook Form 给出的原语并以可组合的方式包装它们。

To handle table views, shadcn/ui uses the headless table library **Tanstack React Table**. The shadcn/ui `Table` and `DataTable` components are built on top of this library. Tanstack React Table exposes a number of APIs to handle table view including filtering, sorting and virtualization.  
为了处理表视图，shadcn/ui 使用无头表库**Tanstack React Table** 。 shadcn/ui `Table`和`DataTable`组件构建在该库之上。 Tanstack React Table 公开了许多 API 来处理表视图，包括过滤、排序和虚拟化。

Calender views, DateTime pickers, and DateRange pickers are among some notorious components that are hard to get right. But shadcn/ui uses the package `React Day Picker` as the base component to implement the headless layer of aforementioned components.  
日历视图、日期时间选择器和日期范围选择器是一些臭名昭著的组件，很难正确使用。但 shadcn/ui 使用`React Day Picker`包作为基础组件来实现上述组件的无头层。

### Style layer 样式层

TailwindCSS lies at the core of the shadcn/ui style layer. However values for attributes such as color, border radius are exposed to the Tailwind configuration are placed in a global.css file as CSS variables. This can be used to manage variable values shared across the design system. If we are using Figma as the design tool this approach can be used to track the Figma variables we would have in a design system.  
TailwindCSS 位于 shadcn/ui 样式层的核心。然而，*颜色、边框半径等属性的值暴露给 Tailwind 配置*，并作为 *CSS 变量放置在 global.css 文件中*。*这可用于管理整个设计系统共享的变量值*。如果我们使用Figma作为设计工具，这种方法可以用来跟踪我们在设计系统中拥有的Figma变量。

In order to manage the differentiated styling for component variants, Class Variance Authority(CVA) is used by shadcn/ui. It provides a very expressive API surface to configure variant styling for each component.  
为了管理组件变体的差异化样式，shadcn/ui 使用了类差异权威（CVA）。它提供了一个非常富有表现力的 API 界面来为每个组件配置变体样式。

As we have discussed the high level architecture of shadcn/ui we can now dive deep into the implementation details of several components. We can start this discussion from one of the simplest components in shadcn/ui.  
正如我们已经讨论了 shadcn/ui 的高级架构一样，我们现在可以深入研究几个组件的实现细节。我们可以从 shadcn/ui 中最简单的组件之一开始讨论。

### shadcn/ui Badge

The implementation of the `<Badge />` component is relatively simple. Therefore it is a good starting point to see how the concepts we have discussed so far can be used to build reusable components.  
`<Badge />`组件的实现相对简单。因此，这是了解如何使用我们迄今为止讨论的概念来构建可重用组件的一个很好的起点。

```tsx
import * as React from "react";
import { cva, type VariantProps } from "class-variance-authority";

import { cn } from "@/lib/utils";

const badgeVariants = cva(
  "inline-flex items-center rounded-full border px-2.5 py-0.5 text-xs font-semibold transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2",
  {
    variants: {
      variant: {
        default: "border-transparent bg-primary text-primary-foreground hover:bg-primary/80",
        secondary: "border-transparent bg-secondary text-secondary-foreground hover:bg-secondary/80",
        destructive: "border-transparent bg-destructive text-destructive-foreground hover:bg-destructive/80",
        outline: "text-foreground",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
);

export interface BadgeProps extends React.HTMLAttributes<HTMLDivElement>, VariantProps<typeof badgeVariants> {}

function Badge({ className, variant, ...props }: BadgeProps) {
  return <div className={cn(badgeVariants({ variant }), className)} {...props} />;
}

export { Badge, badgeVariants };
```

#### cva

The implementation of the component starts with a call to the `cva` function from the library `class-variance-authority` . It is used to declare the variants of the component.  
组件的实现首先从类库`class-variance-authority`中调用`cva`函数。它用于*声明组件的变体*。

```jsx
const badgeVariants = cva(
  "inline-flex items-center rounded-full border px-2.5 py-0.5 text-xs font-semibold transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2",
  {
    variants: {
      variant: {
        default: "border-transparent bg-primary text-primary-foreground hover:bg-primary/80",
        secondary: "border-transparent bg-secondary text-secondary-foreground hover:bg-secondary/80",
        destructive: "border-transparent bg-destructive text-destructive-foreground hover:bg-destructive/80",
        outline: "text-foreground",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
);
```

The first argument to the `cva` function defines the base styles that is applied to all variants of the `<Badge/>` component. As the second argument, `cva` accepts a configuration object that defines the possible variants of the component and the default variant that should be used. We can also notice the usage of utility styles that consume the design system tokens that have been defined in the `tailwind.config.js` which opens up the possibility to easily update the look and feel by adjusting the CSS variables  
`cva`函数的**第一个参数**定义应用于`<Badge/>`组件的*所有变体的基本样式*。作为**第二个参数**， `cva`接受一个配置对象，该对象*定义组件的可能变体*以及*应使用的默认变体*。我们还可以注意到实用程序样式的使用，这些样式消费了`tailwind.config.js`中定义的设计系统令牌，*这提供了通过调整 CSS 变量轻松更新外观的可能性*

Invocation of the `cva` function returns another function; which an be used to apply the styles respective to each variant conditionally. We have stored it in a variable`badgeVariants` so that we can use it to apply the correct styles when the variant name is passed to the component as a prop.  
调用`cva`函数*返回另一个函数*；它可用于有条件地应用各自的样式到每个变体。我们将其存储在变量`badgeVariants`中，以便当变体名称作为 prop 传递给组件时，我们可以使用它来应用正确的样式。

#### BadgeProps

Then we can find the `BadgeProps` interface that defines the types for the component.  
然后我们可以找到定义组件类型的`BadgeProps`接口。

```jsx
export interface BadgeProps extends React.HTMLAttributes<HTMLDivElement>, VariantProps<typeof badgeVariants> {}
```

The base element for the badge component is a HTML `div`. Therefore, this component should be exposed to component consumers as an extension of the `div` element. This is achieved by extending the `React.HTMLAttributes<HTMLDivElement>` type. Additionally we need to expose a `variant` prop from the `<Badge/>` component to let the consumers render the required variant of the component. The helper type `VariantProps` allows us to expose the available variants as an Enum on the `variants` prop .  
徽章组件的基本元素是 HTML `div` 。因此，该组件应该作为`div`元素的扩展暴露给组件使用者。这是通过扩展`React.HTMLAttributes<HTMLDivElement>`类型来实现的。此外，我们需要从`<Badge/>`组件公开一个`variant`属性，以便让消费者渲染组件所需的变体。帮助器类型`VariantProps`允许我们将可用的变体作为`variants` prop 上的枚举公开。

```jsx
function Badge({ className, variant, ...props }: BadgeProps) {
  return <div className={cn(badgeVariants({ variant }), className)} {...props} />;
}
```

Finally we have the functional component that defines the `Badge`. Here we can notice that we are collating all props other than `className` and `variant` into a props object that we spread on the underlying div. This allows component consumers to interface with all the props available on a `div` element.  
最后，我们有了定义`Badge`的功能组件。在这里我们可以注意到，我们正在将除`className`和`variant`之外的所有 props 整理到我们传播到底层 div 上的 props 对象中。这允许组件使用者与`div`元素上可用的所有 props 进行交互。

#### 处理className

Notice how the style application is handled on the component. The value of the variant prop is passed into the `badgeVariants` function, which would return the class string that contains all the utility class names required to render the component variant. However we can notice that we are passing the return value of the aforementioned function and the values passed into the `className` prop through a function named `cn` before its evaluated into the `className` attribute of the div element.  
请注意组件上如何处理样式应用程序。 variant 属性的值被传递到`badgeVariants`函数中，该函数将返回包含呈现组件变体所需的所有实用程序类名称的类字符串。但是我们可以注意到，我们正在传递上述函数的返回值，以及通过名为`cn`的函数传递到`className` prop 的值，然后再将其计算到 div 元素的`className`属性中。

##### cn()

This is actually a special utility function that's provided by shadcn/ui. It's role is to act as a utility class management solution. Let's take a look at the implementation of it.  
这实际上是 shadcn/ui 提供的一个特殊实用函数。它的作用是充当实用程序类管理解决方案。我们来看看它的实现。

```jsx
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

This utility function is a amalgamation of 2 libraries that helps manage utility classes. The first library is `clsx`. It provides the ability to conditionally apply styles via className concatenation to a component.  
该实用程序函数是*两个库的合并*，有助于管理实用程序类。第一个库是`clsx` 。它提供了通过 className 连接有条件地将样式应用到组件的能力。

```jsx
import React from "react";

const Link = ({ isActive, children }: { isActive: boolean, children: React.ReactNode }) => {
  return <a className={clsx("text-lg", { "text-blue-500": isActive })}>{children}</a>;
};
```

Here, we can see a situation where `clsx` has been used independently. By default the only the `text-lg` utility class will be applied to the Link component. But when we pass in the `isActive` prop to the component as a true value, `text-blue-500` utility class will also be applied to the component.  
这里，我们可以看到`clsx`已经独立使用的情况。默认情况下，只有`text-lg`实用程序类将应用于链接组件。但是，当我们将`isActive`属性作为 true 值传递给组件时， `text-blue-500`实用程序类也将应用于该组件。

But there are situations where `clsx` alone can't be used to achieve our goals.  
*但在某些情况下，仅使用`clsx`无法实现我们的目标*。

```jsx
import React from "react";
import clsx from "clsx";

const Link = ({ isActive, children }: { isActive: boolean, children: React.ReactNode }) => {
  return <a className={clsx("text-lg text-grey-800", { "text-blue-500": isActive })}> {children}</a>;
};
```

In this situation, the color utility `text-grey-800` has been applied to the element by default. Our goal is to change the text color to `blue-500` when the `isActive` prop becomes true. But due the nature how the CSS Cascade affects Tailwind, the color style applied by the `text-grey-800` will not be modified.  
在这种情况下，颜色实用程序`text-grey-800`已默认应用于该元素。我们的目标是当`isActive`属性变为 true 时将文本颜色更改为`blue-500` 。但由于 CSS Cascade 影响 Tailwind 的性质， *`text-grey-800`应用的颜色样式将不会被修改*。

This is where the library `tailwind-merge` comes in. If we modify the above code using `tailwind-merge` ,  
*这就是库`tailwind-merge`的用武之地*。如果我们使用`tailwind-merge`修改上面的代码，

```jsx
import React from "react";
import { twMerge } from "tailwind-merge";
import clsx from "clsx";

const Link = ({ isActive, children }: { isActive: boolean, children: React.ReactNode }) => {
  return <a className={twMerge(clsx("text-lg text-grey-800", { "text-blue-500": isActive }))}>{children}</a>;
};
```

Now, the output of `clsx` will be passed through `tailwind-merge` . `taiwlind-merge` will parse the the class string and shallow merges the style definition. Which means the `text-grey-800` is replaced by `text-blue-500` so that the element reflects new styles that were applied conditionally.  
现在， `clsx`的输出将通过`tailwind-merge`传递。 `taiwlind-merge`将解析类字符串并浅层合并样式定义。*这意味着`text-grey-800`被`text-blue-500`替换*，以便元素反映有条件应用的新样式。

This approach helps us make sure there won't be any style conflicts in our variant implementations. Since the `className` prop also passes through the `cn` util, it makes it really easy to override any styles if required. But this comes with a trade-off. Utilization of `cn` opens up the possibility for a component consumer to override the styles in an ad-hoc manner. Which would delegate some level of responsibility to he code review step to verify `cn` has not been abused. On the other hand, if you do not need to enable this behavior at all you can modify the component to use `clsx` only.  
这种方法可以帮助我们确保我们的变体实现中不会出现任何样式冲突。由于`className`属性也通过`cn` util，因此如果需要的话，可以很容易地覆盖任何样式。但这需要权衡。 *`cn`的使用为组件使用者提供了以临时方式覆盖样式的可能性*。这会将一定程度的责任委托给代码审查步骤，以验证`cn`没有被滥用。另一方面，如果您根本不需要启用此行为，您可以修改组件以仅使用`clsx` 。

#### 原则

When we have analyze the implementation of the `Badge` component, we can notice some patterns, including some associated with SOLID:  
当我们分析`Badge`组件的实现时，我们可以注意到一些模式，包括一些与 SOLID 相关的模式：

1. **Single Responsibility Principle (SRP):  
    单一职责原则（SRP）：**
    
    - The `Badge` component appears to have a single responsibility, which is to render a badge with different styles based on the provided variant. It delegates the management of styles to the `badgeVariants` object.  
        `Badge`组件似乎只有一个职责，即根据提供的变体呈现具有不同样式的徽章。它将样式管理委托给`badgeVariants`对象。
2. **Open/Closed Principle (OCP):  
    开闭原理（OCP）：**
    
    - The code seems to follow the open/closed principle by allowing for the addition of new variants without modifying the existing code. New variants can be easily added to the `variants` object in the `badgeVariants` definition.  
        该代码似乎遵循开放/封闭原则，允许添加新变体而不修改现有代码。新变体可以轻松添加到`badgeVariants`定义中的`variants`对象中。
        
    - But there's a caveat, due to how `cn` is utilized it is possible for a component consumer to pass new overriding styles using the `className` attribute. This could open the component for modification. Therefore, when you are building your own component library with shadcn/ui decide whether you should allow that behavior or not.  
        但有一个警告，由于`cn`使用方式，组件使用者可以使用`className`属性传递新的覆盖样式。这可以打开组件进行修改。因此，当您使用 shadcn/ui 构建自己的组件库时，请决定是否应该允许该行为。
        
3. **Dependency Inversion Principle (DIP):  
    依赖倒置原则（DIP）：**
    
    - The `Badge` component and its styling are defined separately. The `Badge` component depends on the `badgeVariants` object for styling information. This separation allows for flexibility and easier maintenance, adhering to the Dependency Inversion Principle.  
        `Badge`组件及其样式是单独定义的。 `Badge`组件依赖`badgeVariants`对象来获取样式信息。这种分离允许灵活性和更容易的维护，并遵循依赖倒置原则。
4. **Consistency and Reusability:  
    一致性和可重用性：**
    
    - The code promotes consistency by using the utility function `cva` to manage and apply styles based on variants. This consistency can make it easier for developers to understand and use the component. Additionally, the `Badge` component is reusable and can be easily integrated into different parts of an application.  
        该代码通过使用实用函数`cva`来管理和应用基于变体的样式，从而提高一致性。这种一致性可以使开发人员更容易理解和使用该组件。此外， `Badge`组件是可重用的，并且可以轻松集成到应用程序的不同部分中。
5. **Separation of Concerns: 关注点分离：**
    
    - The concerns of styling and rendering are separated. The `badgeVariants` object handles the styling logic, while the `Badge` component is responsible for rendering and applying the styles.  
        样式和渲染的关注点是分开的。 `badgeVariants`对象处理样式逻辑，而`Badge`组件负责渲染和应用样式。

After analyzing the implementation of the `Badge` component, we now have a detailed understanding of the general architecture of shadcn/ui. But this was purely a display level component. So let's take some time to look at a few more interactive components.  
分析完`Badge`组件的实现，现在我们对shadcn/ui的总体架构有了详细的了解。但这纯粹是一个显示级别的组件。因此，让我们花一些时间来看看更多的交互式组件。

### shadcn/ui Switch 

```jsx
import * as React from "react"
import * as SwitchPrimitives from "@radix-ui/react-switch"

import { cn } from "@/lib/utils"

const Switch = React.forwardRef<
  React.ElementRef<typeof SwitchPrimitives.Root>,
  React.ComponentPropsWithoutRef<typeof SwitchPrimitives.Root>
>(({ className, ...props }, ref) => (
  <SwitchPrimitives.Root
    className={cn(
      "peer inline-flex h-6 w-11 shrink-0 cursor-pointer items-center rounded-full border-2 border-transparent transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 focus-visible:ring-offset-background disabled:cursor-not-allowed disabled:opacity-50 data-[state=checked]:bg-primary data-[state=unchecked]:bg-input",
      className
    )}
    {...props}
    ref={ref}
  >
    <SwitchPrimitives.Thumb
      className={cn(
        "pointer-events-none block h-5 w-5 rounded-full bg-background shadow-lg ring-0 transition-transform data-[state=checked]:translate-x-5 data-[state=unchecked]:translate-x-0"
      )}
    />
  </SwitchPrimitives.Root>
))
Switch.displayName = SwitchPrimitives.Root.displayName

export { Switch }
```

Here we have the `Switch` component which is commonly found in modern user interfaces to toggle a certain field between 2 values. Unlike the `Badge` component which was purely presentational, `Switch` is an interactive component that responds to user input and toggles its state. It also communicates its current state to the user via its visual style.  
这里我们有现代用户界面中常见的`Switch`组件，用于在 2 个值之间切换某个字段。与纯粹展示性的`Badge`组件不同， `Switch`是一个交互式组件，可以响应用户输入并切换其状态。它还通过视觉风格向用户传达其当前状态。

The primary method that a user could interact with a switch component by clicking/tapping the switch with a pointing device. Even though building a switch component that responds to pointer events is pretty straightforward, the implementation significantly increases in complexity when we need the switch to respond to keyboard interactions and screen readers as well. Some expected behaviors for the switch component can be identified as follows, 1. Responds to `Tab` key presses by focusing on the switch. 2. Once focused, pressing enter would toggle the state of the switch. 3. In the presence of a screen reader it should announce its current state to the user.  
用户通过使用定点设备单击/轻击开关来与开关组件交互的主要方法。尽管构建一个响应指针事件的开关组件非常简单，*但当我们需要开关响应键盘交互和屏幕阅读器时，实现的复杂性会显着增加*。开关组件的一些预期行为可以如下确定： 1. 通过关注开关来响应`Tab`键按下。 2. 聚焦后，按 Enter 键将切换开关的状态。 3. 在存在屏幕阅读器的情况下，它应该向用户宣布其当前状态。

If we analyze the code carefully, we can notice that the actual structure of switch is built-up via the usage of the `<SwitchPrimitives.Root/>` and `<SwitchPrimitives.Thumb/>` compound components. These components are sourced from the RadixUI headless library and contains all the implementations for the expected behavior for a switch. We can also notice the utilization of the `React.forwardRef` to build this component. This makes it possible for this component to be bound to incoming `ref` s. Which is a useful feature when there's is a need to track the focus states and integration with certain external libraries. (Ex: In order to use the component as an input component with the React Hook Form library it should be focusable via a `ref`).  
如果仔细分析代码，我们可以注意到 switch 的实际结构是通过使用`<SwitchPrimitives.Root/>`和`<SwitchPrimitives.Thumb/>`复合组件构建的。这些组件源自 RadixUI 无头库，包含switch预期行为的所有实现。我们还可以注意到使用`React.forwardRef`来构建这个组件。这使得该组件可以绑定到传入的`ref` 。当需要跟踪焦点状态以及与某些外部库集成时，这是一个有用的功能。 （例如：为了将该组件用作 React Hook Form 库的输入组件，它应该可以通过`ref`获得焦点）。

As we discussed before, RadixUI components doesn't provide any styling. Therefore the styles have been applied to this component via the className prop directly after passing through the `cn` utility function. We can also create variants for the component if required by using `cva`.  
正如我们之前讨论的，RadixUI 组件不提供任何样式。因此，在通过`cn`实用函数之后，样式已直接通过 className 属性应用到该组件。如果需要，我们还可以使用`cva`创建组件的变体。

## Conclusion 结论

The architecture and anatomy of shadcn/ui that we have discussed so far is implemented in the same manner in rest of the shadcn/ui components as well. However, the behaviors and implementations of certain components are slightly more complex. Discussion of the architecture of these components deserves their own articles. Therefore I would not go into lengths but provide an overview of their structure.  
到目前为止，我们讨论的 shadcn/ui 的架构和剖析在其他 shadcn/ui 组件中也以相同的方式实现。然而，某些组件的行为和实现稍微复杂一些。对这些组件的架构的讨论值得单独撰写文章。因此，我不会长篇大论，但会概述它们的结构。

1. `Calendar`
    
    - Uses `react-day-picker` as the headless component.  
        使用`react-day-picker`作为无头组件。
    - Uses `date-fns` as the DateTime formatting library.  
        使用`date-fns`作为日期时间格式库。
2. `Table` and `DataTable`  
    `Table`和`DataTable`
    
    - Uses `@tanstack/react-table` as the headless table library.  
        使用`@tanstack/react-table`作为无头表库。
3. `Form`
    
    - Uses `react-hook-form` as the form and form state management library as the headless component.  
        使用`react-hook-form`作为表单，使用表单状态管理库作为无头组件。
    - Utility components that encapsulates form logic are exposed by shadcn/ui. These can be used to assemble parts of the form including inputs and error messages.  
        封装表单逻辑的实用程序组件由 shadcn/ui 公开。这些可用于组装表单的各个部分，包括输入和错误消息。
    - `zod` is used as the schema validation library for the form. Validation errors returned by `zod` are passed into `<FormMessage/>` components that display the errors beside form inputs.  
        `zod`用作表单的模式验证库。 `zod`返回的验证错误将传递到`<FormMessage/>`组件中，该组件在表单输入旁边显示错误。

shadcn/ui introduced a new paradigm in thinking about front-end development. Instead of relying on third party packages that that abstracts the whole component, we could own the implementation of the components and only expose the required elements. Rather than being limited to the opinionated API surface of a pre-built component library when applying your design system, build your own design system with good enough defaults that you can customize later.  
**shadcn/ui 引入了前端开发思维的新范式**。我们可以拥有组件的实现并只公开所需的元素，而不是依赖抽象整个组件的第三方包。在应用您的设计系统时，不要局限于预构建组件库的固执己见的 API 表面，而是构建您自己的设计系统，并使用足够好的默认值以便您以后可以自定义。