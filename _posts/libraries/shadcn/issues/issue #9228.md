https://github.com/shadcn-ui/ui/issues/9228
          
## Bug 详解：CommandItem 选中状态样式错误

### 问题概述

在 **radix-nova** 风格下，`CommandItem` 组件**始终显示为选中状态**，无论是否真正被选中。

---

### 根本原因

问题出在 CSS 样式文件中对 `data-selected` 属性的选择器写法错误。

**错误写法：**
```css
data-selected:bg-muted
```

**正确写法：**
```css
data-[selected=true]:bg-muted
```

---

### 技术细节

#### 1. Tailwind CSS 的 `data-*` 属性选择器语法

在 Tailwind CSS 中，针对 HTML 的 `data-*` 属性，有两种写法：

| 写法 | 含义 |
|------|------|
| `data-selected:bg-muted` | 匹配**任意** `data-selected` 属性（只要属性存在就生效，不关心值） |
| `data-[selected=true]:bg-muted` | 精确匹配 `data-selected="true"` 时才生效 |

#### 2. Radix UI Command 组件的行为

Radix UI 的 `CommandItem` 组件会在 DOM 上渲染 `data-selected` 属性。关键点在于：**未选中的项目可能也会带有 `data-selected="false"` 属性**。

当使用 `data-selected:bg-muted` 时：
- 只要元素上存在 `data-selected` 属性（无论是 `"true"` 还是 `"false"`），样式就会生效
- 结果：**所有 CommandItem 都显示为选中状态**

当使用 `data-[selected=true]:bg-muted` 时：
- 只有 `data-selected="true"` 时样式才生效
- 结果：**只有真正被选中的项才显示高亮**

#### 3. 为什么 shadcn 官网看起来正常？

正如评论者 @brentarcane 指出的，shadcn 官网通过自定义 `@custom-variant` 绕过了这个问题：

```css
@custom-variant data-selected {
  &:where([data-selected="true"]) {
    @slot;
  }
}
```

这段代码重新定义了 `data-selected` 变体，让它只在 `data-selected="true"` 时才生效。但这本质上是一种 **hack  workaround**，正确的做法应该是直接在组件中使用 `data-[selected=true]:bg-muted`。

---

### 影响范围

- **受影响组件**：Command（`CommandItem`）
- **受影响风格**：radix-nova（以及 Base UI command 组件）
- **受影响文件**：[style-nova.css](https://github.com/shadcn-ui/ui/blob/main/apps/v4/registry/styles/style-nova.css#L386)

---

### 修复方案

将样式中的 `data-selected:bg-muted` 改为 `data-[selected=true]:bg-muted`，确保只有真正被选中的 `CommandItem` 才应用 `bg-muted` 背景色。

该 bug 已有两个相关 PR 提交修复：
- [#9229](https://github.com/shadcn-ui/ui/pull/9229)
- [#9254](https://github.com/shadcn-ui/ui/pull/9254)