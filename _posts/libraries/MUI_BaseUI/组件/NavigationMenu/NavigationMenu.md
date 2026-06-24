# 使用介绍

`NavigationMenu` 是 **Base UI**（由 MUI 团队开发的无样式、高可访问性 React 组件库）中专门用于构建**复杂网站级导航栏（如 Mega Menu、多级下拉导航）** 的核心组件 。

与普通的 `DropdownMenu`（下拉菜单）不同，`NavigationMenu` 专为**路由跳转**和**展示大量富文本/网格内容**而设计，并内置了现代前端常见的“共享视口（Shared Viewport）”平滑过渡动画。

以下是对 Base UI `NavigationMenu` 的详细深度解析：

---

## 一、 核心组件结构（Anatomy）

Base UI 采用高度可组合的组件设计模式，`NavigationMenu` 由多个语义化的子组件拼装而成 ：

1.  **`NavigationMenu.Root`**：最外层的容器，负责管理全局状态（当前激活的菜单项）。默认渲染为 `<nav>` 标签。
2.  **`NavigationMenu.List`**：导航项的列表容器，默认渲染为 `<ul>`。
3.  **`NavigationMenu.Item`**：单个导航项，默认渲染为 `<li>`。
4.  **`NavigationMenu.Trigger`**：触发器（通常是顶部导航栏的按钮），用于悬停或点击时展开对应的内容。
5.  **`NavigationMenu.Content`**：与 Trigger 关联的隐藏内容区（包含链接、描述、图片等）。
6.  **`NavigationMenu.Viewport`**：***核心魔法所在**。这是一个动态调整大小的浮动面板，当用户在不同 Trigger 之间切换时，Viewport 会平滑地滑动和缩放以展示新的 `Content`*。
7.  **`NavigationMenu.Portal` & `Positioner`**：用于*将弹出层渲染到 DOM 树的顶层，避免被父容器的 `overflow: hidden` 裁剪，并处理精准的浮动定位*。

---

## 二、 核心特性

### 1. 完全无样式 (Unstyled)

Base UI 不提供任何默认 CSS。你可以*使用 Tailwind CSS、CSS Modules 或 styled-components 自由定制外观，非常适合构建企业级的 Design System* 。

### 2. 极致的可访问性 (Accessibility / a11y)

这是 Base UI 最大的卖点之一。它*自动处理复杂的 WAI-ARIA 规范和键盘导航 ：*

- **键盘交互**：支持使用 `Tab` 键在链接间跳转，`左右方向键` 切换顶级 Trigger，`上下方向键` 在展开的面板内导航，`Esc` 键关闭菜单，`Enter/Space` 激活链接 。
- **屏幕阅读器支持**：自动管理 `aria-controls`、`aria-expanded` 和焦点陷阱（Focus Trap），确保视障用户能完美使用。

### 3. 智能视口动画 (Shared Viewport)

在传统的下拉菜单中，从“产品”移动到“解决方案”时，菜单会先关闭再打开，**产生闪烁感**。`NavigationMenu` 的 `Viewport` **会保持打开状态，并在后台平滑地过渡宽度和高度，提供类似苹果官网或 Stripe 官网的丝滑体验**。

### 4. 支持嵌套与大型菜单

- **多级嵌套**：你可以在 `Content` 内部再次嵌套 `NavigationMenu.Root` 来实现多级子菜单 。
- **自适应高度**：当菜单内容过长时，可以通过内置的 CSS 变量（如 `--available-height`）轻松实现内容压缩或内部滚动，防止超出屏幕视口 。

---

## 三、 代码示例（结合 Tailwind CSS）

以下是一个典型的现代网站导航栏实现示例：

```jsx
import { NavigationMenu } from "@base-ui/react/navigation-menu";

export default function Navbar() {
  return (
    <NavigationMenu.Root className="relative z-10 flex w-full justify-center">
      <NavigationMenu.List className="flex gap-2 border border-gray-200 bg-white p-1 rounded-lg shadow-sm">
        {/* 菜单项 1：产品 */}
        <NavigationMenu.Item>
          <NavigationMenu.Trigger className="px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-100 rounded-md transition-colors">
            产品
          </NavigationMenu.Trigger>
          <NavigationMenu.Content className="grid grid-cols-3 gap-6 p-8 bg-white shadow-xl rounded-lg w-[800px]">
            <div>
              <h3 className="font-bold text-gray-900 mb-3">数据分析</h3>
              <ul className="space-y-2">
                <li>
                  <a
                    href="/analytics"
                    className="text-blue-600 hover:underline"
                  >
                    实时大盘
                  </a>
                </li>
                <li>
                  <a href="/reports" className="text-blue-600 hover:underline">
                    自定义报表
                  </a>
                </li>
              </ul>
            </div>
            <div>
              <h3 className="font-bold text-gray-900 mb-3">自动化</h3>
              <ul className="space-y-2">
                <li>
                  <a
                    href="/workflows"
                    className="text-blue-600 hover:underline"
                  >
                    工作流引擎
                  </a>
                </li>
              </ul>
            </div>
            <div className="bg-gray-50 p-4 rounded-lg flex items-center justify-center">
              <p className="text-sm text-gray-500">放置宣传图片或二维码</p>
            </div>
          </NavigationMenu.Content>
        </NavigationMenu.Item>

        {/* 菜单项 2：关于我们 */}
        <NavigationMenu.Item>
          <NavigationMenu.Trigger className="px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-100 rounded-md transition-colors">
            关于
          </NavigationMenu.Trigger>
          <NavigationMenu.Content className="p-6 bg-white shadow-xl rounded-lg w-[300px]">
            <p className="text-gray-600">了解我们的团队和愿景。</p>
          </NavigationMenu.Content>
        </NavigationMenu.Item>
      </NavigationMenu.List>

      {/* 浮动层与视口 */}
      <NavigationMenu.Portal>
        <NavigationMenu.Positioner sideOffset={10}>
          <NavigationMenu.Popup>
            {/* Viewport 会自动根据当前激活的 Content 调整宽高 */}
            <NavigationMenu.Viewport className="origin-top transition-[width,height] duration-200 ease-in-out bg-white rounded-lg shadow-lg" />
          </NavigationMenu.Popup>
        </NavigationMenu.Positioner>
      </NavigationMenu.Portal>
    </NavigationMenu.Root>
  );
}
```

---

## 四、 NavigationMenu vs DropdownMenu / Menu

很多开发者会混淆它们，以下是核心区分标准：

| 维度         | `NavigationMenu` (导航菜单)                     | `DropdownMenu` / `Menu` (下拉菜单)                     |
| :----------- | :---------------------------------------------- | :----------------------------------------------------- |
| **核心用途** | **网站全局路由**（如顶部导航栏、Mega Menu）     | **应用内操作**（如用户头像菜单、表格行操作、右键菜单） |
| **触发方式** | 桌面端主要依赖 **Hover (悬停)**，移动端依赖点击 | 主要依赖 **Click (点击)**                              |
| **DOM 结构** | 语义化的 `<nav>`、`<ul>`、`<li>` 和 `<a>` 标签  | 通常由 `<button>` 和 `<div>` 组成                      |
| **动画表现** | 拥有共享的 `Viewport`，面板切换时平滑滑动/缩放  | 通常是简单的淡入淡出或弹出层 (Popover)                 |
| **SEO 影响** | 内部包含大量链接，对搜索引擎爬虫非常友好        | 内部多为 Action，通常不被爬虫重视                      |

---

## 五、 最佳实践与注意事项（⚠️ 避坑指南）

1.  **路由库的集成**：
    Base UI 是无路由感知的。在 `NavigationMenu.Content` 内部，请务必使用你所用框架的 Link 组件（如 Next.js 的 `<Link>` 或 React Router 的 `Link`），而不是原生的 `<a>` 标签，以保证客户端路由（Client-side Routing）的流畅性和 SEO 。
2.  **移动端适配 (Mobile Responsiveness)**：
    `NavigationMenu` 的交互逻辑（Hover 展开、复杂的多列视口动画）是**专为桌面端设计的**。在移动端（触摸屏没有 Hover 状态），最佳实践是通过 CSS 媒体查询隐藏 `NavigationMenu`，并替换为 `Sheet`（侧边抽屉）或 `Dialog` 组件来实现“汉堡包菜单”。
3.  **动画性能**：
    由于 `Viewport` 会频繁调整宽度和高度，建议对其应用 CSS 的 `will-change: width, height;` 或确保其使用硬件加速，以避免在低端设备上出现动画掉帧。
4.  **与 Radix UI 的关系**：
    如果你之前使用过 **Radix UI**，你会发现 Base UI 的 `NavigationMenu` 在 API 设计和行为逻辑上与 Radix 非常相似（Base UI 吸收了业界最佳实践），但 Base UI 属于 MUI 生态，在代码体积和与其他 Base UI 组件的协同上具有一定优势。

