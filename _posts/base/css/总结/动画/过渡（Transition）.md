CSS 的 **过渡（Transitions）** 是一种用于*在元素的样式属性发生变化时，平滑地应用动画效果的技术*。它可以让状态之间的变化更加自然和流畅，从而提升用户体验。

## Why
使用 CSS 的过渡（Transition）的主要原因是：
1. 提升用户体验，让界面变化更加自然。
2. 简化动画实现，减少代码复杂性。
3. 性能优化，利用 GPU 加速提高流畅度。

---

## 提升用户体验
CSS 过渡可以让界面*变化更加自然和平滑*，从而减少用户的认知负担。例如：
- 按钮悬停时的颜色渐变。
- 元素显示或隐藏时的淡入淡出效果。
- 页面滚动时导航栏的动态收起或展开。

这些平滑的变化让用户感受到界面的“生命力”，*而不是突兀地切换状态，从而提升整体的用户体验*。

---

## 简化动画实现、减少代码复杂性
相比于 JavaScript 动画，CSS 过渡是一种*更轻量级的解决方案*。*它不需要编写复杂的逻辑*代码即可实现*简单的动画效果*。例如：

**示例：按钮悬停效果**
```css
button {
  background-color: blue;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: green;
}
```

*通过简单的 `transition` 属性*，按钮在鼠标悬停时会自动产生颜色渐变效果，而无需额外的 JavaScript 代码。

---

## 性能优化
现代浏览器*对 CSS 过渡进行了优化，支持硬件加速*（GPU 加速）。尤其是当使用 `transform` 和 `opacity` 属性时，这些操作可以被 GPU 加速，*动画会在 GPU 上运行*，从而显著提升性能。相比之下，*JavaScript 动画可能会占用更多主线程*，导致页面卡顿。

