# 总结

该网页是React Aria关于样式（Styling）的文档，主要介绍了React Aria组件的样式处理方式，支持多种样式解决方案，核心要点如下：


### 一、基础样式机制
1. **类名（Class names）**  
   - 组件默认支持`className`和`style`属性，兼容原生CSS、Tailwind、CSS-in-JS等方案。  
   - 未自定义`className`时，组件自带默认类名（遵循`react-aria-ComponentName`命名规则，如`react-aria-Select`），可直接用于CSS样式编写；自定义`className`会覆盖默认类名。  


2. **状态（States）**  
   - 组件通过数据属性（data attributes）暴露UI状态（如选中、聚焦、禁用等），类似自定义CSS伪类，例如`data-selected`、`data-focused`，可在CSS中通过属性选择器（如`.react-aria-ListBoxItem[data-selected]`）定位样式。  


3. **渲染属性（Render props）**  
   - `className`和`style`可接收函数，根据组件状态动态返回样式（如`className={({isSelected}) => isSelected ? 'selected' : 'unselected'}`）。  
   - 作为子元素时，可修改默认渲染内容（如条件渲染图标），或通过`defaultClassName`、`defaultStyle`等合并默认样式。  


4. **插槽（Slots）**  
   - 同一组件的多个子元素通过`slot`属性区分（如NumberField的`increment`/`decrement`按钮），可在CSS中通过`[slot=xxx]`选择器单独样式化。  


5. **CSS变量（CSS variables）**  
   - 部分组件提供内置CSS变量（如Select的`--trigger-width`），用于保持样式一致性（如使弹出框宽度与触发按钮一致）。  


### 二、Tailwind CSS集成
1. **基础用法**  
   - 直接使用数据属性作为修饰符（如`data-[selected]:bg-blue-400`），或通过渲染属性动态应用类名。  
   - 支持通过`var(--xxx)`使用组件的CSS变量（如`w-[--trigger-width]`）。  


2. **专用插件**  
   - 安装`tailwindcss-react-aria-components`插件后，可省略`data-`前缀（如`selected:bg-blue-400`），并支持编辑器自动补全。  
   - 支持布尔状态（如`pressed:bg-blue`）和非布尔状态（如`orientation-vertical:flex-row`），可自定义修饰符前缀（如`rac-selected:xxx`）。  


### 三、动画（Animation）
1. **CSS过渡与关键帧**  
   - 覆盖类组件（如Popover、Modal）通过`data-entering`（进入起始状态）和`data-exiting`（退出结束状态）支持过渡/动画，需在默认状态或退出状态定义过渡属性。  


2. **Tailwind CSS动画**  
   - 结合`tailwindcss-animate`插件，使用`data-[entering]:animate-in`等工具类实现常见动画（如淡入、滑动）。  


3. **Framer Motion集成**  
   - 通过`motion()`包装组件创建动画包装器，支持`initial`、`animate`等动画属性。  
   - 处理退出动画时，需使用`isExiting`属性保持元素在DOM中，结合状态跟踪（如`unmounted`/`hidden`/`visible`）确保动画完整执行；集合组件可配合`AnimatePresence`实现增删动画。  


文档还明确各组件的默认类名、状态、插槽及CSS变量可在对应组件的样式章节查询，便于针对性样式开发。

---

# Styling 
样式设置

React Aria Components do not include any styles by default, allowing you to build custom designs to fit your application or design system using any styling solution.React Aria 组件默认不包含任何样式，这使你能够使用任何样式解决方案构建适合你的应用程序或设计系统的自定义设计。

## Class names

---

Each component accepts the standard `className` and `style` props which enable using vanilla CSS, utility classes (e.g. Tailwind), CSS-in-JS (e.g. Styled Components), etc.

每个组件都接受标准的 `className` 和 `style` 属性，这些属性支持使用普通 CSS、实用类（如 Tailwind）、CSS-in-JS（如 Styled Components）等。