# 源码介绍

## 概述

Navigation Menu 是 Base UI 提供的一个**无头（headless）、未样式化**的导航菜单组件，用于构建网站的主导航系统。它支持多级嵌套菜单、悬停展开、键盘导航等特性，完全可访问（accessible）。

**核心特点：**

- 完全无样式，可自定义任意 CSS 样式
- 支持受控和非受控模式
- 内置悬停延迟和智能关闭逻辑
- 支持键盘导航和屏幕阅读器
- 支持嵌套子菜单（多层级）
- 自动尺寸调整和动画过渡
- 基于 Floating UI 的精确定位

## 组件结构（Anatomy）

Navigation Menu 由 **13 个组件部件**组成，采用组合模式：

```tsx
import { NavigationMenu } from "@base-ui/react/navigation-menu";

<NavigationMenu.Root>
  <NavigationMenu.List>
    <NavigationMenu.Item>
      <NavigationMenu.Trigger>
        <NavigationMenu.Icon />
      </NavigationMenu.Trigger>
      <NavigationMenu.Content>
        <NavigationMenu.Link />
      </NavigationMenu.Content>
    </NavigationMenu.Item>
  </NavigationMenu.List>

  <NavigationMenu.Portal>
    <NavigationMenu.Backdrop />
    <NavigationMenu.Positioner>
      <NavigationMenu.Popup>
        <NavigationMenu.Arrow />
        <NavigationMenu.Viewport />
      </NavigationMenu.Popup>
    </NavigationMenu.Positioner>
  </NavigationMenu.Portal>
</NavigationMenu.Root>;
```

## 各组件详解

### 1. Root（根组件）

**文件：** [NavigationMenuRoot.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx)

**作用：** *包裹整个导航菜单，提供状态管理和上下文*

**渲染元素：**

- 顶层：`<nav>` 元素
- 嵌套时：`<div>` 元素

**关键 Props：**

```tsx
interface NavigationMenuRootProps {
  // 受控值：当前打开的菜单项
  value?: any | null;
  // 非受控默认值
  defaultValue?: any | null;
  // 值变化回调
  onValueChange?: (value, eventDetails) => void;
  // 打开延迟（毫秒）
  delay?: number; // 默认 50ms
  // 关闭延迟（毫秒）
  closeDelay?: number; // 默认 50ms
  // 方向：水平或垂直
  orientation?: "horizontal" | "vertical"; // 默认 'horizontal'
  // 命令式操作
  actionsRef?: React.RefObject<NavigationMenuRootActions>;
  // 动画完成回调
  onOpenChangeComplete?: (open: boolean) => void;
}
```

**状态管理：**

- 使用 `useControlled` hook 支持受控/非受控模式
- 通过 `NavigationMenuRootContext` 共享状态给子组件
- 跟踪当前激活的菜单项 `value`
- 管理打开/关闭状态、过渡状态、激活方向等

### 2. List（列表）

**文件：** [NavigationMenuList.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx)

**作用：** 包含菜单项的容器

**渲染元素：** `<ul>`

**特性：**

- 使用 `CompositeRoot` 实现键盘导航（方向键）
- 处理外部点击关闭（`useDismiss`）
- 支持悬停浮动交互

### 3. Item（菜单项）

**文件：** [NavigationMenuItem.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/item/NavigationMenuItem.tsx)

**作用：** 单个导航菜单项

**渲染元素：** `<li>`

**Props：**

```tsx
interface NavigationMenuItemProps {
  // 唯一标识值，用于受控模式
  value?: any; // 未提供时自动生成
}
```

**特性：**

- 通过 `NavigationMenuItemContext` 向子组件提供 `value`
- 作为 Trigger 和 Content 的容器

### 4. Trigger（触发器）

**文件：** [NavigationMenuTrigger.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx)

**作用：** 打开/关闭菜单的按钮

**渲染元素：** `<button>`

**关键特性：**

- **悬停展开：** 使用 `useHoverReferenceInteraction` 和 `safePolygon` 算法
- **点击切换：** 使用 `useClick` 交互
- **智能延迟：**
  - 打开延迟 50ms（防止意外触发）
  - 关闭延迟 50ms（允许鼠标移动到菜单）
- **自动尺寸调整：**
  - 监听内容变化（MutationObserver）
  - 监听窗口大小变化（ResizeObserver）
  - 动态设置 CSS 变量 `--popup-width` 和 `--popup-height`
- **焦点管理：** 使用 FocusGuard 确保焦点循环正确
- **键盘支持：**
  - 水平菜单：`ArrowDown` 打开
  - 垂直菜单：`ArrowRight`（LTR）或 `ArrowLeft`（RTL）打开

**状态：**

```tsx
interface NavigationMenuTriggerState {
  open: boolean; // 当前项是否激活
}
```

### 5. Icon（图标）

**文件：** [NavigationMenuIcon.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/icon/NavigationMenuIcon.tsx)

**作用：** 指示触发器会打开菜单的图标

**渲染元素：** `<span>`

**默认内容：** `▼`（可自定义）

**状态：**

```tsx
interface NavigationMenuIconState {
  open: boolean; // 关联的菜单是否打开
}
```

### 6. Content（内容）

**文件：** [NavigationMenuContent.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/content/NavigationMenuContent.tsx)

**作用：** 菜单项的内容容器，激活时移入 Popup

**渲染元素：** `<div>`

**关键特性：**

- **Portal 渲染：** 通过 `ReactDOM.createPortal` 渲染到 Viewport 中
- **过渡状态：** 跟踪 `transitionStatus`（starting/open/closed）
- **激活方向：** 跟踪 `activationDirection`（left/right/up/down）用于动画
- **keepMounted：** 保持挂载以支持 SSR 和爬虫
- **焦点管理：** 跟踪内部焦点状态，支持 inert 属性

**Props：**

```tsx
interface NavigationMenuContentProps {
  // 关闭时保持挂载（用于 SSR）
  keepMounted?: boolean; // 默认 false
}
```

**状态：**

```tsx
interface NavigationMenuContentState {
  open: boolean;
  transitionStatus: TransitionStatus;
  activationDirection: "left" | "right" | "up" | "down" | null;
}
```

### 7. Link（链接）

**文件：** [NavigationMenuLink.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/link/NavigationMenuLink.tsx)

**作用：** 导航菜单中的可点击链接

**渲染元素：** `<a>`

**Props：**

```tsx
interface NavigationMenuLinkProps {
  // 是否为当前活动页面
  active?: boolean; // 默认 false
  // 点击后是否关闭菜单
  closeOnClick?: boolean; // 默认 false
}
```

**特性：**

- 设置 `aria-current="page"` 当 `active=true`
- 支持 `render` prop 自定义渲染（如 Next.js Link）
- 焦点离开时检查是否需要关闭菜单

**自定义链接示例：**

```tsx
import NextLink from "next/link";

<NavigationMenu.Link
  render={<NextLink href="/path" />}
  active={isActive}
  closeOnClick
>
  链接文本
</NavigationMenu.Link>;
```

### 8. Portal（传送门）

**文件：** [NavigationMenuPortal.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/portal/NavigationMenuPortal.tsx)

**作用：** 将 Popup 渲染到 DOM 的其他位置（默认 `<body>`）

**渲染元素：** `<div>`

**Props：**

```tsx
interface NavigationMenuPortalProps {
  // 关闭时保持挂载
  keepMounted?: boolean;
  // 自定义容器
  container?: HTMLElement | null;
}
```

### 9. Positioner（定位器）

**文件：** [NavigationMenuPositioner.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/positioner/NavigationMenuPositioner.tsx)

**作用：** 基于 Floating UI 精确定位 Popup

**渲染元素：** `<div>`

**关键 Props：**

```tsx
interface NavigationMenuPositionerProps {
  // 锚点元素
  anchor?: Element | null;
  // 定位方式
  positionMethod?: "absolute" | "fixed"; // 默认 'absolute'
  // 放置位置
  side?: "top" | "right" | "bottom" | "left"; // 默认 'bottom'
  // 对齐方式
  align?: "start" | "center" | "end"; // 默认 'center'
  // 偏移量
  sideOffset?: number; // 默认 0
  alignOffset?: number; // 默认 0
  // 碰撞检测
  collisionBoundary?: "clipping-ancestors" | Element[];
  collisionPadding?: number | object;
  // 箭头内边距
  arrowPadding?: number; // 默认 5
  // 粘性定位
  sticky?: boolean; // 默认 false
}
```

**CSS 变量：**

- `--available-width` / `--available-height`：可用空间
- `--anchor-width` / `--anchor-height`：锚点尺寸
- `--transform-origin`：变换原点
- `--positioner-width` / `--positioner-height`：定位器尺寸

