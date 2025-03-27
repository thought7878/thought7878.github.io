

## 1. 是什么？

关键帧动画（`@keyframes`）是 CSS 中的一种动画技术，允许开发者通过定义一组样式规则，并在指定的时间点应用这些规则，从而创建复杂的、多阶段的动画效果。与 CSS 的 `transition` 不同，关键帧动画可以精确控制动画的每个阶段，适用于需要分步骤展示或循环播放的场景。

**核心概念：**

- **@keyframes**：定义动画的关键帧。
- **animation** 属性：将关键帧动画应用到元素上。
- 支持复杂动画，如颜色渐变、路径运动、缩放等。

---

## 2. 为什么使用关键帧动画？

以下是使用关键帧动画的主要原因和优势：

### (1) 支持复杂动画

CSS 过渡（Transition）只能处理单个属性从一个值到另一个值的变化，而关键帧动画可以通过 `@keyframes` 定义多个时间点的状态，从而实现更复杂的动画效果。

**示例：颜色渐变 + 尺寸变化**

```css
@keyframes example {
  0% {
    background-color: red;
    transform: scale(1);
  }
  50% {
    background-color: blue;
    transform: scale(1.5);
  }
  100% {
    background-color: green;
    transform: scale(1);
  }
}

.box {
  width: 100px;
  height: 100px;
  animation: example 3s infinite;
}
```

---

### (2) 精确控制动画阶段

通过 `@keyframes`，你可以为动画的不同阶段设置不同的样式规则。这种灵活性使得关键帧动画非常适合需要分阶段展示的场景。

**示例：文字逐字显示**

```css
@keyframes text-reveal {
  0% {
    width: 0;
  }
  100% {
    width: 200px;
  }
}

.text-box {
  white-space: nowrap;
  overflow: hidden;
  border-right: 2px solid black;
  animation: text-reveal 2s steps(10, end);
}
```

---

### (3) 自动播放和循环

关键帧动画支持自动播放和循环功能，而无需额外的 JavaScript 控制。这对于制作加载动画、背景动画等非常有用。

**示例：旋转加载动画**

```css
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.loader {
  width: 50px;
  height: 50px;
  border: 5px solid #ccc;
  border-top-color: #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}
```

---

### (4) 性能优化

关键帧动画通常由浏览器的渲染引擎优化，特别是当使用 `transform` 和 `opacity` 属性时，动画可以在 GPU 上运行，从而提高性能。

---

### (5) 更丰富的用户体验

关键帧动画可以用来增强用户界面的交互性和吸引力。例如：

- 按钮点击后的动态反馈。
- 页面加载时的欢迎动画。
- 数据可视化中的动态图表。

---

## 3. 怎么用？

### (1) 基本语法

```css
/* 定义关键帧 */
@keyframes 动画名称 {
  关键帧百分比 {
    样式规则;
  }
}

/* 应用动画 */
选择器 {
  animation: 动画名称 持续时间 速度曲线 延迟时间 播放次数 方向 填充模式;
}
```

---

### (2) 关键帧的定义

通过 `@keyframes` 定义动画的关键帧，可以使用百分比（如 `0%`, `50%`, `100%`）或关键字（如 `from`, `to`）来表示动画的不同阶段。

**示例：简单的移动动画**

```css
@keyframes move {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(200px);
  }
}
```

---

### (3) animation 属性

将定义好的关键帧动画应用到元素上，需要使用 `animation` 属性。以下是其常用子属性：

- **animation-name**: 动画名称（与 `@keyframes` 定义的名称一致）。
- **animation-duration**: 动画持续时间（单位为秒或毫秒）。
- **animation-timing-function**: 动画的速度曲线（如 `ease`, `linear`）。
- **animation-delay**: 动画开始前的延迟时间。
- **animation-iteration-count**: 动画的播放次数（如 `infinite` 表示无限循环）。
- **animation-direction**: 动画的方向（如 `normal`, `reverse`, `alternate`）。
- **animation-fill-mode**: 动画结束后的状态（如 `forwards` 保持最终状态）。

**简写形式：**

```css
animation: 动画名称 持续时间 速度曲线 延迟时间 播放次数 方向 填充模式;
```

---

### (4) 示例代码

#### 示例 1：基础动画

```html
<div class="box"></div>
```

```css
@keyframes slide {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(200px);
  }
}

.box {
  width: 100px;
  height: 100px;
  background-color: red;
  animation: slide 2s linear infinite;
}
```

**解释：**

- 定义了一个从左到右的滑动动画。
- 动画持续时间为 2 秒，线性速度，无限循环。

---

#### 示例 2：按钮点击反馈

```html
<button class="btn">Click Me</button>
```

```css
@keyframes pulse {
  0% {
    transform: scale(1);
    box-shadow: 0 0 0 rgba(0, 0, 0, 0);
  }
  50% {
    transform: scale(1.1);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  }
  100% {
    transform: scale(1);
    box-shadow: 0 0 0 rgba(0, 0, 0, 0);
  }
}

.btn {
  padding: 10px 20px;
  font-size: 16px;
  animation: pulse 0.5s ease-in-out;
}
```

**解释：**

- 按钮点击后会产生一个“脉冲”效果。
- 动画持续时间为 0.5 秒，速度曲线为 `ease-in-out`。

---

#### 示例 3：复杂的加载动画

```html
<div class="loader"></div>
```

```css
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.loader {
  width: 50px;
  height: 50px;
  border: 5px solid #ccc;
  border-top-color: #3498db;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}
```

**解释：**

- 创建了一个无限旋转的加载动画。
- 动画持续时间为 1 秒，线性速度。

---

### (5) 注意事项

1. **可动画属性**：并非所有 CSS 属性都支持动画，常见支持的属性包括 `transform`, `opacity`, `color`, `background-color` 等。
2. **性能优化**：优先使用 `transform` 和 `opacity`，避免对昂贵的属性（如 `width`, `height`）进行频繁动画操作。
3. **浏览器兼容性**：对于旧版浏览器，可能需要添加 `-webkit-` 前缀。

---

## 总结

- **是什么？**  
  关键帧动画是一种通过 `@keyframes` 定义多阶段样式的 CSS 技术，用于创建复杂的、可循环的动画效果。

- **为什么？**

  - 支持复杂动画。
  - 精确控制动画阶段。
  - 自动播放和循环。
  - 性能优化。
  - 提升用户体验。

- **怎么用？**
  - 使用 `@keyframes` 定义动画的关键帧。
  - 使用 `animation` 属性将动画应用到元素上。
  - 注意性能优化和浏览器兼容性。

关键帧动画是现代网页设计中不可或缺的一部分，能够为用户提供更加生动、流畅的视觉体验。