When a custom `className` is not provided, each component includes a default class name following the `react-aria-ComponentName` naming convention. You can use this to style a component with standard CSS without needing any custom classes.

当未提供自定义的 `className` 时，每个组件都会包含一个遵循 `react-aria-ComponentName` 命名约定的默认类名。你可以使用这个默认类名通过标准CSS对组件进行样式设置，而无需任何自定义类。

```
.react-aria-Select {
  /* ... */
}
```

A custom `className` can also be specified on any component. This overrides the default `className` provided by React Aria with your own.

也可以在任何组件上指定自定义的`className`。这会用你自己的类名覆盖React Aria提供的默认`className`。

```
<Select className="my-select">
  {/* ... */}
</Select>
```

The default class names for each component are listed in the Styling section of their documentation.

每个组件的默认类名在其文档的“样式设置”部分列出。

## States

---

Components often support multiple UI states (e.g. pressed, hovered, selected, etc.). React Aria Components exposes states using data attributes, which you can target in CSS selectors. They can be thought of like custom CSS pseudo classes. For example:

组件通常支持多种用户界面状态（例如按下、悬停、选中等等）。React Aria 组件使用数据属性来公开状态，你可以在 CSS 选择器中定位这些属性。它们可以被视为类似于自定义 CSS 伪类。例如：

```
.react-aria-ListBoxItem[data-selected] {
  /* ... */
}

.react-aria-ListBoxItem[data-focused] {
  /* ... */
}
```