### 10. Popup（弹出层）

**文件：** [NavigationMenuPopup.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/popup/NavigationMenuPopup.tsx)

**作用：** 菜单内容的容器

**渲染元素：** `<nav>`

**状态：**

```tsx
interface NavigationMenuPopupState {
  open: boolean;
  transitionStatus: TransitionStatus;
  side: Side; // 放置位置
  align: Align; // 对齐方式
  anchorHidden: boolean; // 锚点是否隐藏
}
```

**CSS 变量：**

- `--popup-width` / `--popup-height`：Popup 尺寸（用于动画）

### 11. Viewport（视口）

**文件：** [NavigationMenuViewport.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/viewport/NavigationMenuViewport.tsx)

**作用：** 内容的裁剪容器，控制可见区域

**渲染元素：** `<div>`

**特性：**

- 包含 FocusGuard 确保焦点循环
- 管理 inert 状态防止焦点循环
- 作为 Content 的 Portal 目标

### 12. Backdrop（背景遮罩）

**文件：** [NavigationMenuBackdrop.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/backdrop/NavigationMenuBackdrop.tsx)

**作用：** 菜单打开时的背景遮罩

**渲染元素：** `<div>`

**状态：**

```tsx
interface NavigationMenuBackdropState {
  open: boolean;
  transitionStatus: TransitionStatus;
}
```

**样式：** 默认 `user-select: none`

### 13. Arrow（箭头）

**文件：** [NavigationMenuArrow.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/arrow/NavigationMenuArrow.tsx)

**作用：** 指向触发器的箭头指示器

**渲染元素：** `<div>`

**状态：**

```tsx
interface NavigationMenuArrowState {
  open: boolean;
  side: Side;
  align: Align;
  uncentered: boolean; // 箭头是否无法居中
}
```

## 核心机制

### 1. 状态管理

**Context 层次：**

- `NavigationMenuRootContext`：全局状态（open、value、transitionStatus 等）
- `NavigationMenuItemContext`：菜单项级别（value）
- `NavigationMenuTreeContext`：Floating UI 节点 ID
- `NavigationMenuPositionerContext`：定位信息
- `NavigationMenuPortalContext`：Portal 配置
- `NavigationMenuDismissContext`：关闭交互

**状态流：**

```
Root (value, open)
  ↓
Item (value)
  ↓
Trigger (isActiveItem = open && value === itemValue)
  ↓
Content (open = popupMounted && value === itemValue)
```

### 2. 悬停交互

**safePolygon 算法：**

- 创建从触发器到 Popup 的安全多边形区域
- 鼠标在多边形内移动不会关闭菜单
- 防止鼠标斜向移动时意外关闭

**延迟逻辑：**

```tsx
// 打开延迟
delay = 50ms // 防止快速划过时触发

// 关闭延迟
closeDelay = 50ms // 允许鼠标移动到菜单

// 已打开时的"粘性"
stickIfOpen = true // 500ms 内的点击不会关闭
```

### 3. 尺寸动画

**自动尺寸调整流程：**

1. Trigger 激活时测量 Content 尺寸
2. 设置 CSS 变量 `--popup-width` 和 `--popup-height`
3. 使用 MutationObserver 监听内容变化
4. 使用 ResizeObserver 监听窗口变化
5. 动画完成后重置为 `auto`

**关键代码：**

```tsx
// 设置固定尺寸（用于动画）
popupElement.style.setProperty("--popup-width", `${width}px`);
popupElement.style.setProperty("--popup-height", `${height}px`);

// 动画完成后重置
scheduleAutoSizeReset(); // 等待动画完成后设置为 'auto'
```

### 4. 键盘导航

**List 级别（CompositeRoot）：**

- `ArrowLeft` / `ArrowRight`（水平菜单）：在 Trigger 间移动
- `ArrowUp` / `ArrowDown`（垂直菜单）：在 Trigger 间移动
- `Home` / `End`：跳到第一个/最后一个

**Trigger 级别：**

- `ArrowDown`（水平菜单）：打开菜单
- `ArrowRight`（垂直菜单，LTR）：打开菜单
- `ArrowLeft`（垂直菜单，RTL）：打开菜单
- `Escape`：关闭菜单
- `Tab`：在内容中移动焦点

**Content 级别：**

- `Tab`：在链接间移动
- `Escape`：关闭菜单并返回 Trigger

### 5. 嵌套子菜单

**两种方式：**

**方式 1：Portal 嵌套（独立弹出）**

```tsx
<NavigationMenu.Content>
  <NavigationMenu.Root orientation="vertical">
    <NavigationMenu.List>
      <NavigationMenu.Item>
        <NavigationMenu.Trigger>子菜单</NavigationMenu.Trigger>
        <NavigationMenu.Content>子内容</NavigationMenu.Content>
      </NavigationMenu.Item>
    </NavigationMenu.List>
    <NavigationMenu.Portal>
      <NavigationMenu.Positioner side="right">
        <NavigationMenu.Popup>
          <NavigationMenu.Viewport />
        </NavigationMenu.Popup>
      </NavigationMenu.Positioner>
    </NavigationMenu.Portal>
  </NavigationMenu.Root>
</NavigationMenu.Content>
```

**方式 2：内联嵌套（同一面板）**

```tsx
<NavigationMenu.Content>
  <NavigationMenu.Root defaultValue="developers">
    <NavigationMenu.List>{/* 子菜单项 */}</NavigationMenu.List>
    <NavigationMenu.Viewport />
  </NavigationMenu.Root>
</NavigationMenu.Content>
```

### 6. 焦点管理

**FocusGuard 系统：**

- `beforeOutsideRef` / `afterOutsideRef`：Trigger 外部
- `beforeInsideRef` / `afterInsideRef`：Content 内部

**焦点循环：**

```
Trigger → beforeOutside → beforeInside → Content 内容 → afterInside → afterOutside → Trigger
```

**返回焦点：**

- 关闭时焦点返回 Trigger（除非是悬停、外部点击、焦点移出触发）

### 7. 关闭逻辑

**关闭原因（REASONS）：**

- `triggerPress`：点击 Trigger
- `triggerHover`：悬停 Trigger
- `outsidePress`：外部点击
- `listNavigation`：列表导航
- `focusOut`：焦点移出
- `escapeKey`：Escape 键
- `linkPress`：链接点击
- `none`：无原因

**关闭条件检查：**

```tsx
// isOutsideMenuEvent 检查
!contains(popupElement, relatedTarget) &&
  !contains(rootRef.current, relatedTarget) &&
  !nodeChildrenContains; // 嵌套子菜单
```

## 完整示例

### 基础示例

```tsx
import * as React from "react";
import { NavigationMenu } from "@base-ui/react/navigation-menu";

export default function App() {
  return (
    <NavigationMenu.Root>
      <NavigationMenu.List>
        <NavigationMenu.Item>
          <NavigationMenu.Trigger>
            产品
            <NavigationMenu.Icon>▼</NavigationMenu.Icon>
          </NavigationMenu.Trigger>
          <NavigationMenu.Content>
            <ul>
              <li>
                <NavigationMenu.Link href="/features" closeOnClick>
                  功能特性
                </NavigationMenu.Link>
              </li>
              <li>
                <NavigationMenu.Link href="/pricing" closeOnClick>
                  价格
                </NavigationMenu.Link>
              </li>
            </ul>
          </NavigationMenu.Content>
        </NavigationMenu.Item>

        <NavigationMenu.Item>
          <NavigationMenu.Trigger>
            资源
            <NavigationMenu.Icon>▼</NavigationMenu.Icon>
          </NavigationMenu.Trigger>
          <NavigationMenu.Content>
            <ul>
              <li>
                <NavigationMenu.Link href="/docs" closeOnClick>
                  文档
                </NavigationMenu.Link>
              </li>
              <li>
                <NavigationMenu.Link href="/blog" closeOnClick>
                  博客
                </NavigationMenu.Link>
              </li>
            </ul>
          </NavigationMenu.Content>
        </NavigationMenu.Item>
      </NavigationMenu.List>

      <NavigationMenu.Portal>
        <NavigationMenu.Positioner sideOffset={8}>
          <NavigationMenu.Popup>
            <NavigationMenu.Arrow />
            <NavigationMenu.Viewport />
          </NavigationMenu.Popup>
        </NavigationMenu.Positioner>
      </NavigationMenu.Portal>
    </NavigationMenu.Root>
  );
}
```

### 受控模式

```tsx
function ControlledMenu() {
  const [value, setValue] = React.useState(null);

  return (
    <NavigationMenu.Root
      value={value}
      onValueChange={(newValue) => setValue(newValue)}
    >
      {/* ... */}
    </NavigationMenu.Root>
  );
}
```

### 命令式关闭

