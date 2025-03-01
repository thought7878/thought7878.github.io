格式化上下文（Formatting Context）是 CSS 布局中的一个核心概念，它决定了页面中元素的布局规则和相互影响方式。不同类型的格式化上下文会遵循不同的布局模型，例如块级元素、行内元素、弹性盒子或网格布局的规则。以下是主要分类和关键点：

---

### **1. 块级格式化上下文（BFC, Block Formatting Context）**
- **定义**：BFC 是一个*独立的渲染区域*，*内部元素*按照块级盒子模型布局，*与外部元素隔离*。
- **触发条件**：
  - 根元素 `<html>`（默认创建 BFC）
  - `float` 值不为 `none`
  - `position` 为 `absolute` 或 `fixed`
  - `display` 为 `inline-block`、`table-cell`、`table-caption`、`flex`、`grid` 等
  - `overflow` 不为 `visible`（如 `hidden`、`auto`）
- **特性**：
  - 垂直方向排列元素，外边距（margin）可能合并（但 BFC 内部可避免合并）。
  - 内部浮动元素不会影响外部布局（BFC 容器计算高度时会包含浮动元素）。
  - BFC 区域不会与浮动元素重叠（常用于多栏布局）。

---

### **2. 行内格式化上下文（IFC, Inline Formatting Context）**
- **定义**：IFC 用于布局行内元素（如文本、`<span>`），元素在水平方向依次排列。
- **触发条件**：父元素为行内内容（如 `display: inline`）或包含行内子元素。
- **特性**：
  - 行内元素按基线（baseline）对齐。
  - 支持 `vertical-align` 属性调整对齐方式。
  - 行盒（line box）高度由最高行内元素决定。

---

### **3. 弹性格式化上下文（FFC, Flex Formatting Context）**
- **触发条件**：`display: flex` 或 `display: inline-flex`。
- **特性**：
  - 子元素成为弹性项目（flex item），默认水平排列。
  - 支持弹性伸缩、对齐（`justify-content`、`align-items`）等复杂布局。

---

### **4. 网格格式化上下文（GFC, Grid Formatting Context）**
- **触发条件**：`display: grid` 或 `display: inline-grid`。
- **特性**：
  - 二维布局系统，通过行和列定义单元格。
  - 支持复杂的网格对齐和项目定位（如 `grid-template`、`grid-area`）。

---

### **5. 格式化上下文的作用**
- **隔离布局**：避免不同布局规则相互干扰（如 BFC 防止浮动元素溢出）。
- **控制元素行为**：例如 BFC 解决外边距合并、清除浮动；FFC/GFC 实现现代布局。
- **提升渲染性能**：独立上下文减少浏览器重排范围。

---

### **示例：BFC 解决外边距合并**
```html
<div class="container">
  <div class="child">Child 1</div>
  <div class="child">Child 2</div>
</div>
```
若 `.container` 未触发 BFC，两个 `.child` 的垂直 margin 会合并。通过为 `.container` 添加 `overflow: hidden` 触发 BFC，可阻止合并。

---

### **总结**
格式化上下文是 CSS 布局的基石，理解其类型和触发条件能帮助开发者：
- 解决布局冲突（如浮动塌陷、外边距合并）。
- 选择最佳布局模型（BFC、Flex、Grid）。
- 优化页面渲染性能。