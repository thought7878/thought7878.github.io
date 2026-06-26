Radix UI 是一个备受推崇的 React 组件库，它的核心理念是**提供无样式（Unstyled）、高度可定制且完全符合无障碍标准（WAI-ARIA）的基础组件（Primitives）**。

在 Radix UI 中，**Dialog（对话框/模态框）** 是最常用的组件之一。它帮你处理了模态框开发中最令人头疼的几个问题：**焦点陷阱（Focus Trap）、键盘导航、屏幕阅读器支持、以及 Portal 渲染（避免 z-index 和 overflow 裁剪问题）**。

以下是对 Radix UI Dialog 的详细介绍，包括其核心组件、使用方法、进阶 API 以及如何为其添加样式和动画。

---

### 一、 核心组件拆解 (Primitives)

Radix Dialog *由多个小组件组合而成，这种“原子化”的设计让你可以**完全控制 DOM 结构和样式***。

1. **`Dialog.Root`**：
   - 包含所有 Dialog 组件的根节点。
   - 负责管理对话框的打开/关闭状态（受控或非受控）。
2. **`Dialog.Trigger`**：
   - 触发对话框打开的按钮（或其他交互元素）。它会自动绑定 `onClick` 事件和必要的无障碍属性（如 `aria-haspopup="dialog"`）。
3. **`Dialog.Portal`**：
   - **非常关键**。*它将对话框的 DOM 节点渲染到 `<body>` 的末尾*。
   - **作用**：*避免对话框被父级元素的 `overflow: hidden` 裁剪，同时轻松解决 `z-index` 层叠上下文问题，确保对话框永远在最顶层*。
1. **`Dialog.Overlay`**：
   - *对话框背后的半透明遮罩层*。
   - 默认情况下，点击 Overlay 会关闭对话框（可以通过事件拦截）。
5. **`Dialog.Content`**：
   - 对话框的主体内容区域。
   - **核心功能**：它包含了**焦点陷阱（Focus Trap）**。当对话框打开时，用户的 Tab 键焦点会被锁定在 Content 内部，无法跑到背后的网页上；关闭时，焦点会自动返回到 `Trigger` 元素上。
6. **`Dialog.Title`** & **`Dialog.Description`**：
   - 为屏幕阅读器提供语义化信息。
   - `Content` 会自动通过 `aria-labelledby` 和 `aria-describedby` 关联这两个元素，确保视障用户能听懂这个对话框是干什么的。
7. **`Dialog.Close`**：
   - 用于关闭对话框的按钮。

---

### 二、 基础用法示例 (配合 Tailwind CSS)

因为 Radix UI **没有任何默认样式**，所以开发者通常使用 Tailwind CSS 或 CSS-in-JS 来为它编写样式。

```tsx
import React from 'react';
import * as Dialog from '@radix-ui/react-dialog';
import { Cross2Icon } from '@radix-ui/react-icons'; // 可选的图标库

const MyDialog = () => (
  <Dialog.Root>
    {/* 触发按钮 */}
    <Dialog.Trigger asChild>
      <button className="bg-violet-500 text-white px-4 py-2 rounded hover:bg-violet-600">
        编辑个人资料
      </button>
    </Dialog.Trigger>

    {/* Portal 将内容传送到 body 底部 */}
    <Dialog.Portal>
      {/* 遮罩层 */}
      <Dialog.Overlay className="fixed inset-0 bg-black/50 data-[state=open]:animate-overlayShow" />
      
      {/* 内容区域 */}
      <Dialog.Content className="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-8 rounded-lg shadow-lg w-[90vw] max-w-md focus:outline-none data-[state=open]:animate-contentShow">
        
        {/* 标题与描述 (无障碍必需) */}
        <Dialog.Title className="text-xl font-bold text-gray-900 m-0">
          编辑个人资料
        </Dialog.Title>
        <Dialog.Description className="text-gray-500 mt-2 mb-6">
          在此处更改您的个人信息。完成后点击保存。
        </Dialog.Description>

        {/* 表单内容 */}
        <fieldset className="mb-4">
          <label className="text-gray-700 w-24 inline-block" htmlFor="name">姓名</label>
          <input className="w-full border px-2 py-1 rounded" id="name" defaultValue="张三" />
        </fieldset>

        {/* 底部操作按钮 */}
        <div className="mt-6 flex justify-end gap-3">
          <Dialog.Close asChild>
            <button className="px-4 py-2 bg-gray-200 rounded hover:bg-gray-300">
              取消
            </button>
          </Dialog.Close>
          <button className="px-4 py-2 bg-violet-500 text-white rounded hover:bg-violet-600">
            保存更改
          </button>
        </div>

        {/* 右上角关闭图标 */}
        <Dialog.Close asChild>
          <button className="absolute top-3 right-3 text-gray-400 hover:text-gray-600" aria-label="Close">
            <Cross2Icon />
          </button>
        </Dialog.Close>
      </Dialog.Content>
    </Dialog.Portal>
  </Dialog.Root>
);

export default MyDialog;
```

---

### 三、 进阶 API 与状态控制

