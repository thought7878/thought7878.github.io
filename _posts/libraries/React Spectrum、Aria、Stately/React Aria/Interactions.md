# React Aria 交互总结

## 核心定位
React Aria 提供跨设备交互的钩子函数，确保组件在不同浏览器和平台上行为一致，以实现高质量的用户体验。

## 关键内容

### 1. 交互实现的挑战与解决方案
- **挑战**：Web 覆盖设备（桌面、移动设备、电视等）和交互模式（鼠标、触摸、键盘等）多样，但 Web 平台缺乏高层抽象，依赖开发者组合低阶事件，导致跨设备一致性差。
- **解决方案**：React Aria 提供高层抽象的钩子函数，支持“press”“hover”“focus”等高阶事件，规范化跨浏览器和设备的行为差异。


### 2. 指针事件处理
- **背景**：Web 最初为鼠标设计，触摸设备出现后浏览器需模拟鼠标事件（如触摸触发`onMouseEnter`/`onMouseLeave`），但体验欠佳；移动浏览器会延迟`onClick`等事件以判断用户意图（缩放、滚动等）。
- **React Aria 处理方式**：优先使用新的指针事件规范，旧设备回退到鼠标和触摸事件；同时支持键盘和焦点事件，确保非鼠标/触摸用户可交互。


### 3. 键盘与焦点行为
- **键盘导航**：基于“制表位（tab stops）”，通过`Tab`/`Shift+Tab`切换；复合组件（如列表框、单选组）作为单个制表位，内部元素用箭头键导航。
- **覆盖元素焦点管理**：对话框、弹出框等打开时焦点限制在内部，关闭时恢复到打开元素，由`FocusScope`组件实现。
- **焦点环**：为键盘用户提供当前焦点元素的视觉提示，仅在键盘导航时可见，可通过`useFocusRing`钩子或`FocusRing`组件实现；`useFocusVisible`钩子判断用户是否用键盘导航。


### 4. 辅助技术适配
- **依赖基础**：辅助技术（如屏幕阅读器）依赖 ARIA 或原生 HTML 语义信息识别元素。
- **事件处理**：React Aria 处理辅助技术触发的事件（如屏幕阅读器常触发`onClick`而非指针事件），模拟“按下开始/结束”等事件，确保组件接收一致的事件流。


### 5. 支持的浏览器与平台
- 测试覆盖多种设备（鼠标输入、触摸屏、混合设备）和浏览器：
  - Chrome（macOS、Windows、Android）
  - Firefox（macOS、Windows）
  - Safari（macOS、iOS、iPadOS）
  - Edge（Windows）


### 6. 核心交互钩子与组件
- **交互钩子**：包括`useFocus`（焦点）、`useHover`（悬停）、`useKeyboard`（键盘）、`useLongPress`（长按）、`usePress`（按下）等。
- **相关组件**：`FocusRing`（焦点环）、`FocusScope`（焦点范围）等。

---

# Interactions 交互

High quality interactions that work across a wide variety of devices are essential to a great user experience. React Aria implements hooks for cross-device interactions that ensure consistent behavior across browsers and platforms.
高质量的交互能够在各种设备上正常运行，这对于出色的用户体验至关重要。React Aria 实现了用于跨设备交互的钩子函数，确保在不同浏览器和平台上行为一致。

## Introduction
简介

---

Building high quality interactions that work across a wide variety of devices and platforms is very difficult today. The web runs across an extremely wide variety of devices and platforms: desktop, mobile, TVs, cars, even refrigerators. This extends to interaction models too: the web supports mouse, touch, keyboard, gamepads, screen readers, and more.
如今，构建能够在各种设备和平台上良好运行的高质量交互非常困难。网络覆盖了极为广泛的设备和平台：桌面电脑、移动设备、电视、汽车，甚至冰箱。这也延伸到了交互模式：网络支持鼠标、触摸、键盘、游戏手柄、屏幕阅读器等多种方式。