参考：[[#性能优化]]

示例：
```css
.box {
  width: 100px;
  height: 100px;
  background-color: red;
  transition: transform 0.5s ease;
}

.box:hover {
  transform: translateX(200px);
}
```

使用 `transform` 实现平滑位移。在这个例子中，`transform` 的动画*不会触发重排或重绘，而是由 GPU 直接处理，因此动画流畅且高效*。


---
## What

## 过渡的基本概念

### 作用
- **平滑变化**：*当元素的 CSS 属性值发生变化时*（例如通过伪类 `:hover` 或 JavaScript 动态修改），**过渡为其添加简单的动画效果**。
- **增强体验**：过渡让界面的变化更自然，*避免了突兀的视觉跳跃*。

### 核心组成部分
要实现过渡效果，需要指定以下*四个关键属性：*
1. **`transition-property`**：指定*需要应用过渡效果的 CSS 属性*。
2. **`transition-duration`**：定义*过渡的持续时间*。
3. **`transition-timing-function`**：定义*过渡的速度曲线*（如线性、加速等）。
4. **`transition-delay`**：定义*过渡开始前的延迟时间*。


### 与其他技术的对比

|**特性**|**CSS Transition**|**CSS Animation (@keyframes)**|**JavaScript 动画**|
|---|---|---|---|
|**适用场景**|简单的属性变化|复杂的多阶段动画|高度定制化的交互|
|**性能**|性能较高，适合简单动画|性能较高，适合复杂动画|性能较低，需手动优化|
|**易用性**|简单易用|中等复杂度|较复杂|
|**灵活性**|有限|较高|最高|

---

## 语法

```css
/* 单独设置 */
transition-property: <property>;
transition-duration: <time>;
transition-timing-function: <timing-function>;
transition-delay: <time>;

/* 简写形式 */
transition: <property> <duration> <timing-function> <delay>;
```

- **简写示例**：
  ```css
  transition: background-color 0.5s ease-in-out 0.2s;
  ```
  上述代码表示：
  - 对 `background-color` 属性应用过渡效果。
  - 持续时间为 0.5 秒。
  - 使用 `ease-in-out` 速度曲线。
  - 延迟 0.2 秒后开始。

---

## 核心属性详解

### `transition-property`
- **作用**：指定*需要应用过渡效果的 CSS 属性*。
- **取值**：
  - `<property>`：具体属性名，如 `width`、`background-color`。
  - `all`：*对所有可过渡的属性*应用过渡效果。
  - `none`：不应用任何过渡效果。

### `transition-duration`
- **作用**：定义*过渡的持续时间*。
- **取值**：时间值，单位为秒（`s`）或毫秒（`ms`）。
  - 示例：`0.5s` 表示 0.5 秒，`500ms` 表示 500 毫秒。

### `transition-timing-function`
- **作用**：定义*过渡的速度曲线*。
- **常见取值**：
  - `ease`（默认值）：慢速开始，快速中间，缓慢结束。
  - `linear`：匀速变化。
  - `ease-in`：慢速开始，加速结束。
  - `ease-out`：快速开始，减速结束。
  - `ease-in-out`：慢速开始和结束，中间加速。
  - `cubic-bezier(n1, n2, n3, n4)`：自定义贝塞尔曲线。

### `transition-delay`
- **作用**：定义过渡开始前的延迟时间。
- **取值**：时间值，单位为秒（`s`）或毫秒（`ms`）。
  - 示例：`0.2s` 表示延迟 0.2 秒。

---

## 注意事项

### 可过渡的属性
   - *并非所有 CSS 属性都支持过渡效果*。常见的可过渡属性包括：
     - 尺寸相关：`width`、`height`、`margin`、`padding`。
     - 颜色相关：`color`、`background-color`、`border-color`。
     - 变形相关：`transform`（如 `rotate`、`scale`、`translate`）。
     - 透明度：`opacity`。
   - *不支持过渡的属性*：如 `display` 和 `visibility`。

### 性能优化
尽量避免对昂贵的属性（如 `width`、`height`）进行过渡，**推荐使用 `transform` 和 `opacity`，因为它们由 GPU 加速，性能更高**。

以下属性是 CSS 过渡中性能较高的选择：
- `transform`（如 `translate`, `scale`, `rotate`）
- `opacity`
- `filter`（部分场景下）

*避免对昂贵的属性*（如 `width`, `height`, `margin`, `box-shadow`）进行频繁的过渡操作，因为它们可能*触发重排（reflow）或重绘（repaint）*，影响性能。


### 与其他动画技术的结合
尽管 CSS 过渡适合简单的动画效果，但对于复杂的动画需求，可以结合其他技术（如 `@keyframes` 或 JavaScript）来实现更丰富的交互效果。

**示例：结合 `@keyframes` 实现复杂动画**
```css
@keyframes slideIn {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(0);
  }
}

.box {
  animation: slideIn 1s ease-in-out;
  transition: opacity 0.5s ease;
}

.box:hover {
  opacity: 0.5;
}
```

### 兼容性
   - 过渡效果在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

## 应用场景
CSS 过渡可以应用于各种常见的交互场景，包括但不限于：
- **按钮和链接**：悬停效果、点击反馈。
- **导航菜单**：展开/折叠效果。
- **模态框**：显示/隐藏时的淡入淡出。
- **图片轮播**：平滑切换效果。
- **加载动画**：进度条或旋转图标。

### 示例 1：按钮悬停效果
```css
.button {
    background-color: lightblue;
    color: white;
    padding: 10px 20px;
    border: none;
    transition: background-color 0.3s ease, transform 0.3s ease;
}
.button:hover {
    background-color: coral;
    transform: scale(1.1); /* 放大效果 */
}
```
效果：
- 鼠标悬停时，按钮背景颜色渐变，并伴随轻微放大效果。

---

### 示例 2：卡片翻转效果
```css
.card {
    width: 200px;
    height: 200px;
    background-color: lightblue;
    transition: transform 0.6s ease-in-out;
}
.card:hover {
    transform: rotateY(180deg); /* 3D 翻转效果 */
}
```
效果：
- 鼠标悬停时，卡片会沿 Y 轴翻转 180 度。

---

### 示例 3：延迟加载（非hover情况）
```css
.image {
    opacity: 0;
    transition: opacity 1s ease-in-out 0.5s; /* 延迟 0.5 秒后开始 */
}
.image.loaded {
    opacity: 1; /* 图片加载完成后显示 */
}
```
效果：
- 图片加载完成后，经过 0.5 秒延迟，逐渐显现出来。


---

## 完整的示例

以下是一个完整的示例，展示如何使用过渡效果：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS Transitions Example</title>
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: lightblue;
            transition: all 0.5s ease-in-out; /* 定义过渡效果 */
        }
        .box:hover {
            width: 200px; /* 鼠标悬停时宽度变为 200px */
            background-color: coral; /* 背景颜色变为珊瑚色 */
            transform: rotate(45deg); /* 添加旋转效果 */
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```


---

## 总结

- **核心功能**：
  - CSS 过渡用于在属性值变化时添加平滑的动画效果。
- **常用技巧**：
  - 结合 `transform` 和 `opacity` 提升性能。
  - 使用 `transition-delay` 实现延迟效果。
- **最佳实践**：
  - 明确指定需要过渡的属性，避免滥用 `all`。
  - 优先选择 GPU 加速的属性（如 `transform` 和 `opacity`）以提升性能。

通过合理使用 CSS 过渡，您可以轻松实现流畅且美观的动态效果！

