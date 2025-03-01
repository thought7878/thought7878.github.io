**固定定位（Fixed Positioning）** 是 CSS 中一种特殊的布局方式，用于将元素*相对于浏览器视口（viewport）* 进行定位。与绝对定位不同，固定定位的元素*不会随页面滚动而移动*，因此常用于创建固定的*导航栏、侧边栏、返回顶部按钮*等。

---

## **1. 固定定位的核心概念**

### **1.1 定义**
- **固定定位** 是通过 `position: fixed` 属性实现的。
- 元素会*脱离普通流*，并*相对于浏览器视口*进行定位。
- *即使页面滚动，固定定位的元素仍然**保持在视口中的指定位置***。

### **1.2 语法**
```css
position: fixed;
top: <值>;    /* 距离视口顶部的距离 */
right: <值>;  /* 距离视口右侧的距离 */
bottom: <值>; /* 距离视口底部的距离 */
left: <值>;   /* 距离视口左侧的距离 */
```
- 可以使用 `px`、`%`、`em` 等单位。
- 至少需要设置一个方向属性（如 `top`、`left` 等），否则元素会保持在原始位置。

---

## **2. 固定定位的工作机制**

### **2.1 相对于视口定位**
- 固定定位的元素始终相对于浏览器视口，而不是任何父元素。
- 示例：
```html
<div class="fixed-box">Fixed Box</div>
```

```css
.fixed-box {
  position: fixed;
  top: 10px;
  right: 10px;
  background-color: coral;
  padding: 10px;
}
```

**效果**：
- 元素距离视口右上角 10px，并始终保持在该位置，即使页面滚动也不会移动。

---

### **2.2 不受父元素影响**
- 固定定位的元素不会受到父元素的 `position` 属性影响，因为它始终相对于视口。
- 示例：
```html
<div class="parent">
  <div class="fixed-box">Fixed Box</div>
</div>
```

```css
.parent {
  position: relative;
  width: 300px;
  height: 200px;
  background-color: lightblue;
}
.fixed-box {
  position: fixed;
  top: 50px;
  left: 50px;
  background-color: coral;
}
```

**效果**：
- `.fixed-box` 不会相对于 `.parent` 定位，而是相对于视口。

---

### **2.3 堆叠顺序（Z-index）**
- 固定定位的元素可以通过 `z-index` 控制堆叠顺序。
- 数值越大，元素越靠前。
- 示例：
```css
.box1 {
  position: fixed;
  top: 10px;
  left: 10px;
  z-index: 1;
}
.box2 {
  position: fixed;
  top: 20px;
  left: 20px;
  z-index: 2;
}
```

---

## **3. 固定定位的应用场景**

### **3.1 固定导航栏**
- 使用固定定位可以创建始终可见的导航栏。
- 示例：
```html
<nav class="navbar">Navigation Bar</nav>
```

```css
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background-color: #333;
  color: white;
  padding: 10px;
  text-align: center;
}
```

**效果**：
- 导航栏始终固定在页面顶部，即使页面滚动也不会消失。

---

### **3.2 返回顶部按钮**
- 使用固定定位可以创建一个始终可见的“返回顶部”按钮。
- 示例：
```html
<button class="back-to-top">Back to Top</button>
```

```css
.back-to-top {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background-color: black;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
}
```

**效果**：
- 按钮始终固定在页面右下角，方便用户快速返回顶部。

---

### **3.3 侧边栏或工具栏**
- 使用固定定位可以创建一个固定的侧边栏或工具栏。
- 示例：
```html
<div class="sidebar">Sidebar Content</div>
```

```css
.sidebar {
  position: fixed;
  top: 0;
  left: 0;
  width: 200px;
  height: 100%;
  background-color: #f4f4f4;
  padding: 20px;
}
```

**效果**：
- 侧边栏始终固定在页面左侧，内容不会随页面滚动而移动。

---

## **4. 注意事项与局限性**

### **4.1 脱离普通流**
- 固定定位的元素会脱离普通流，因此不会影响其他元素的布局。
- 示例：
```html
<div class="box1">Box 1</div>
<div class="box2">Box 2</div>
```

```css
.box1 {
  position: fixed;
  top: 0;
  left: 0;
}
.box2 {
  background-color: lightgreen;
}
```

**效果**：
- `Box 1` 脱离普通流，`Box 2` 会占据 `Box 1` 的位置。

---

### **4.2 需要明确的尺寸**
- 固定定位的元素通常*需要明确设置宽度和高度*，否则可能无法正确显示。

---

### **4.3 不适合动态内容**
- 如果页面中有动态内容（如弹出框），可能会遮挡固定定位的元素，需注意层级关系。

---

### **4.4 移动端兼容性**
- 在某些移动端浏览器中，固定定位*可能会导致性能问题或行为异常（如抖动）*。建议测试并优化。

---

## **5. 总结**
- **固定定位** 是一种强大的布局工具，适用于需要始终可见的元素（如导航栏、返回顶部按钮）。
- 它始终相对于视口定位，不受页面滚动的影响。
- 尽管功能强大，但在复杂布局中，需注意与其他元素的交互和层级关系。