```tsx
function ImperativeMenu() {
  const actionsRef = React.useRef(null);

  const handleClose = () => {
    actionsRef.current?.unmount();
  };

  return (
    <NavigationMenu.Root actionsRef={actionsRef}>
      {/* ... */}
    </NavigationMenu.Root>
  );
}
```

## CSS 变量

**Popup 相关：**

- `--popup-width`：Popup 宽度
- `--popup-height`：Popup 高度

**Positioner 相关：**

- `--available-width`：可用宽度
- `--available-height`：可用高度
- `--anchor-width`：锚点宽度
- `--anchor-height`：锚点高度
- `--transform-origin`：变换原点
- `--positioner-width`：定位器宽度
- `--positioner-height`：定位器高度

## 数据属性（Data Attributes）

**Trigger：**

- `data-open`：菜单打开时
- `data-closed`：菜单关闭时

**Content：**

- `data-open`：内容显示时
- `data-closed`：内容隐藏时
- `data-transition-status`：`starting` | `open` | `closed`
- `data-activation-direction`：`left` | `right` | `up` | `down`

**Popup：**

- `data-open`：Popup 打开时
- `data-closed`：Popup 关闭时
- `data-transition-status`：过渡状态
- `data-side`：`top` | `right` | `bottom` | `left`
- `data-align`：`start` | `center` | `end`

## 最佳实践

1. **使用 `closeOnClick`：** 对于导航链接，设置 `closeOnClick={true}` 以在点击后关闭菜单

2. **处理大内容：**

   ```css
   .Popup {
     max-height: var(--available-height);
   }
   .Content {
     overflow-y: auto;
   }
   ```

3. **自定义链接：** 使用 `render` prop 集成路由库

   ```tsx
   <NavigationMenu.Link render={<NextLink href="..." />}>
   ```

4. **嵌套菜单方向：**
   - 顶层：`orientation="horizontal"`（水平导航栏）
   - 子菜单：`orientation="vertical"`（垂直下拉）

5. **动画过渡：** 使用 `transitionStatus` 和 `activationDirection` 创建流畅动画

6. **SSR 支持：** 使用 `keepMounted` 保持内容挂载以支持爬虫

## 总结

Base UI Navigation Menu 是一个功能强大、高度可定制的导航菜单组件。它提供了：

- **完整的组件系统**：13 个可组合的部件
- **智能交互**：悬停延迟、safePolygon、自动尺寸调整
- **无障碍支持**：完整的键盘导航和 ARIA 属性
- **灵活定位**：基于 Floating UI 的精确定位和碰撞检测
- **动画友好**：过渡状态、激活方向、CSS 变量
- **嵌套支持**：多层级子菜单

通过无头设计，你可以完全控制样式，同时获得生产级的交互行为。

# 初始化流程

基于源代码分析，我将详细介绍 navigation-menu *组件从实例化到完全初始化的完整流程*。

---
## 初始化流程图

```
用户渲染 <NavigationMenu.Root>
    ↓
Root 组件执行
    ├─ 解构 Props，设定默认值
    ├─ 检测嵌套状态 (nested)
    ├─ 初始化受控/非受控状态 (useControlled)
    ├─ 创建 Refs 和 States
    ├─ 初始化过渡状态 (useTransitionStatus)
    ├─ 创建 NavigationMenuRootContext
    ├─ 创建 FloatingTree（顶层）
    └─ 渲染 <nav> 元素
    ↓
List 组件执行
    ├─ 消费 RootContext
    ├─ 初始化悬停交互 (useHoverFloatingInteraction)
    ├─ 初始化关闭交互 (useDismiss)
    ├─ 创建 CompositeRoot（键盘导航）
    └─ 渲染 <ul> 元素
    ↓
Item 组件执行（每个菜单项）
    ├─ 生成或使用提供的 value
    ├─ 创建 NavigationMenuItemContext
    └─ 渲染 <li> 元素
    ↓
Trigger 组件执行
    ├─ 消费 RootContext 和 ItemContext
    ├─ 计算 isActiveItem
    ├─ 初始化 Timeout 和 AnimationFrame
    ├─ 初始化 Floating UI Context
    ├─ 初始化悬停和点击交互
    ├─ 创建 <button> 元素并绑定事件
    └─ 条件渲染 FocusGuard（激活时）
    ↓
Content 组件执行
    ├─ 计算 open 状态
    ├─ 初始化过渡状态
    └─ 条件渲染（初始为 null）
    ↓
Portal 组件执行
    ├─ 检查 mounted 状态
    └─ 条件渲染（初始为 null）
    ↓
Positioner 组件执行
    ├─ 初始化锚点定位
    └─ 渲染 <div> 元素（hidden/inert）
    ↓
Popup 组件执行
    ├─ 消费 RootContext
    └─ 渲染 <nav> 元素
    ↓
Viewport 组件执行
    ├─ 消费 RootContext
    ├─ 创建 FocusGuard
    └─ 渲染 <div> 元素
    ↓
Effect 执行（DOM 挂载后）
    ├─ Root: 设置关闭时的固定尺寸
    ├─ Trigger: 创建 ResizeObserver
    ├─ Trigger: 监听窗口 resize
    ├─ Trigger: 创建 MutationObserver
    └─ Trigger: 设置 FloatingRootContext（激活时）
    ↓
初始化完成，等待用户交互
```

---

## 一、初始化触发条件
```
用户渲染 <NavigationMenu.Root>
```

### 1.1 组件实例化入口

Navigation Menu 的初始化*由 `<NavigationMenu.Root>` 组件的渲染触发*。