In order to ensure high quality interactions across browsers and devices, React Aria Components includes states such as `data-hovered` and `data-pressed` which are similar to CSS pseudo classes such as `:hover` and `:active`, but work consistently between mouse, touch, and keyboard modalities. You can read more about this in our [blog post series](https://react-spectrum.adobe.com/blog/building-a-button-part-1.html) and our [Interactions](https://react-spectrum.adobe.com/react-aria/interactions.html) overview.

为确保在各种浏览器和设备上实现高质量交互，React Aria 组件包含诸如 `data-hovered` 和 `data-pressed` 之类的状态，它们类似于 `:hover` 和 `:active` 等 CSS 伪类，但在鼠标、触摸和键盘操作方式之间保持一致。你可以在我们的 [博客文章系列](https://react-spectrum.adobe.com/blog/building-a-button-part-1.html) 以及 [交互](https://react-spectrum.adobe.com/react-aria/interactions.html) 概述中了解更多相关信息。

All states supported by each component are listed in the Styling section of their documentation.
每个组件支持的所有状态都列在其文档的“样式设置”部分中。

## Render props
渲染属性

---

The `className` and `style` props also accept functions which receive states for styling. This lets you dynamically determine the classes or styles to apply.
`className` 和 `style` 属性也接受函数，这些函数接收用于设置样式的状态。这使你能够动态确定要应用的类或样式。

```
<ListBoxItem
  className={({ isSelected }) => isSelected ? 'selected' : 'unselected'}
>
  Item
</ListBoxItem>
```

Render props may also be used as children to alter what elements are rendered based on the current state. For example, you could render a checkmark icon when an item is selected.
渲染属性也可以用作子元素，以便根据当前状态改变渲染的元素。例如，当某个项目被选中时，你可以渲染一个复选标记图标。

```tsx
<ListBoxItem>
  {({isSelected}) => (
    <>
      {isSelected && <CheckmarkIcon />}
      <span>Item</span>
    </>
  )}
</ListBoxItem>
```

Render props also let you modify the default values provided by React Aria via the `defaultClassName`, `defaultStyle`, and `defaultChildren` options. For example, you could wrap the default children of a `SelectValue` in an extra element, append an additional class name to React Aria's default, or merge default inline styles with your own.
渲染属性还允许你通过 `defaultClassName`、`defaultStyle` 和 `defaultChildren` 选项修改 React Aria 提供的默认值。例如，你可以将 `SelectValue` 的默认子元素包装在一个额外的元素中，在 React Aria 的默认类名基础上追加一个额外的类名，或者将默认内联样式与你自己的样式合并。

```tsx
<SelectValue>
  {({defaultChildren}) => <span>{defaultChildren}</span>}
</SelectValue>
```

The render props exposed for each component are listed in the Styling section of their documentation.
每个组件暴露的渲染属性列在其文档的“样式设置”部分。

## Slots
插槽

---

Some components include multiple instances of the same component as children. These use the `slot` prop to distinguish them, which can also be used in CSS for styling purposes. This example targets the increment and decrement buttons in a [NumberField](https://react-spectrum.adobe.com/react-aria/NumberField.html).
一些组件包含多个相同组件的实例作为子组件。这些组件使用 `slot` 属性来区分它们，该属性也可用于CSS进行样式设置。此示例针对 [NumberField](https://react-spectrum.adobe.com/react-aria/NumberField.html) 中的增加和减少按钮。

```tsx
<NumberField>
  <Label>Width</Label>
  <Group>
    <Input />
    <Button slot="increment">+</Button>
    <Button slot="decrement">-</Button>  </Group>
</NumberField>
```

```scss
.react-aria-NumberField {
  [slot=increment] {     
    border-radius: 4px 4px 0 0;
  }

  [slot=decrement] {     
    border-radius: 0 0 4px 4px;
  }
}
```

The slots supported by each component are shown in the Anatomy section of their documentation.每个组件支持的插槽在其文档的“剖析”部分中展示。

## CSS variables

---

Some components provide CSS variables that you can use in your styling code. For example, the [Select](https://react-spectrum.adobe.com/react-aria/Select.html) component provides a `--trigger-width` variable on the popover that is set to the width of the trigger button. You can use this to make the width of the popover match the width of the button.
一些组件提供了CSS变量，你可以在样式代码中使用。例如，[Select](https://react-spectrum.adobe.com/react-aria/Select.html) 组件在弹出框上提供了一个 `--trigger-width` 变量，该变量设置为触发按钮的宽度。你可以使用这个变量使弹出框的宽度与按钮的宽度相匹配。

```css
.react-aria-Popover {
  width: var(--trigger-width);
}
```

The CSS variables provided by each component are listed in the Styling section of their documentation.
每个组件提供的CSS变量列在其文档的“样式设置”部分中。

## Tailwind CSS

---

[Tailwind CSS](https://tailwindcss.com/) is a utility-first CSS framework for rapid styling that works great with React Aria Components. To access [states](https://react-spectrum.adobe.com/react-aria/styling.html#states), you can use [data attributes](https://tailwindcss.com/docs/hover-focus-and-other-states#data-attributes) as modifiers:
Tailwind CSS是一款以实用程序优先的CSS框架，用于快速进行样式设计，与React Aria组件配合使用效果极佳。若要访问“状态”，可以使用“数据属性”作为修饰符：

```tsx
<ListBoxItem className="data-[selected]:bg-blue-400 data-[disabled]:bg-gray-100">
  Item
</ListBoxItem>
```

Alternatively, you can use [render props](https://react-spectrum.adobe.com/react-aria/styling.html#render-props) to control which Tailwind classes are applied based on states. This can be useful if you need to apply multiple classes based on a single state:
或者，你可以使用 [渲染属性](https://react-spectrum.adobe.com/react-aria/styling.html#render-props) 来根据状态控制应用哪些Tailwind类。如果你需要根据单个状态应用多个类，这可能会很有用：

```tsx
<Radio
  className={({isFocusVisible, isSelected}) => `
    flex rounded-lg p-4
    ${isFocusVisible ? 'ring-2 ring-blue-600 ring-offset-1' : ''}
    ${isSelected ? 'bg-blue-600 border-white/30 text-white' : ''}
  `}>
  {/* ... */}
</Radio>
```

To access [CSS variables](https://react-spectrum.adobe.com/react-aria/styling.html#css-variables), use Tailwind's [arbitrary value](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values) syntax.
要访问[CSS变量](https://react-spectrum.adobe.com/react-aria/styling.html#css-variables)，请使用Tailwind的[任意值](https://tailwindcss.com/docs/adding-custom-styles#using-arbitrary-values)语法。

```tsx
<Popover className="w-[--trigger-width]">
  {/* ... */}
</Popover>
```

### Plugin

A Tailwind CSS plugin is also available to make styling states of React Aria Components easier, with shorter names and autocomplete in your editor. To install:
也提供了一个Tailwind CSS插件，通过更短的名称以及编辑器中的自动补全功能，让设置React Aria组件的样式状态变得更加容易。安装方法如下：

```bash
yarn add tailwindcss-react-aria-components
```

When using Tailwind v4, add it to your CSS:
使用Tailwind v4时，将其添加到你的CSS中：

```css
@import "tailwindcss";
@plugin "tailwindcss-react-aria-components";
```

Tailwind v3

With the plugin installed, you can now access all states without the `data-` prefix. If you have the [Tailwind VSCode Extension](https://tailwindcss.com/docs/editor-setup#intelli-sense-for-vs-code) installed, you'll also get autocomplete for all states in your editor.
安装该插件后，现在你无需使用 `data-` 前缀即可访问所有状态。如果你安装了 [Tailwind VSCode 扩展](https://tailwindcss.com/docs/editor-setup#intelli-sense-for-vs-code)，在编辑器中所有状态还会有自动补全功能。

```tsx
<ListBoxItem className="selected:bg-blue-400 disabled:bg-gray-100">
  Item
</ListBoxItem>
```

### Boolean states

Boolean states such as `data-pressed` can be styled with `pressed:` like this:
诸如`data-pressed`这样的布尔状态，可以使用`pressed:` 进行如下样式设置：

```tsx
<Button className="pressed:bg-blue">
  {/* ... */}
</Button>
```

### Non-boolean states

Non-boolean states follow the `{name}-{value}` pattern, so you can style an element with `data-orientation="vertical"` using `orientation-vertical:`.
非布尔状态遵循 `{name}-{value}` 模式，因此你可以使用 `orientation-vertical:` 对带有 `data-orientation="vertical"` 的元素进行样式设置。

```tsx
<Tabs className="orientation-vertical:flex-row">
  {/* ... */}
</Tabs>
```

### Modifier prefix
修饰符前缀

By default, all modifiers are unprefixed (e.g. `disabled:`), and generate CSS that automatically handles both React Aria Components and native CSS pseudo classes when the names conflict. If you prefer, you can optionally prefix all React Aria Components modifiers with a string of your choice.
默认情况下，所有修饰符都无前缀（例如 `disabled:`），并且在名称冲突时会生成自动处理React Aria组件和原生CSS伪类的CSS。如果你愿意，还可以选择用你自己选择的字符串作为所有React Aria组件修饰符的前缀。

```
@plugin "tailwindcss-react-aria-components" { prefix: rac };
```

Tailwind v3

With this configured, all states for React Aria Components can be accessed with that prefix.
配置完成后，可通过该前缀访问React Aria组件的所有状态。

```tsx
<ListBoxItem className="rac-selected:bg-blue-400 rac-disabled:bg-gray-100">
  Item
</ListBoxItem>
```

## Animation

---

React Aria Components supports both [CSS transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions/Using_CSS_transitions) and [keyframe animations](https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes), and works with JavaScript animation libraries like [Framer Motion](https://www.framer.com/motion/).
React Aria 组件同时支持[CSS 过渡](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions/Using_CSS_transitions)和[关键帧动画](https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes)，并且可与诸如[Framer Motion](https://www.framer.com/motion/)之类的 JavaScript 动画库配合使用。

### CSS transitions

Overlay components such as [Popover](https://react-spectrum.adobe.com/react-aria/Popover.html) and [Modal](https://react-spectrum.adobe.com/react-aria/Modal.html) support entry and exit animations via the `[data-entering]` and `[data-exiting]` states, or via the corresponding render prop functions.
诸如[弹出框](https://react-spectrum.adobe.com/react-aria/Popover.html)和[模态框](https://react-spectrum.adobe.com/react-aria/Modal.html)之类的覆盖组件通过`[data-entering]`和`[data-exiting]`状态，或通过相应的渲染属性函数，支持进入和退出动画。

- `[data-entering]` represents the starting state of the entry animation. The component will transition from the entering state to the default state when it opens.`[data-entering]` 表示进入动画的起始状态。组件打开时，将从进入状态过渡到默认状态。
- `[data-exiting]` represents the ending state of the exit animation. The component will transition from the default state to the exiting state and wait for any animations to complete before being removed from the DOM.`[data-exiting]` 表示退出动画的结束状态。组件将从默认状态过渡到退出状态，并等待所有动画完成后，再从 DOM 中移除。

```scss
.react-aria-Popover {
  transition: opacity 300ms;

  &[data-entering],
  &[data-exiting] {
    opacity: 0;
  }
}
```

Note that the `[data-entering]` state is only applied for one frame when using CSS transitions. The transition itself should be assigned in the default state. To create a different exit animation, assign the transition in the `[data-exiting]` state.
请注意，在使用CSS过渡时，`[data-entering]`状态仅应用于一帧。过渡本身应在默认状态下指定。要创建不同的退出动画，请在`[data-exiting]`状态下指定过渡。

```scss
.react-aria-Popover {
  /* entry transition */
  transition: transform 300ms, opacity 300ms;

  /* starting state of the entry transition */
  &[data-entering] {
    opacity: 0;
    transform: scale(0.8);
  }

  &[data-exiting] {
    /* exit transition */
    transition: opacity 150ms;
    /* ending state of the exit transition */
    opacity: 0;
  }
}
```

### CSS animations

For more complex animations, you can also apply CSS keyframe animations using the same `[data-entering]` and `[data-exiting]` states.
对于更复杂的动画，你还可以使用相同的 `[data-entering]` 和 `[data-exiting]` 状态来应用CSS关键帧动画。

```scss
.react-aria-Popover[data-entering] {
  animation: slide 300ms;
}

.react-aria-Popover[data-exiting] {
  animation: slide 300ms reverse;
}

@keyframes slide {
  from {
    transform: translateY(-20px);
    opacity: 0;
  }

  to {
    transform: translateY(0);
    opacity: 1;
  }
}
```

Note that unlike CSS transitions, keyframe animations are not interruptible. If the user opens and closes an overlay quickly, the animation may appear to jump to the ending state before the next animation starts.
请注意，与CSS过渡不同，关键帧动画是不可中断的。如果用户快速打开和关闭覆盖层，动画可能会在下一个动画开始前直接跳到结束状态。

### Tailwind CSS

If you are using Tailwind CSS, we recommend using the [tailwindcss-animate](https://github.com/jamiebuilds/tailwindcss-animate) plugin. This includes utilities for building common animations such as fading, sliding, and zooming.
如果你正在使用Tailwind CSS，我们建议使用 [tailwindcss-animate](https://github.com/jamiebuilds/tailwindcss-animate) 插件。这包括用于构建常见动画（如淡入淡出、滑动和缩放）的实用程序。

```tsx
<Popover className="data-[entering]:animate-in data-[entering]:fade-in data-[exiting]:animate-out data-[exiting]:fade-out">
  {/* ... */}
</Popover>
```

### Framer Motion

[Framer Motion](https://www.framer.com/motion/) and other JavaScript animation libraries can also be used with React Aria Components. Use the [motion](https://www.framer.com/motion/component/#custom-components) function to create a wrapper component that adds support for Framer Motion's animation props.
[Framer Motion](https://www.framer.com/motion/) 等 JavaScript 动画库也可与 React Aria 组件搭配使用。使用 [motion](https://www.framer.com/motion/component/#custom-components) 函数创建一个包装组件，以支持 Framer Motion 的动画属性。

```tsx
import {Modal, ModalOverlay} from 'react-aria-components';
import {motion} from 'framer-motion';

// Create Framer Motion wrappers.
const MotionModal = motion(Modal);
const MotionModalOverlay = motion(ModalOverlay);
```

This enables using props like [animate](https://www.framer.com/motion/animation/) with React Aria Components.
这使得可以在React Aria组件中使用诸如 [animate](https://www.framer.com/motion/animation/) 之类的属性。

```tsx
<MotionModal
  initial={{opacity: 0}}
  animate={{opacity: 1}}>
  {/* ... */}
</MotionModal>
```

Overlay exit animations can be implemented using the `isExiting` prop, which keeps the element in the DOM until an animation is complete. Framer Motion's [variants](https://www.framer.com/motion/animation/#variants) are a good way to setup named animation states.
可以使用`isExiting`属性来实现叠加层退出动画，该属性会在动画完成之前将元素保留在DOM中。Framer Motion的[变体](https://www.framer.com/motion/animation/#variants)是设置命名动画状态的好方法。

```tsx
type AnimationState = 'unmounted' | 'hidden' | 'visible';

function Example() {
  // Track animation state.
  let [animation, setAnimation] = React.useState<AnimationState>('unmounted');
  return (
    <DialogTrigger
      // Start animation when open state changes.
      onOpenChange={(isOpen) => setAnimation(isOpen ? 'visible' : 'hidden')}    >
      <Button>Open dialog</Button>
      <MotionModalOverlay
        // Prevent modal from unmounting during animation.
        isExiting={animation === 'hidden'}
        // Reset animation state once it is complete.
        onAnimationComplete={(animation) => {
          setAnimation((a) =>
            animation === 'hidden' && a === 'hidden' ? 'unmounted' : a
          );
        }}        variants={{
          hidden: { opacity: 0 },
          visible: { opacity: 1 }
        }}
        initial="hidden"
        animate={animation}
      >
        <MotionModal
          variants={{
            hidden: { opacity: 0, y: 32 },
            visible: { opacity: 1, y: 0 }
          }}
        >
          {/* ... */}
        </MotionModal>
      </MotionModalOverlay>
    </DialogTrigger>
  );
}
```

**Note**: Framer Motion's `AnimatePresence` component may not work with React Aria overlays in all cases, so the example shown above is the recommended approach for exit animations.
**注意**：Framer Motion的`AnimatePresence`组件并非在所有情况下都能与React Aria覆盖层配合使用，因此上面展示的示例是退场动画的推荐方法。

The [AnimatePresence](https://www.framer.com/motion/animate-presence/) component allows you to animate when items are added or removed in collection components. Use `array.map` to create children, and make sure each child has a unique `key` in addition to an `id` to ensure Framer Motion can track it.
[AnimatePresence](https://www.framer.com/motion/animate-presence/) 组件允许你在集合组件中添加或移除项目时进行动画处理。使用 `array.map` 创建子元素，并确保每个子元素除了有 `id` 之外，还有一个唯一的 `key`，以确保 Framer Motion 能够跟踪它。

```tsx
import {GridList, GridListItem} from 'react-aria-components';
import {motion, AnimatePresence} from 'framer-motion';

const MotionItem = motion(GridListItem);

<GridList>
  <AnimatePresence>
    {items.map(item => (
      <MotionItem
        key={item.id}
        id={item.id}
        layout
        exit={{opacity: 0}}>
        {/* ... */}
      </MotionItem>
    ))}
  </AnimatePresence>
</GridList>
```

