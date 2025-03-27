
## 1. 是什么？
CSS 的 `transform` 属性是一种用于**对元素进行几何变换**的技术，包括*平移（translate）、缩放（scale）、旋转（rotate）、倾斜（skew）*。通过 `transform`，可以动态改变元素的*位置、大小、角度、形状*，**而不会影响文档流中的其他元素**。

**核心概念：**
- **2D 变换**：在二维平面内操作，如平移、缩放、旋转和倾斜。
- **3D 变换**：在三维空间内操作，如沿 Z 轴旋转或位移。
- 不会触发重排（reflow），通常由 GPU 加速，性能较高。

---

## 2. 为什么使用变换？

以下是使用 CSS 变换的主要原因和优势：

### (1) 高性能的动画基础
`transform` 是一种高性能的属性，因为*它不会触发布局重新计算（重排），也不会引发重绘（repaint）*。现代浏览器会对 `transform` 进行 *GPU 加速*，从而提升动画流畅度。

**示例：平滑移动**
```css
.box {
  transform: translateX(100px);
  transition: transform 0.5s ease;
}
```

---

### (2) 灵活的视觉效果
通过 `transform`，*可以轻松实现各种视觉效果*，例如：
- 元素的放大或缩小。
- 图片的旋转或倾斜。
- 3D 效果（如翻转卡片、立方体等）。

**示例：3D 卡片翻转**
```css
.card {
  width: 200px;
  height: 200px;
  transform-style: preserve-3d;
  transition: transform 1s;
}

.card:hover {
  transform: rotateY(180deg);
}
```

---

### (3) 不影响布局
与直接修改 `position` 或 `margin` 等属性不同，`transform` *不会影响文档流中的其他元素*。这意味着*即使一个元素发生了变换，周围的元素也不会重新排列*。

**示例：不影响布局的移动**
```css
.box {
  transform: translateY(50px);
}
```
在这个例子中，`.box` 向下移动了 50 像素，但其他元素的位置保持不变。

---

### (4) 支持复杂交互
`transform` 可以*与其他 CSS 功能（如 `transition` 和 `@keyframes`）结合*，创建复杂的交互效果，例如按钮悬停效果、模态框弹出动画等。

**示例：按钮悬停放大**
```css
button {
  transform: scale(1);
  transition: transform 0.3s ease;
}

button:hover {
  transform: scale(1.1);
}
```

---

## 3. 怎么用？

### (1) 基本语法
```css
选择器 {
  transform: 函数1(参数) 函数2(参数) ...;
}
```

`transform` 属性*支持多个**函数**组合*使用，常见的函数包括：
- **平移**：`translate()`, `translateX()`, `translateY()`, `translateZ()`, `translate3d()`
- **缩放**：`scale()`, `scaleX()`, `scaleY()`, `scaleZ()`, `scale3d()`
- **旋转**：`rotate()`, `rotateX()`, `rotateY()`, `rotateZ()`, `rotate3d()`
- **倾斜**：`skew()`, `skewX()`, `skewY()`
- 矩阵变换：`matrix()`、`matrix3d()`

---

### (2) 2D 变换

#### (a) 平移（Translate）
将元素沿 X 轴或 Y 轴移动指定的距离。
- **常用函数**：
  - `translateX(tx)`：沿 X 轴移动。
  - `translateY(ty)`：沿 Y 轴移动。
  - `translate(tx, ty)`：同时沿 X 轴和 Y 轴移动。
  - `translateZ(tz)`：沿 Z 轴移动（仅适用于 3D 变换）。
- **示例**：
```css
.box {
  transform: translateX(50px); /* 水平移动 50px */
  transform: translateY(50px); /* 垂直移动 50px */
  transform: translate(50px, 50px); /* 同时水平和垂直移动 */
}
```

#### (b) 缩放（Scale）
改变元素的大小。
- **常用函数**：
  - `scaleX(sx)`：沿 X 轴缩放。
  - `scaleY(sy)`：沿 Y 轴缩放。
  - `scale(s)`：均匀缩放。
  - `scale(sx, sy)`：分别沿 X 轴和 Y 轴缩放。
- **示例**：
```css
.box {
  transform: scaleX(2); /* 水平方向放大 2 倍 */
  transform: scaleY(0.5); /* 垂直方向缩小为一半 */
  transform: scale(1.5); /* 同时水平和垂直放大 1.5 倍 */
}
```

#### (c) 旋转（Rotate）
让元素绕中心点旋转指定的角度。
- **常用函数**：
  - `rotate(angle)`：围绕 Z 轴旋转（默认为 2D）。
  - `rotateX(angle)`：围绕 X 轴旋转（3D）。
  - `rotateY(angle)`：围绕 Y 轴旋转（3D）。
  - `rotateZ(angle)`：围绕 Z 轴旋转（明确指定）。