**关键代码位置：** [NavigationMenuRoot.tsx#L63-L311](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L63-L311)

```tsx
// 用户代码触发点
<NavigationMenu.Root>
  <NavigationMenu.List>
    <NavigationMenu.Item>
      <NavigationMenu.Trigger>...</NavigationMenu.Trigger>
      <NavigationMenu.Content>...</NavigationMenu.Content>
    </NavigationMenu.Item>
  </NavigationMenu.List>
  {/* ... */}
</NavigationMenu.Root>
```

### 1.2 初始化前提条件

- React 应用已挂载到 DOM
- `@base-ui/react/navigation-menu` 模块已加载
- 组件树开始渲染 `NavigationMenu.Root`

---

## 二、Root 组件初始化（阶段 1：状态与 Context 建立）

```
Root 组件执行
    ├─ 解构 Props，设定默认值
    ├─ 检测嵌套状态 (nested)
    ├─ 初始化受控/非受控状态 (useControlled)
    ├─ 创建 Refs 和 States
    ├─ 初始化过渡状态 (useTransitionStatus)
    ├─ 创建 NavigationMenuRootContext
    ├─ 创建 FloatingTree（顶层）
    └─ 渲染 <nav> 元素
```

### 2.1 Props 解构与默认值设定

**执行位置：** [NavigationMenuRoot.tsx#L67-L76](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L67-L76)

```tsx
const {
  defaultValue = null,        // 非受控模式的初始值
  value: valueParam,          // 受控模式的当前值
  onValueChange,              // 值变化回调
  actionsRef,                 // 命令式操作引用
  delay = 50,                 // 打开延迟（毫秒）
  closeDelay = 50,            // 关闭延迟（毫秒）
  orientation = 'horizontal', // 方向：水平或垂直
  onOpenChangeComplete,       // 动画完成回调
} = componentProps;
```

**初始状态设定逻辑：**
- `defaultValue` 默认为 `null`，表示菜单初始关闭
- `delay` 和 `closeDelay` 均为 50ms，防止快速划过时意外触发
- `orientation` 默认 `'horizontal'`，适用于顶部导航栏

### 2.2 嵌套检测

**执行位置：** [NavigationMenuRoot.tsx#L78-L79](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L78-L79)

```tsx
const nested = useFloatingParentNodeId() != null;
const parentRootContext = useNavigationMenuRootContext(true);
```

**逻辑说明：**
- 通过 `useFloatingParentNodeId()` 检测是否存在父级 FloatingNode
- 如果存在，说明当前菜单是嵌套的子菜单
- `nested` 标志影响后续 DOM 元素类型（`<nav>` vs `<div>`）和 Context 行为

### 2.3 受控/非受控状态初始化

**执行位置：** [NavigationMenuRoot.tsx#L81-L89](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L81-L89)

```tsx
const [value, setValueUnwrapped] = useControlled({
  controlled: valueParam,
  default: defaultValue,
  name: 'NavigationMenu',
  state: 'value',
});

// 从 value 派生 open 状态
const open = value != null;
```

**`useControlled` hook 的作用：**
- 统一处理受控和非受控模式
- 如果传入 `value` prop，使用受控模式
- 否则使用 `defaultValue` 作为非受控模式的初始值
- 返回当前 `value` 和更新函数 `setValueUnwrapped`

**初始状态：**
- `value = null`（默认）
- `open = false`（菜单关闭）

### 2.4 Ref 和 State 初始化

**执行位置：** [NavigationMenuRoot.tsx#L91-L118](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L91-L118)

```tsx
// DOM 元素引用
const rootRef = React.useRef<HTMLDivElement | null>(null);
const [positionerElement, setPositionerElement] = React.useState<HTMLElement | null>(null);
const [popupElement, setPopupElement] = React.useState<HTMLElement | null>(null);
const [viewportElement, setViewportElement] = React.useState<HTMLElement | null>(null);
const [viewportTargetElement, setViewportTargetElement] = React.useState<HTMLElement | null>(null);

// 状态引用
const [activationDirection, setActivationDirection] = React.useState<...>(null);
const [floatingRootContext, setFloatingRootContext] = React.useState<...>(undefined);
const [viewportInert, setViewportInert] = React.useState(false);

// 焦点管理引用
const prevTriggerElementRef = React.useRef<Element | null | undefined>(null);
const currentContentRef = React.useRef<HTMLDivElement | null>(null);
const beforeInsideRef = React.useRef<HTMLSpanElement | null>(null);
const afterInsideRef = React.useRef<HTMLSpanElement | null>(null);
const beforeOutsideRef = React.useRef<HTMLSpanElement | null>(null);
const afterOutsideRef = React.useRef<HTMLSpanElement | null>(null);

// 自动尺寸调整引用
const popupAutoSizeResetRef = React.useRef<NavigationMenuPopupAutoSizeResetState>({
  abortController: null,
  owner: null,
});
```

**初始状态说明：**
- 所有 DOM 元素引用初始为 `null`，等待子组件挂载后设置
- `activationDirection` 初始为 `null`，表示无激活方向
- `viewportInert` 初始为 `false`，视口可交互
- `popupAutoSizeResetRef` 用于管理自动尺寸重置的 AbortController

### 2.5 过渡状态初始化

**执行位置：** [NavigationMenuRoot.tsx#L120](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L120)

```tsx
const { mounted, setMounted, transitionStatus } = useTransitionStatus(open);
```

**`useTransitionStatus` hook：**
- 跟踪组件的挂载状态和过渡状态
- `mounted`：是否已挂载（初始为 `false`）
- `transitionStatus`：过渡状态（`'starting'` | `'open'` | `'closed'`）
- 初始时 `open = false`，所以 `mounted = false`，`transitionStatus = 'closed'`

---

## 三、Context Provider 初始化

### 3.1 NavigationMenuRootContext 创建

**执行位置：** [NavigationMenuRoot.tsx#L241-L293](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L241-L293)

```tsx
const contextValue: NavigationMenuRootContext<Value> = React.useMemo(
  () => ({
    open,                    // 是否打开
    value,                   // 当前激活的菜单项值
    setValue,                // 更新值的函数
    mounted,                 // 是否已挂载
    transitionStatus,        // 过渡状态
    positionerElement,       // Positioner DOM 元素
    setPositionerElement,    // 设置 Positioner 元素
    popupElement,            // Popup DOM 元素
    setPopupElement,         // 设置 Popup 元素
    viewportElement,         // Viewport DOM 元素
    setViewportElement,      // 设置 Viewport 元素
    viewportTargetElement,   // Viewport 目标元素
    setViewportTargetElement,// 设置 Viewport 目标
    activationDirection,     // 激活方向
    setActivationDirection,  // 设置激活方向
    floatingRootContext,     // Floating UI 上下文
    setFloatingRootContext,  // 设置 Floating UI 上下文
    currentContentRef,       // 当前内容引用
    nested,                  // 是否嵌套
    rootRef,                 // 根元素引用
    beforeInsideRef,         // 焦点守卫引用
    afterInsideRef,
    beforeOutsideRef,
    afterOutsideRef,
    prevTriggerElementRef,   // 上一个触发器引用
    popupAutoSizeResetRef,   // 自动尺寸重置引用
    delay,                   // 打开延迟
    closeDelay,              // 关闭延迟
    orientation,             // 方向
    viewportInert,           // 视口是否 inert
    setViewportInert,        // 设置视口 inert
  }),
  [/* 依赖数组 */]
);
```

**Context 的作用：**
- 在整个组件树中共享状态和方法
- 子组件通过 `useNavigationMenuRootContext()` 访问

### 3.2 FloatingTree 和 FloatingNode 初始化

**执行位置：** [NavigationMenuRoot.tsx#L295-L308](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L295-L308)

```tsx
const jsx = (
  <NavigationMenuRootContext.Provider value={contextValue}>
    <TreeContext componentProps={componentProps} forwardedRef={forwardedRef}>
      {componentProps.children}
    </TreeContext>
  </NavigationMenuRootContext.Provider>
);

if (!nested) {
  // 顶层菜单：包裹 FloatingTree
  return <FloatingTree>{jsx}</FloatingTree>;
}

return jsx;
```

**FloatingTree 的作用：**
- 来自 Floating UI 库
- 为嵌套的浮动元素提供父子关系管理
- 只在顶层菜单时创建

### 3.3 TreeContext 和 DOM 元素创建

**执行位置：** [NavigationMenuRoot.tsx#L313-L352](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L313-L352)

```tsx
function TreeContext<Value>(props) {
  const nodeId = useFloatingNodeId();  // 生成唯一节点 ID
  const { rootRef, nested, open } = useNavigationMenuRootContext();

  const state: NavigationMenuRootState = {
    open,
    nested,
  };

  // 创建 DOM 元素
  const element = useRenderElement(nested ? 'div' : 'nav', props.componentProps, {
    state,
    ref: [props.forwardedRef, rootRef],
    props: elementProps,
  });

  return (
    <NavigationMenuTreeContext.Provider value={nodeId}>
      <FloatingNode id={nodeId}>{element}</FloatingNode>
    </NavigationMenuTreeContext.Provider>
  );
}
```

**DOM 元素创建规则：**
- 顶层菜单：渲染 `<nav>` 元素
- 嵌套菜单：渲染 `<div>` 元素
- `rootRef` 绑定到该元素
- `FloatingNode` 提供节点 ID 用于 Floating UI 树管理

---

## 四、List 组件初始化

### 4.1 Context 消费

**执行位置：** [NavigationMenuList.tsx#L29-L40](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx#L29-L40)

```tsx
const nodeId = useNavigationMenuTreeContext();

const {
  orientation,
  open,
  floatingRootContext,
  positionerElement,
  value,
  closeDelay,
  viewportElement,
  nested,
} = useNavigationMenuRootContext();
```

### 4.2 交互状态计算

**执行位置：** [NavigationMenuList.tsx#L42-L46](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx#L42-L46)

```tsx
const fallbackContext = React.useMemo(() => getEmptyRootContext(), []);
const context = floatingRootContext || fallbackContext;
const interactionsEnabled = positionerElement != null || value == null;
const hoverInteractionsEnabled =
  positionerElement != null || viewportElement != null || value == null;
```

**初始状态：**
- `floatingRootContext` 初始为 `undefined`，使用 `fallbackContext`
- `interactionsEnabled = true`（因为 `value == null`）
- `hoverInteractionsEnabled = true`

### 4.3 悬停交互初始化

**执行位置：** [NavigationMenuList.tsx#L48-L52](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx#L48-L52)

```tsx
useHoverFloatingInteraction(context, {
  enabled: Boolean(floatingRootContext) && hoverInteractionsEnabled,
  closeDelay,
  nodeId,
});
```

**初始状态：**
- `enabled = false`（因为 `floatingRootContext` 初始为 `undefined`）
- 当 Trigger 激活并设置 `floatingRootContext` 后才启用

### 4.4 关闭交互初始化

**执行位置：** [NavigationMenuList.tsx#L54-L64](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx#L54-L64)

```tsx
const dismiss = useDismiss(context, {
  enabled: interactionsEnabled,
  outsidePressEvent: 'intentional',
  outsidePress(event) {
    const target = getTarget(event) as HTMLElement | null;
    const closestNavigationMenuTrigger = target?.closest(
      `[${NAVIGATION_MENU_TRIGGER_IDENTIFIER}]`,
    );
    return closestNavigationMenuTrigger === null;
  },
});
```

**关闭逻辑：**
- 监听外部点击事件
- 只有点击在触发器外部时才关闭
- 使用 `data-base-ui-navigation-menu-trigger` 属性识别触发器

### 4.5 CompositeRoot 初始化（键盘导航）

**执行位置：** [NavigationMenuList.tsx#L110-L124](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx#L110-L124)

```tsx
return (
  <NavigationMenuDismissContext.Provider value={dismissProps}>
    <CompositeRoot
      render={render}
      className={className}
      style={style}
      state={state}
      refs={[forwardedRef]}
      props={props}
      loopFocus={false}
      orientation={orientation}
      tag="ul"
    />
  </NavigationMenuDismissContext.Provider>
);
```

**CompositeRoot 的作用：**
- 提供键盘导航支持（方向键）
- 管理焦点在触发器之间的移动
- `loopFocus={false}`：焦点不循环
- `orientation`：决定使用哪个方向键

### 4.6 DOM 元素创建

**执行位置：** [NavigationMenuList.tsx#L95-L100](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx#L95-L100)

```tsx
const element = useRenderElement('ul', componentProps, {
  state,
  ref: forwardedRef,
  props,
  enabled: nested,
});
```

**DOM 元素：**
- 渲染 `<ul>` 元素
- 包含所有 `<NavigationMenu.Item>`

---

## 五、Item 组件初始化

### 5.1 Value 生成

**执行位置：** [NavigationMenuItem.tsx#L21-L24](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/item/NavigationMenuItem.tsx#L21-L24)

```tsx
const { render, className, style, value: valueProp, ...elementProps } = componentProps;

const fallbackValue = useBaseUiId();  // 自动生成唯一 ID
const value = valueProp ?? fallbackValue;
```

**Value 的作用：**
- 唯一标识菜单项
- 用于受控模式下判断哪个菜单项激活
- 如果未提供 `value` prop，自动生成

### 5.2 ItemContext 创建

**执行位置：** [NavigationMenuItem.tsx#L31-L36](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/item/NavigationMenuItem.tsx#L31-L36)

```tsx
const contextValue: NavigationMenuItemContextValue = React.useMemo(() => ({ value }), [value]);

return (
  <NavigationMenuItemContext.Provider value={contextValue}>
    {element}
  </NavigationMenuItemContext.Provider>
);
```

### 5.3 DOM 元素创建

**执行位置：** [NavigationMenuItem.tsx#L26-L29](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/item/NavigationMenuItem.tsx#L26-L29)

```tsx
const element = useRenderElement('li', componentProps, {
  ref: forwardedRef,
  props: elementProps,
});
```

**DOM 元素：**
- 渲染 `<li>` 元素
- 包含 `<Trigger>` 和 `<Content>`

---

## 六、Trigger 组件初始化（核心交互）

### 6.1 Context 消费

**执行位置：** [NavigationMenuTrigger.tsx#L82-L111](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L82-L111)

```tsx
const {
  value,
  setValue,
  mounted,
  open,
  positionerElement,
  setActivationDirection,
  setFloatingRootContext,
  popupElement,
  viewportElement,
  transitionStatus,
  rootRef,
  beforeOutsideRef,
  afterOutsideRef,
  afterInsideRef,
  beforeInsideRef,
  prevTriggerElementRef,
  popupAutoSizeResetRef,
  currentContentRef,
  delay,
  closeDelay,
  orientation,
  setViewportInert,
  nested,
} = useNavigationMenuRootContext();

const { value: itemValue } = useNavigationMenuItemContext();
const nodeId = useNavigationMenuTreeContext();
const tree = useFloatingTree();
const dismissProps = useNavigationMenuDismissContext();
const direction = useDirection();
```

### 6.2 激活状态判断

**执行位置：** [NavigationMenuTrigger.tsx#L128-L132](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L128-L132)

```tsx
const isActiveItem = open && value === itemValue;
const isActiveItemRef = useValueAsRef(isActiveItem);
const interactionsEnabled = (positionerElement != null || value == null) && !disabled;
const hoverFloatingElement = positionerElement || viewportElement;
const hoverInteractionsEnabled = (hoverFloatingElement != null || value == null) && !disabled;
```

**初始状态：**
- `isActiveItem = false`（因为 `open = false`）
- `interactionsEnabled = true`
- `hoverInteractionsEnabled = true`

### 6.3 Timeout 和 AnimationFrame 初始化

**执行位置：** [NavigationMenuTrigger.tsx#L113-L117](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L113-L117)

```tsx
const stickIfOpenTimeout = useTimeout();
const focusFrame = useAnimationFrame();
const mutationFrame = useAnimationFrame();
const resizeFrame = useAnimationFrame();
const sizeFrame = useAnimationFrame();
```

**作用：**
- `stickIfOpenTimeout`：管理"粘性"打开状态
- 各种 `AnimationFrame`：用于延迟执行尺寸测量和更新

### 6.4 本地状态初始化

**执行位置：** [NavigationMenuTrigger.tsx#L119-L126](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L119-L126)

```tsx
const [triggerElement, setTriggerElement] = React.useState<HTMLElement | null>(null);
const [stickIfOpen, setStickIfOpen] = React.useState(true);
const [pointerType, setPointerType] = React.useState<'mouse' | 'touch' | 'pen' | ''>('');

const triggerElementRef = React.useRef<HTMLElement | null>(null);
const allowFocusRef = React.useRef(false);
const prevSizeRef = React.useRef(DEFAULT_SIZE);
const skipAutoSizeSyncRef = React.useRef(false);
```

**初始状态：**
- `triggerElement = null`（等待 DOM 挂载）
- `stickIfOpen = true`（防止快速点击关闭）
- `pointerType = ''`（未检测）
- `prevSizeRef = { width: 0, height: 0 }`

### 6.5 Floating UI Context 初始化

**执行位置：** [NavigationMenuTrigger.tsx#L554-L561](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L554-L561)

```tsx
const context = useFloatingRootContext({
  open,
  onOpenChange: handleOpenChange,
  elements: {
    reference: triggerElement,      // 触发器元素
    floating: hoverFloatingElement, // 浮动元素（Positioner 或 Viewport）
  },
});
```

**初始状态：**
- `open = false`
- `reference = null`（等待 Trigger DOM 挂载）
- `floating = null`（等待 Positioner/Viewport DOM 挂载）

### 6.6 悬停交互初始化

**执行位置：** [NavigationMenuTrigger.tsx#L594-L605](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L594-L605)

```tsx
const hoverProps = useHoverReferenceInteraction(context, {
  enabled: hoverInteractionsEnabled,
  move: false,
  handleClose: safePolygon({
    blockPointerEvents: shouldBlockSafePolygonPointerEvents,
    getScope,
  }),
  restMs: mounted && positionerElement ? 0 : delay,
  delay: { close: closeDelay },
  triggerElementRef,
  getHandleCloseContext: getInlineHandleCloseContext,
});
```

**safePolygon 算法：**
- 创建从触发器到浮动元素的安全多边形区域
- 鼠标在多边形内移动不会关闭菜单
- 防止鼠标斜向移动时意外关闭

### 6.7 点击交互初始化

**执行位置：** [NavigationMenuTrigger.tsx#L612-L616](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L612-L616)

```tsx
const click = useClick(context, {
  enabled: interactionsEnabled,
  stickIfOpen,
  toggle: isActiveItem,
});
```

### 6.8 DOM 元素创建和事件绑定

**执行位置：** [NavigationMenuTrigger.tsx#L731-L811](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L731-L811)

```tsx
const defaultProps: HTMLProps = {
  tabIndex: 0,
  onMouseEnter: handleOpenEvent,      // 鼠标进入
  onClick: handleOpenEvent,           // 点击
  onPointerEnter: handleSetPointerType, // 指针类型检测
  onPointerDown: handleTriggerPointerDown,
  'aria-expanded': isActiveItem,      // 无障碍属性
  'aria-controls': isActiveItem ? popupElement?.id : undefined,
  [NAVIGATION_MENU_TRIGGER_IDENTIFIER as string]: '', // data 属性
  onFocus() { /* ... */ },
  onMouseMove() { /* ... */ },
  onKeyDown(event) { /* ... */ },     // 键盘导航
  onBlur(event) { /* ... */ },        // 焦点离开
};

return (
  <CompositeItem
    tag="button"
    render={render}
    className={className}
    style={style}
    state={state}
    stateAttributesMapping={pressableTriggerOpenStateMapping}
    refs={[forwardedRef, handleTriggerElement, buttonRef]}
    props={[
      referenceProps,              // Floating UI 交互属性
      dismissProps?.reference || EMPTY_ARRAY,
      defaultProps,                // 默认事件处理器
      elementProps,                // 用户自定义属性
      getButtonProps,              // 按钮属性
    ]}
  />
);
```

**DOM 元素：**
- 渲染 `<button>` 元素
- 绑定所有事件监听器
- `data-base-ui-navigation-menu-trigger` 属性用于识别

### 6.9 FocusGuard 初始化（激活时）

**执行位置：** [NavigationMenuTrigger.tsx#L812-L857](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L812-L857)

```tsx
{isActiveItem && (
  <React.Fragment>
    <FocusGuard
      ref={beforeOutsideRef}
      onFocus={(event) => { /* ... */ }}
    />
    <span aria-owns={viewportElement?.id} style={ownerVisuallyHidden} />
    <FocusGuard
      ref={afterOutsideRef}
      onFocus={(event) => { /* ... */ }}
    />
  </React.Fragment>
)}
```

**FocusGuard 的作用：**
- 创建焦点陷阱，确保焦点循环正确
- 只在 `isActiveItem = true` 时渲染
- 初始时不渲染（因为 `isActiveItem = false`）

---

## 七、Content 组件初始化

### 7.1 打开状态判断

**执行位置：** [NavigationMenuContent.tsx#L60](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/content/NavigationMenuContent.tsx#L60)

```tsx
const open = popupMounted && value === itemValue;
```

**初始状态：**
- `popupMounted = false`（Popup 未挂载）
- `open = false`

### 7.2 过渡状态初始化

**执行位置：** [NavigationMenuContent.tsx#L67](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/content/NavigationMenuContent.tsx#L67)

```tsx
const { mounted, setMounted, transitionStatus } = useTransitionStatus(open);
```

**初始状态：**
- `mounted = false`
- `transitionStatus = 'closed'`

### 7.3 Portal 渲染逻辑

**执行位置：** [NavigationMenuContent.tsx#L133-L172](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/content/NavigationMenuContent.tsx#L133-L172)

```tsx
const portalContainer = viewportTargetElement || viewportElement;
const hidden = keepMounted && !mounted;
const shouldRenderInline = keepMounted && !portalContainer && !hasMountedInPortal;

if (shouldRenderInline) {
  // 内联渲染（keepMounted 模式）
  return <CompositeRoot /* ... */ />;
}

if (!portalContainer || (!mounted && !keepMounted)) {
  return null; // 初始时不渲染
}

// Portal 渲染到 Viewport
return ReactDOM.createPortal(
  <FloatingNode id={nodeId}>
    <CompositeRoot /* ... */ />
  </FloatingNode>,
  portalContainer,
);
```

**初始状态：**
- `portalContainer = null`（Viewport 未挂载）
- 返回 `null`，不渲染内容

---

## 八、Portal、Positioner、Popup、Viewport 初始化

### 8.1 Portal 初始化

**执行位置：** [NavigationMenuPortal.tsx#L14-L32](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/portal/NavigationMenuPortal.tsx#L14-L32)

```tsx
const { mounted } = useNavigationMenuRootContext();

const shouldRender = mounted || keepMounted;
if (!shouldRender) {
  return null; // 初始时不渲染
}

return (
  <NavigationMenuPortalContext.Provider value={keepMounted}>
    <FloatingPortal ref={forwardedRef} {...portalProps} />
  </NavigationMenuPortalContext.Provider>
);
```

**初始状态：**
- `mounted = false`
- `keepMounted = false`（默认）
- 返回 `null`

### 8.2 Positioner 初始化

**执行位置：** [NavigationMenuPositioner.tsx#L39-L186](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/positioner/NavigationMenuPositioner.tsx#L39-L186)

**关键初始化步骤：**

```tsx
// 1. 消费 Context
const {
  open,
  mounted,
  positionerElement,
  setPositionerElement,
  floatingRootContext,
  nested,
  transitionStatus,
} = useNavigationMenuRootContext();

// 2. 锚点定位初始化
const positioning = useAnchorPositioning({
  anchor: anchor ?? domReference ?? prevTriggerElementRef,
  positionMethod,
  mounted,
  side,
  sideOffset,
  align,
  alignOffset,
  collisionBoundary,
  collisionPadding,
  // ...
});

// 3. DOM 元素创建
const element = usePositioner(componentProps, state, {
  styles: positioning.positionerStyles,
  transitionStatus,
  props: elementProps,
  refs: [forwardedRef, setPositionerElement, positionerRef],
  hidden: !mounted,
  inert: !open,
});
```

**初始状态：**
- `hidden = true`（因为 `mounted = false`）
- `inert = true`（因为 `open = false`）

### 8.3 Popup 初始化

**执行位置：** [NavigationMenuPopup.tsx#L27-L78](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/popup/NavigationMenuPopup.tsx#L27-L78)

```tsx
const { open, transitionStatus, setPopupElement } = useNavigationMenuRootContext();

const state: NavigationMenuPopupState = {
  open,
  transitionStatus,
  side: positioning.side,
  align: positioning.align,
  anchorHidden: positioning.anchorHidden,
};

const element = useRenderElement('nav', componentProps, {
  state,
  ref: [forwardedRef, setPopupElement],
  props: [/* ... */],
  stateAttributesMapping,
});
```

**DOM 元素：**
- 渲染 `<nav>` 元素
- `setPopupElement` 绑定到 Root Context

### 8.4 Viewport 初始化

**执行位置：** [NavigationMenuViewport.tsx#L72-L132](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/viewport/NavigationMenuViewport.tsx#L72-L132)

```tsx
const {
  setViewportElement,
  setViewportTargetElement,
  floatingRootContext,
  prevTriggerElementRef,
  viewportInert,
  setViewportInert,
} = useNavigationMenuRootContext();

const element = useRenderElement('div', componentProps, {
  ref: [forwardedRef, setViewportElement],
  props: [
    {
      id,
      onBlur(event) { /* ... */ },
      ...(!hasPositioner && viewportInert && { inert: inertValue(true) }),
      children: hasPositioner ? (
        children
      ) : (
        <Guards>
          <div ref={setViewportTargetElement}>{children}</div>
        </Guards>
      ),
    },
    elementProps,
  ],
});
```

**DOM 元素：**
- 渲染 `<div>` 元素
- `setViewportElement` 和 `setViewportTargetElement` 绑定到 Root Context
- 包含 FocusGuard（通过 `<Guards>` 组件）

---

## 九、Effect 初始化（副作用）

### 9.1 Root 组件的 Effect

**执行位置：** [NavigationMenuRoot.tsx#L122-L237](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx#L122-L237)

```tsx
// 1. 关闭时设置固定尺寸（用于退出动画）
useIsoLayoutEffect(() => {
  if (open) return;
  if (!positionerElement || !popupElement) return;
  
  const closeTransitionSize = getPositionerFixedSize(positionerElement);
  if (!closeTransitionSize) return;
  
  setSharedFixedSize(popupElement, positionerElement, closeTransitionSize.width, closeTransitionSize.height);
}, [open, popupElement, positionerElement]);

// 2. value 变化时重置 viewportInert
React.useEffect(() => {
  setViewportInert(false);
}, [value]);

// 3. 命令式操作
React.useImperativeHandle(actionsRef, () => ({ unmount: handleUnmount }), [handleUnmount]);

// 4. 动画完成回调
useOpenChangeComplete({
  enabled: !actionsRef,
  open,
  ref: { current: popupElement },
  onComplete() {
    if (!open) {
      handleUnmount();
    }
  },
});
```

### 9.2 Trigger 组件的 Effect

**执行位置：** [NavigationMenuTrigger.tsx#L151-L512](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L151-L512)

**关键 Effect：**

```tsx
// 1. ResizeObserver 监听 Popup 尺寸变化
useIsoLayoutEffect(() => {
  if (!popupElement || typeof ResizeObserver !== 'function') return;
  
  const resizeObserver = new ResizeObserver(() => {
    prevSizeRef.current = {
      width: popupElement.offsetWidth,
      height: popupElement.offsetHeight,
    };
  });
  
  resizeObserver.observe(popupElement);
  
  return () => resizeObserver.disconnect();
}, [popupElement]);

// 2. 窗口 resize 监听
React.useEffect(() => {
  if (!open || !isActiveItem || !popupElement || !positionerElement) return;
  
  const win = ownerWindow(positionerElement);
  function handleResize() {
    resizeFrame.cancel();
    resizeFrame.request(syncCurrentSize);
  }
  
  const unsubscribe = addEventListener(win, 'resize', handleResize);
  return () => {
    resizeFrame.cancel();
    unsubscribe();
  };
}, [open, isActiveItem, popupElement, positionerElement, resizeFrame, syncCurrentSize]);

// 3. MutationObserver 监听内容变化
React.useEffect(() => {
  const observedElement = currentContentRef.current;
  if (!observedElement || !popupElement || !isActiveItem || typeof MutationObserver !== 'function') return;
  
  const mutationObserver = new MutationObserver(() => {
    if (transitionStatus === 'starting' || popupElement.hasAttribute(TransitionStatusDataAttributes.startingStyle)) {
      syncCurrentSize();
      return;
    }
    
    const { size, syncPositioner } = getMutationBaseline();
    if (syncPositioner) {
      handleInterruptedMutationResize(size.width, size.height);
      return;
    }
    
    handleValueChange(size.width, size.height);
  });
  
  mutationObserver.observe(observedElement, {
    childList: true,
    subtree: true,
    characterData: true,
    attributes: true,
    attributeFilter: ['hidden'],
  });
  
  return () => mutationObserver.disconnect();
}, [/* ... */]);

// 4. 激活时设置 FloatingRootContext
useIsoLayoutEffect(() => {
  if (isActiveItem) {
    setFloatingRootContext(context);
    prevTriggerElementRef.current = triggerElement;
  }
}, [isActiveItem, context, setFloatingRootContext, prevTriggerElementRef, triggerElement]);
```

---

## 十、初始化完成状态

### 10.1 初始 DOM 结构

```html
<nav data-base-ui-navigation-menu>                    <!-- Root -->
  <ul>                                                <!-- List (CompositeRoot) -->
    <li>                                              <!-- Item -->
      <button                                         <!-- Trigger -->
        data-base-ui-navigation-menu-trigger
        aria-expanded="false"
        tabindex="0"
      >
        菜单项
        <span aria-hidden="true">▼</span>             <!-- Icon -->
      </button>
      <!-- Content: null (未激活时不渲染) -->
    </li>
  </ul>
  <!-- Portal: null (mounted=false 时不渲染) -->
</nav>
```

### 10.2 初始状态总结

| 状态 | 初始值 | 说明 |
|------|--------|------|
| `open` | `false` | 菜单关闭 |
| `value` | `null` | 无激活项 |
| `mounted` | `false` | 未挂载 |
| `transitionStatus` | `'closed'` | 过渡状态：关闭 |
| `activationDirection` | `null` | 无激活方向 |
| `viewportInert` | `false` | 视口可交互 |
| `floatingRootContext` | `undefined` | Floating UI 上下文未设置 |
| `positionerElement` | `null` | Positioner DOM 未挂载 |
| `popupElement` | `null` | Popup DOM 未挂载 |
| `viewportElement` | `null` | Viewport DOM 未挂载 |

---

## 十一、异常处理机制

### 11.1 Context 缺失检查

**执行位置：** [NavigationMenuRootContext.ts#L59-L75](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRootContext.ts#L59-L75)

```tsx
function useNavigationMenuRootContext<Value = any>(optional?: boolean) {
  const context = React.useContext<NavigationMenuRootContext<Value> | undefined>(
    NavigationMenuRootContext,
  );
  if (context === undefined && !optional) {
    throw new Error(
      'Base UI: NavigationMenuRootContext is missing. Navigation Menu parts must be placed within <NavigationMenu.Root>.',
    );
  }
  return context;
}
```

**检查点：**
- 所有子组件消费 Context 时都会检查
- 如果缺失，抛出明确的错误信息
- `optional` 参数允许在某些场景下可选消费

### 11.2 ItemContext 缺失检查

**执行位置：** [NavigationMenuItemContext.ts#L12-L19](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/item/NavigationMenuItemContext.ts#L12-L19)

```tsx
export function useNavigationMenuItemContext() {
  const value = React.useContext(NavigationMenuItemContext);
  if (!value) {
    throw new Error(
      'Base UI: NavigationMenuItem parts must be used within a <NavigationMenu.Item>.',
    );
  }
  return value;
}
```

### 11.3 PortalContext 缺失检查

**执行位置：** [NavigationMenuPortalContext.ts#L6-L11](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/portal/NavigationMenuPortalContext.ts#L6-L11)

```tsx
export function useNavigationMenuPortalContext() {
  const value = React.useContext(NavigationMenuPortalContext);
  if (value === undefined) {
    throw new Error('Base UI: <NavigationMenu.Portal> is missing.');
  }
  return value;
}
```

### 11.4 DOM 元素空检查

**执行位置：** 多个组件中的条件判断

```tsx
// Trigger 中的示例
if (!popupElement || !positionerElement) {
  return; // 提前返回，避免空指针
}

// Content 中的示例
if (!portalContainer || (!mounted && !keepMounted)) {
  return null; // 不渲染
}
```

### 11.5 Feature 检测

**执行位置：** [NavigationMenuTrigger.tsx#L375-L391](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx#L375-L391)

```tsx
useIsoLayoutEffect(() => {
  if (!popupElement || typeof ResizeObserver !== 'function') {
    return undefined; // 不支持 ResizeObserver 时跳过
  }
  // ...
}, [popupElement]);
```

**Feature 检测：**
- `ResizeObserver`：用于监听尺寸变化
- `MutationObserver`：用于监听 DOM 变化
- 如果浏览器不支持，优雅降级

---

## 十二、与其他系统模块的交互

### 12.1 Floating UI 集成

**关键模块：**
- `FloatingTree` / `FloatingNode`：嵌套浮动元素管理
- `useFloatingRootContext`：浮动上下文
- `useHoverReferenceInteraction` / `useHoverFloatingInteraction`：悬停交互
- `useClick`：点击交互
- `useDismiss`：关闭交互
- `safePolygon`：安全多边形算法

### 12.2 Composite Widget 集成

**关键模块：**
- `CompositeRoot`：键盘导航容器
- `CompositeItem`：键盘导航项

**作用：**
- 提供方向键导航支持
- 管理焦点在触发器之间的移动
- 支持 `orientation` 配置

### 12.3 Base UI Utils 集成

**关键模块：**
- `useControlled`：受控/非受控状态管理
- `useStableCallback`：稳定回调
- `useIsoLayoutEffect`：同构 layout effect
- `useTimeout` / `useAnimationFrame`：延迟执行
- `ownerDocument` / `ownerWindow`：Shadow DOM 安全
- `contains` / `activeElement`：Shadow DOM 安全的 DOM 遍历

### 12.4 React 集成

**关键模块：**
- `ReactDOM.createPortal`：Portal 渲染
- `ReactDOM.flushSync`：同步更新
- `React.useMemo` / `React.useCallback`：性能优化
- `React.useRef` / `React.useState`：状态管理

---


## 十四、关键配置文件路径

| 文件 | 路径 | 作用 |
|------|------|------|
| Root 组件 | [packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRoot.tsx) | 根组件，状态管理 |
| Root Context | [packages/react/src/navigation-menu/root/NavigationMenuRootContext.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/root/NavigationMenuRootContext.ts) | Context 定义 |
| List 组件 | [packages/react/src/navigation-menu/list/NavigationMenuList.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/list/NavigationMenuList.tsx) | 列表容器 |
| Item 组件 | [packages/react/src/navigation-menu/item/NavigationMenuItem.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/item/NavigationMenuItem.tsx) | 菜单项 |
| Trigger 组件 | [packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/trigger/NavigationMenuTrigger.tsx) | 触发器，核心交互 |
| Content 组件 | [packages/react/src/navigation-menu/content/NavigationMenuContent.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/content/NavigationMenuContent.tsx) | 内容容器 |
| Portal 组件 | [packages/react/src/navigation-menu/portal/NavigationMenuPortal.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/portal/NavigationMenuPortal.tsx) | Portal 渲染 |
| Positioner 组件 | [packages/react/src/navigation-menu/positioner/NavigationMenuPositioner.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/positioner/NavigationMenuPositioner.tsx) | 定位管理 |
| Popup 组件 | [packages/react/src/navigation-menu/popup/NavigationMenuPopup.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/popup/NavigationMenuPopup.tsx) | 弹出层 |
| Viewport 组件 | [packages/react/src/navigation-menu/viewport/NavigationMenuViewport.tsx](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/viewport/NavigationMenuViewport.tsx) | 视口容器 |
| 常量定义 | [packages/react/src/navigation-menu/utils/constants.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/utils/constants.ts) | 延迟时间、属性名 |
| CSS 变量 | [packages/react/src/navigation-menu/popup/NavigationMenuPopupCssVars.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/popup/NavigationMenuPopupCssVars.ts) | Popup CSS 变量 |
| CSS 变量 | [packages/react/src/navigation-menu/positioner/NavigationMenuPositionerCssVars.ts](file:///Users/ll/Desktop/资料/编程/仓库/react/base-ui/packages/react/src/navigation-menu/positioner/NavigationMenuPositionerCssVars.ts) | Positioner CSS 变量 |

---

## 总结

Navigation Menu 的初始化是一个**分阶段、延迟加载**的过程：

1. **阶段 1（同步）**：Root 组件创建 Context 和基础状态
2. **阶段 2（同步）**：List、Item、Trigger 创建 DOM 结构和事件绑定
3. **阶段 3（条件）**：Content、Portal 等根据状态条件渲染
4. **阶段 4（异步）**：Effect 执行，创建 Observer，绑定全局事件
5. **阶段 5（交互触发）**：用户交互后，激活菜单，完成完整初始化

这种设计确保了：
- **性能优化**：未激活的内容不渲染
- **资源节约**：Observer 和事件监听按需创建
- **可访问性**：完整的键盘导航和 ARIA 支持
- **灵活性**：支持嵌套、受控/非受控、多种交互方式