#### 1. 受控状态 (Controlled State)
如果你需要在外部控制 Dialog 的开关（例如通过 URL 参数、Redux 状态或父组件逻辑），可以使用 `open` 和 `onOpenChange`：

```tsx
const [isOpen, setIsOpen] = React.useState(false);

<Dialog.Root open={isOpen} onOpenChange={setIsOpen}>
  {/* ... */}
</Dialog.Root>
```

#### 2. 模态 (Modal) vs 非模态 (Non-modal)
默认情况下，Dialog 是**模态**的（`modal={true}`），这意味着*背后的页面会被冻结，无法滚动，也无法被点击，焦点被锁定*。
如果你需要一个**非模态**对话框（例如：一个悬浮的工具箱，*允许用户同时与背后的主页面交互*），可以设置 `modal={false}`：

```tsx
<Dialog.Root modal={false}>
  {/* 此时背后的页面依然可以滚动和点击 */}
</Dialog.Root>
```

#### 3. 拦截关闭行为 (Intercepting Events)
有时候，用户在表单中填写了内容，点击了遮罩层（Overlay）或按下了 `Esc` 键，你不希望它直接关闭，而是弹出一个“确认放弃保存”的提示。Radix 提供了事件拦截 API：

```tsx
<Dialog.Content
  // 拦截点击遮罩层或外部区域
  onPointerDownOutside={(e) => {
    if (hasUnsavedChanges) {
      e.preventDefault(); // 阻止关闭
      alert("您有未保存的更改！");
    }
  }}
  // 拦截 Esc 键
  onEscapeKeyDown={(e) => {
    if (hasUnsavedChanges) {
      e.preventDefault(); // 阻止关闭
    }
  }}
>
  {/* ... */}
</Dialog.Content>
```
*注意：`onInteractOutside` 可以捕获所有的外部交互（包括触摸和鼠标点击）。*

---

### 四、 动画支持 (CSS Animations)

Radix UI 通过在组件上注入 `data-state` 属性来配合 CSS 实现丝滑的进入/退出动画。

`Dialog.Content` 和 `Dialog.Overlay` 会动态拥有以下属性：
- `data-state="open"`
- `data-state="closed"`

**CSS (或 Tailwind) 动画示例：**

```css
/* 使用 CSS 关键帧 */
@keyframes overlayShow {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes contentShow {
  from { opacity: 0; transform: translate(-50%, -48%) scale(0.96); }
  to { opacity: 1; transform: translate(-50%, -50%) scale(1); }
}

/* 应用于组件 */
.DialogOverlay[data-state='open'] {
  animation: overlayShow 150ms cubic-bezier(0.16, 1, 0.3, 1);
}
.DialogContent[data-state='open'] {
  animation: contentShow 150ms cubic-bezier(0.16, 1, 0.3, 1);
}
```
*注：如果你使用 Tailwind，可以像上面的基础示例那样，利用 `tailwindcss-animate` 插件或自定义配置通过 `data-[state=open]:animate-xxx` 来实现。*

---

### 五、 无障碍设计 (Accessibility / A11y) 细节

这是 Radix UI 最大的卖点。如果你自己手写一个 Dialog，你需要处理以下所有细节，而 Radix 已经帮你做好了：

1. **键盘支持**：
   - 按 `Space` 或 `Enter` 键激活 `Trigger` 打开对话框。
   - 按 `Esc` 键关闭对话框。
   - 打开时，焦点自动移动到 `Content` 内的第一个可聚焦元素（或 `Content` 本身）。
   - **Tab 循环（Focus Trap）**：在对话框内按 `Tab`，焦点会在对话框内的元素循环，绝不会跑到背后的网页上。
2. **屏幕阅读器 (Screen Readers)**：
   - `Dialog.Content` 具有 `role="dialog"` 和 `aria-modal="true"` 属性。
   - `aria-labelledby` 自动指向 `Dialog.Title` 的 ID。
   - `aria-describedby` 自动指向 `Dialog.Description` 的 ID。
   - 当对话框打开时，屏幕阅读器会立刻朗读标题和描述。
3. **背景滚动锁定 (Scroll Lock)**：
   - 当对话框打开时，Radix 会自动锁定 `<body>` 的滚动条，防止背后的页面发生滚动穿透（Scroll Chaining）问题，并在关闭时恢复原来的滚动位置。

---

### 六、 总结：什么时候该用 Radix Dialog？

**推荐使用场景：**
- 你的项目有严格的设计系统（Design System），需要完全自定义 UI，不能接受 Ant Design、MUI 等组件库强加的默认样式。
- 你的项目对**无障碍（A11y）** 有严格要求（如面向政府、海外企业级 SaaS、医疗/教育类产品）。
- 你受够了传统 UI 库中 `z-index` 冲突和 `overflow: hidden` 导致模态框被切断的问题。
- 你正在使用 Tailwind CSS 或 CSS Modules，希望拥有 100% 的样式控制权。

**不推荐使用场景：**
- 你需要快速搭建一个后台原型，不想写任何 CSS（此时建议直接使用 Shadcn UI，它底层就是 Radix + Tailwind，或者直接选用 Ant Design / MUI）。