- **示例**：
```css
.box {
  transform: rotate(45deg); /* 顺时针旋转 45 度 */
}
```

#### (d) 倾斜（Skew）
让元素沿 X 轴或 Y 轴倾斜指定的角度。
- **常用函数**：
  - `skewX(angle)`：沿 X 轴倾斜。
  - `skewY(angle)`：沿 Y 轴倾斜。
  - `skew(ax, ay)`：同时沿 X 轴和 Y 轴倾斜。
- **示例**：
```css
.box {
  transform: skewX(20deg); /* 沿 X 轴倾斜 20 度 */
  transform: skewY(10deg); /* 沿 Y 轴倾斜 10 度 */
  transform: skew(20deg, 10deg); /* 同时沿 X 和 Y 轴倾斜 */
}
```


#### (e) 矩阵变换（Matrix）
通过矩阵表示复杂的变换。
- **常用函数**：
  - `matrix(a, b, c, d, tx, ty)`：定义一个 2D 变换矩阵。
  - `matrix3d(...)`：定义一个 3D 变换矩阵。
- **示例**：
  ```css
  transform: matrix(1, -0.3, 0, 1, 0, 0); /* 自定义 2D 变换 */
  ```

---

### (3) 3D 变换

#### (a) 平移（Translate）
在三维空间中移动元素。
```css
.box {
  transform: translateZ(100px); /* 沿 Z 轴移动 100px */
  transform: translate3d(50px, 50px, 100px); /* 在 X、Y、Z 轴上同时移动 */
}
```

#### (b) 旋转（Rotate）
在三维空间中旋转元素。
```css
.box {
  transform: rotateX(45deg); /* 绕 X 轴旋转 45 度 */
  transform: rotateY(90deg); /* 绕 Y 轴旋转 90 度 */
  transform: rotateZ(180deg); /* 绕 Z 轴旋转 180 度 */
  transform: rotate3d(1, 1, 1, 45deg); /* 绕向量 (1, 1, 1) 旋转 45 度 */
}
```

#### (c) 透视（Perspective）
为 3D 变换添加深度效果。
```css
.container {
  perspective: 1000px; /* 设置透视距离 */
}

.box {
  transform: rotateY(45deg);
}
```

---

### (4) 示例代码

#### 示例 1：按钮悬停放大
```html
<button class="btn">Hover Me</button>
```

```css
.btn {
  padding: 10px 20px;
  font-size: 16px;
  transform: scale(1);
  transition: transform 0.3s ease;
}

.btn:hover {
  transform: scale(1.1);
}
```

**解释：**
- 按钮在悬停时会放大 10%。

---

#### 示例 2：3D 卡片翻转
```html
<div class="card">
  <div class="front">Front</div>
  <div class="back">Back</div>
</div>
```

```css
.card {
  width: 200px;
  height: 200px;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 1s;
}

.card:hover {
  transform: rotateY(180deg);
}

.front, .back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.back {
  transform: rotateY(180deg);
}
```

**解释：**
- 创建了一个 3D 卡片翻转效果，鼠标悬停时卡片翻转 180 度。

---

#### 示例 3：加载动画
```html
<div class="loader"></div>
```

```css
.loader {
  width: 50px;
  height: 50px;
  border: 5px solid #ccc;
  border-top-color: #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
```

**解释：**
- 创建了一个无限旋转的加载动画。

---

### (5) 注意事项
- **坐标系**：
   - 默认情况下，变换以元素的中心点为原点。
   - 可以通过 `transform-origin` 属性更改变换的原点。
- **3D 变换**：
   - 使用 3D 变换时，需要确保父容器启用了 3D 渲染上下文：
     ```css
     transform-style: preserve-3d;
     ```

- **GPU 加速**：尽量使用 `transform` 和 `opacity`，避免直接修改 `width`, `height`, `top`, `left` 等会*触发重排*的属性。
- **单位**：平移和旋转的角度需要明确指定单位（如 `px`, `deg`）。
- **兼容性**：对于旧版浏览器，可能需要添加 `-webkit-` 前缀。

---

## 总结

- **是什么？**  
  `transform` 是一种用于对元素进行几何变换的 CSS 属性，包括平移、缩放、旋转和倾斜。

- **为什么？**  
  - 高性能的动画基础。
  - 灵活的视觉效果。
  - 不影响布局。
  - 支持复杂交互。

- **怎么用？**  
  - 使用 `transform` 属性定义变换函数。
  - 支持 2D 和 3D 变换。
  - 结合 `transition` 和 `@keyframes` 创建动画效果。