Unfortunately, the web platform doesn't have any high level abstractions across these interaction models. There's no high level gesture events, or even the concept of a “press” event that works across all of these interaction models. We just have low level events like mouse, touch, keyboard, and focus events, and it's up to developers to put them together properly. This leads to many web apps not working consistently across various types of devices and interaction models.
不幸的是，网络平台在这些交互模型中没有任何高层次的抽象。没有高层次的手势事件，甚至没有适用于所有这些交互模型的 “按下” 事件概念。我们只有诸如鼠标、触摸、键盘和焦点之类的低层次事件，如何恰当地组合这些事件则取决于开发者。这导致许多网络应用在各种类型的设备和交互模型上无法始终如一地正常运行。

React Aria includes a collection of [Hooks](https://react.dev/reference/react/hooks) that provide higher level abstractions over the low level events exposed by the web platform, and helps normalize the behavior across browsers and devices. This includes support for high level events like “press”, “hover”, and “focus”. Some of these seem simple at first, but you’d be surprised how complicated it is to handle these events in a cross-browser, cross-device way. There are many tiny behavioral differences that have a big impact on how well components work across all of these platforms.
React Aria 包含一组 [Hook](https://react.dev/reference/react/hooks)，这些 Hook 对 Web 平台暴露的底层事件提供了更高级别的抽象，并有助于规范跨浏览器和设备的行为。这包括对 “按下”、“悬停” 和 “聚焦” 等高阶事件的支持。其中一些乍看之下似乎很简单，但你可能会惊讶于以跨浏览器、跨设备的方式处理这些事件有多复杂。存在许多微小的行为差异，这些差异对组件在所有这些平台上的运行效果有着重大影响。

## Pointer events

---

The web was originally designed for mouse events. Later, when touch devices were introduced, browsers added support for touch events. However, since existing web apps had not been designed with touch in mind, browsers needed to emulate mouse events on touch devices to ensure some compatibility with them.
网络最初是为鼠标事件而设计的。后来，随着触摸设备的出现，浏览器增加了对触摸事件的支持。然而，由于现有的网络应用程序在设计时并未考虑到触摸操作，浏览器需要在触摸设备上模拟鼠标事件，以确保与之具有一定的兼容性。

Touch input is quite different than mouse input though. For example, it is possible to hover over an element with a mouse, but not with touch. But since existing apps designed for mouse input have hover only effects, browsers emulate hover events on touch devices. Touching an element once fires `onMouseEnter` and touching it again fires `onMouseLeave`. On one hand this is good because it means the functionality is accessible to users on touch devices, but on the other hand, it's not a great experience.
不过，触摸输入与鼠标输入有很大不同。例如，可以用鼠标悬停在某个元素上，但触摸操作却无法做到。但由于现有的为鼠标输入设计的应用程序仅具有悬停效果，浏览器会在触摸设备上模拟悬停事件。触摸一次元素会触发`onMouseEnter`，再次触摸则会触发`onMouseLeave`。一方面，这是好事，因为这意味着触摸设备上的用户也能使用该功能；但另一方面，这并不是一种很棒的体验。

Mobile browsers also support many types of gestures by default, e.g. double tap to zoom, scrolling, and long press to select text. Because of this, touch devices often delay firing events like `onClick` in order to first determine if the user intends to zoom or scroll the page. In the context of a highly interactive web application, this leads to a sub-optimal user experience.
移动浏览器默认也支持多种手势操作，例如双击缩放、滚动以及长按选择文本。正因为如此，触摸设备通常会延迟触发诸如`onClick`之类的事件，以便首先判断用户是否想要缩放或滚动页面。在高度交互式的Web应用程序环境中，这会导致用户体验欠佳。

The new [pointer events](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events) spec helps simplify handling events across mouse, touch, and stylus/pen interaction models. React Aria uses pointer events where available with fallbacks on older devices to mouse and touch events. It normalizes many cross-browser behavioral differences, and ensures that all browsers and platforms behave consistently. In addition, React Aria supports additional interaction models like keyboard and focus events to ensure that all users can interact with your app, not just mouse and touchscreen users.
新的[指针事件](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)规范有助于简化跨鼠标、触摸和手写笔/钢笔交互模型的事件处理。React Aria 在支持指针事件的设备上使用指针事件，在旧设备上则回退到鼠标和触摸事件。它规范了许多跨浏览器的行为差异，确保所有浏览器和平台的表现一致。此外，React Aria 还支持键盘和焦点事件等其他交互模型，以确保所有用户都能与你的应用程序进行交互，而不仅仅是鼠标和触摸屏用户。

CSS pseudo classes like `:hover` and `:active` are also problematic in many cases, especially on hybrid devices that support both mouse and touch. They also emulate mouse behavior on touch devices, e.g. showing hover effects when you touch elements. It's best to use JavaScript events that apply CSS styles rather than these pseudo classes for the best cross-device experience.
像`:hover`和`:active`这样的CSS伪类在很多情况下也存在问题，尤其是在同时支持鼠标和触摸操作的混合设备上。它们还会在触摸设备上模拟鼠标行为，例如当你触摸元素时显示悬停效果。为了获得最佳的跨设备体验，最好使用JavaScript事件来应用CSS样式，而不是这些伪类。

See [usePress](https://react-spectrum.adobe.com/react-aria/usePress.html) and [useHover](https://react-spectrum.adobe.com/react-aria/useHover.html) for more details about React Aria's hooks for cross device interactions.
有关React Aria用于跨设备交互的钩子的更多详细信息，请参阅[usePress](https://react-spectrum.adobe.com/react-aria/usePress.html)和[useHover](https://react-spectrum.adobe.com/react-aria/useHover.html)。

## Keyboard and focus behavior

---

Keyboard and focus support is important to allow users to navigate your app with a keyboard. This is imperative for users who are unable to physically use a mouse or touch screen, but also nice for power users who may find it faster to navigate parts of your app without lifting their hands from the keyboard.
键盘和焦点支持对于用户使用键盘浏览应用程序至关重要。这对于无法实际使用鼠标或触摸屏的用户来说必不可少，而对于那些可能觉得无需将手从键盘上移开就能更快浏览应用程序部分内容的高级用户来说，也是一项不错的功能。

At a high level, keyboard navigation is broken up into **tab stops**, which may be navigated by pressing the Tab key to move to the next tab stop, and Shift + Tab to move to the previous tab stop. A tab stop may be an atomic component like a text field or button, or a composite component like a listbox, radio group, grid, or toolbar. Composite components behave as a single tab stop. Elements within a composite component are typically navigated with the arrow keys, while the Tab key continues to navigate to the next/previous tab stop. React Aria implements many of these composite components and handles all of the keyboard navigation behavior for elements inside them.
从高层次上讲，键盘导航被分解为**制表位**，可以通过按Tab键移动到下一个制表位，按Shift + Tab组合键移动到上一个制表位。制表位可以是像文本字段或按钮这样的原子组件，也可以是像列表框、单选按钮组、网格或工具栏这样的复合组件。复合组件表现为单个制表位。复合组件内的元素通常使用箭头键进行导航，而Tab键则继续导航到下一个/上一个制表位。React Aria实现了许多这样的复合组件，并处理它们内部元素的所有键盘导航行为。

Overlay elements like dialogs, popovers, and menus have additional focus behavior to ensure that focus stays within them while they are open, and focus is restored back to the element that opened them when they are closed. In React Aria this is implemented by the [FocusScope](https://react-spectrum.adobe.com/react-aria/FocusScope.html) component.
像对话框、弹出框和菜单这样的覆盖元素具有额外的焦点行为，以确保在它们打开时焦点保持在其内部，并且在关闭时焦点恢复到打开它们的元素。在 React Aria 中，这是由 [FocusScope](https://react-spectrum.adobe.com/react-aria/FocusScope.html) 组件实现的。

Another important feature for keyboard users is a **focus ring**. This is a visual affordance for the currently focused element, which allows a keyboard user to know which element they are currently on. It should only be visible when navigating with a keyboard, so as not to distract mouse and touchscreen users. This can be implemented using the [useFocusRing](https://react-spectrum.adobe.com/react-aria/useFocusRing.html) hook or the [FocusRing](https://react-spectrum.adobe.com/react-aria/FocusRing.html) component. The [useFocusVisible](https://react-spectrum.adobe.com/react-aria/useFocusVisible.html) hook can also be used to determine whether the user is currently navigating with a keyboard or not.
对于使用键盘的用户来说，另一个重要功能是**焦点环**。这是当前获得焦点元素的视觉提示，可让使用键盘的用户知道他们当前所在的元素。它应该仅在使用键盘导航时可见，以免干扰使用鼠标和触摸屏的用户。这可以通过使用[useFocusRing](https://react-spectrum.adobe.com/react-aria/useFocusRing.html)钩子函数或[FocusRing](https://react-spectrum.adobe.com/react-aria/FocusRing.html)组件来实现。[useFocusVisible](https://react-spectrum.adobe.com/react-aria/useFocusVisible.html)钩子函数也可用于判断用户当前是否正在使用键盘进行导航。

## Assistive technology
辅助技术

---

An assistive technology, such as a screen reader, relies on semantic information exposed to the browser through ARIA or native HTML element semantics. This allows it to know what an element represents and relay this information to the user. As a screen reader or other assistive technology navigates around an application, it may fire various JavaScript events. Since screen readers are navigating the app in a completely different way than traditional mouse, touch, or keyboard behavior, these events are emulated by a screen reader. It may focus elements, fire click events to activate them, or otherwise emulate mouse and keyboard events.
辅助技术（如屏幕阅读器）依赖于通过ARIA或原生HTML元素语义暴露给浏览器的语义信息。这使其能够了解元素代表什么，并将此信息传达给用户。当屏幕阅读器或其他辅助技术在应用程序中导航时，可能会触发各种JavaScript事件。由于屏幕阅读器与传统鼠标、触摸或键盘行为的应用程序导航方式完全不同，这些事件由屏幕阅读器模拟。它可能会聚焦元素、触发点击事件以激活它们，或以其他方式模拟鼠标和键盘事件。

React Aria is careful to handle events fired by assistive technology, and normalizes this behavior as needed so it is consistent with other interactions. For example, while we use pointer events for mouse and touch interactions, many screen readers do not fire these events at all, instead only firing `onClick`. React Aria handles this and simulates press start and press end events so that components receive a consistent stream of events regardless of the interaction model.
React Aria 会谨慎处理辅助技术触发的事件，并根据需要对这种行为进行规范化，以使其与其他交互保持一致。例如，虽然我们对鼠标和触摸交互使用指针事件，但许多屏幕阅读器根本不会触发这些事件，而是只触发 `onClick`。React Aria 会处理这种情况，并模拟按下开始和按下结束事件，以便组件无论使用何种交互模式，都能接收到一致的事件流。

## Supported browsers and platforms
支持的浏览器和平台

---

All React Aria components are tested across a wide variety of browsers and devices. We test across devices with mouse input, touchscreens, and also hybrid devices.所有React Aria组件都在各种浏览器和设备上进行了测试。我们在支持鼠标输入、触摸屏的设备以及混合设备上进行测试。

- Chrome on macOS, Windows, and Android适用于macOS、Windows和安卓系统的Chrome浏览器
- Firefox on macOS and Windows macOS和Windows系统上的Firefox浏览器
- Safari on macOS, iOS, and iPadOS macOS、iOS和iPadOS上的Safari
- Edge on Windows Windows 系统上的 Microsoft Edge 浏览器