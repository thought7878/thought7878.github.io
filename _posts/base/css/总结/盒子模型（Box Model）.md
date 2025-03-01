**盒子模型（Box Model）** 是 CSS 中描述元素布局和尺寸的核心概念。*每个 HTML 元素都被视为一个矩形的“盒子”*，这个盒子由内容区域、内边距、边框和外边距组成。理解盒子模型是掌握 CSS 布局的基础。


- **盒子的外部显示类型/规则**：block 或 inline，外部显示类型（outer display type），指的是盒子与附近的其他元素的行为。
- **盒子的内部显示类型/规则**：这决定了它们的子元素如何行为，如BFC、IFC、FFC、GFC。

参考：[formatting contexts](https://developer.mozilla.org/en-US/docs/Web/CSS/Visual_formatting_model#formatting_contexts_and_the_display_property)

---

## 1. 盒子模型的组成部分

盒子模型由以下四个部分组成，从内到外依次为：

### 1.1 内容区域（Content）
- **定义**：*元素的实际内容区域*，用于显示文本、图片等内容。
- **属性**：
  - `width` 和 `height` 定义内容区域的宽度和高度。
  - *默认情况下，`width` 和 `height` 只包括内容区域*。

```css
div {
  width: 200px; /* 内容区域宽度 */
  height: 100px; /* 内容区域高度 */
}
```

---

### 1.2 内边距（Padding）
- **定义**：内容区域与边框之间的空间。
- **作用**：增加内容与边框之间的间距，使内容看起来更宽松。
- **属性**：
  - `padding-top`、`padding-right`、`padding-bottom`、`padding-left`
  - 简写形式：`padding: 上 右 下 左;`

```css
div {
  padding: 10px; /* 四个方向的内边距均为 10px */
}
```

---

### 1.3 边框（Border）
- **定义**：围绕内容区域和内边距的*边界线*。
- **作用**：为元素添加视觉上的*分隔或装饰*。
- **属性**：
  - `border-width`：边框的厚度。
  - `border-style`：边框的样式（如 `solid`、`dashed`、`dotted`）。
  - `border-color`：边框的颜色。
  - 简写形式：`border: 宽度 样式 颜色;`

```css
div {
  border: 5px solid black; /* 黑色实线边框，厚度为 5px */
}
```

---

### 1.4 外边距（Margin）
- **定义**：元素与其他元素之间的外部空间。
- **作用**：*控制元素之间的间距*。
- **属性**：
  - `margin-top`、`margin-right`、`margin-bottom`、`margin-left`
  - 简写形式：`margin: 上 右 下 左;`

```css
div {
  margin: 20px; /* 四个方向的外边距均为 20px */
}
```

---

## 2. 盒子模型的两种计算方式

CSS 提供了两种盒子模型的计算方式，*通过 `box-sizing` 属性控制*。

### 2.1 内容盒模型（Content-box，默认值）
- **定义**：`width` 和 `height` *仅包括内容区域*，不包括内边距和边框。
- **计算公式**：
  ```
  总宽度 = width + padding-left + padding-right + border-left + border-right + margin-left + margin-right
  总高度 = height + padding-top + padding-bottom + border-top + border-bottom + margin-top + margin-bottom
  ```

```css
div {
  box-sizing: content-box; /* 默认值 */
  width: 200px;
  padding: 10px;
  border: 5px solid black;
  margin: 20px;
}
/* 实际宽度 = 200 + 10 + 10 + 5 + 5 + 20 + 20 = 270px */
```

---

### 2.2 边框盒模型（Border-box）
- **定义**：`width` 和 `height` *包括内容区域、内边距和边框*。
- **计算公式**：
  ```
  总宽度 = width + margin-left + margin-right
  总高度 = height + margin-top + margin-bottom
  ```

```css
div {
  box-sizing: border-box;
  width: 200px;
  padding: 10px;
  border: 5px solid black;
  margin: 20px;
}
/* 实际宽度 = 200 + 20 + 20 = 240px */
```

---

## 3. 盒子模型的应用场景

### 3.1 创建一致的布局
- 使用 `box-sizing: border-box` 可以让布局更加直观，*避免因内边距和边框导致的宽度溢出问题*。

```css
* {
  box-sizing: border-box; /* 统一使用边框盒模型 */
}
```

---

### 3.2 控制间距
- 使用 `margin` 和 `padding` 控制元素之间的间距和内部空间。
- 示例：创建卡片布局。
```html
<div class="card">
  <img src="image.jpg" alt="Image">
  <p>Card Content</p>
</div>
```

```css
.card {
  width: 300px;
  padding: 20px;
  border: 1px solid #ccc;
  margin: 10px;
}
```

---

### 3.3 居中对齐
- 使用 `margin: auto` 和固定宽度实现水平居中。
- 示例：
```css
.centered {
  width: 200px;
  margin: 0 auto; /* 水平居中 */
}
```

---

## 4. 注意事项与常见问题

### 4.1 外边距折叠（Margin Collapse）
- **定义**：相邻块级元素的外边距会合并成一个外边距。
- **适用场景**：
  - 垂直方向上的兄弟元素。
  - 父子元素之间（如果父元素没有边框、内边距或其他分隔因素）。

```css
div {
  margin-top: 20px;
  margin-bottom: 20px;
}
```

**效果**：
- 实际间距为 `20px`，而不是 `40px`。

---

### 4.2 负边距（Negative Margin）
- **定义**：设置负值的外边距可以*拉近元素之间的距离，甚至重叠*。
- **应用场景**：实现特殊布局效果。
- 示例：
```css
.box {
  margin-left: -10px; /* 向左移动 10px */
}
```

---

### 4.3 边框和背景的关系
- **背景**：元素的背景*会延伸到内边距区域，但不会延伸到外边距区域*。
- 示例：
```css
div {
  background-color: lightblue;
  padding: 20px;
  border: 5px solid black;
  margin: 10px;
}
```

**效果**：
- 背景颜色覆盖内容区域和内边距，但不覆盖外边距。

---

## **5. 总结**
- **盒子模型** 是 CSS 布局的核心，由内容区域、内边距、边框和外边距组成。
- 使用 `box-sizing: border-box` 可以简化布局计算。
- 理解盒子模型有助于解决常见的布局问题，如外边距折叠和宽度溢出。
