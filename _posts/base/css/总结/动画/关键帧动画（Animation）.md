CSS 的 **关键帧动画（Animation）** 是一种强大的工具，用于*创建复杂的、多阶段的*动画效果。与简单的 CSS 过渡不同，关键帧动画允许开发者定义多个时间点的状态，并通过这些状态之间的平滑过渡来实现动态效果。

---

### 1. 关键帧动画的基本概念

#### 作用
- **复杂动画**：关键帧动画可以定义多个状态（关键帧），并让元素在这些状态之间动态变化。
- **循环和控制**：支持循环播放、延迟启动、反向播放等高级功能。

#### 核心组成部分
要实现关键帧动画，需要以下两个核心部分：
1. **`@keyframes` 规则**：*定义动画的关键帧（即每个时间点的状态）*。
2. **`animation` 属性**：*将动画应用到元素上*，并设置动画的持续时间、速度曲线、延迟等参数。

---

### 2. 语法

#### 2.1 `@keyframes` 规则
```css
@keyframes <animation-name> {
    from { /* 初始状态 */ }
    to { /* 结束状态 */ }
}

/* 或者使用百分比定义多个关键帧 */
@keyframes <animation-name> {
    0% { /* 初始状态 */ }
    50% { /* 中间状态 */ }
    100% { /* 结束状态 */ }
}
```

- **`from` 和 `to`**：分别表示动画的起始和结束状态。
- **百分比**：可以定义任意数量的关键帧，精确控制动画的每一阶段。

---

#### 2.2 `animation` 属性
```css
/* 单独设置 */
animation-name: <animation-name>;
animation-duration: <time>;
animation-timing-function: <timing-function>;
animation-delay: <time>;
animation-iteration-count: <number>|infinite;
animation-direction: normal|reverse|alternate|alternate-reverse;
animation-fill-mode: none|forwards|backwards|both;
animation-play-state: running|paused;

/* 简写形式 */
animation: <name> <duration> <timing-function> <delay> <iteration-count> <direction> <fill-mode> <play-state>;
```

---

### 3. 使用方法

以下是一个完整的示例，展示如何使用关键帧动画：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS Animation Example</title>
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: lightblue;
            position: relative;
            animation: move-and-change 4s ease-in-out infinite alternate;
        }

        @keyframes move-and-change {
            0% {
                transform: translateX(0);
                background-color: lightblue;
                border-radius: 0;
            }
            50% {
                transform: translateX(200px);
                background-color: coral;
                border-radius: 50%;
            }
            100% {
                transform: translateX(0) rotate(360deg);
                background-color: lightgreen;
                border-radius: 0;
            }
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

---

### 4. 核心属性详解

#### 4.1 `@keyframes`
- 定义动画的关键帧，描述动画在每个时间点的状态。
- 示例：
  ```css
  @keyframes example {
      0% { opacity: 0; }
      50% { opacity: 1; transform: scale(1.5); }
      100% { opacity: 0; transform: scale(1); }
  }
  ```

#### 4.2 `animation-name`
- 指定动画的名称，必须与 `@keyframes` 定义的名称一致。

#### 4.3 `animation-duration`
- 定义动画的持续时间。
- 示例：`4s` 表示动画持续 4 秒。

#### 4.4 `animation-timing-function`
- 定义动画的速度曲线。
- 常见取值：
  - `ease`（默认值）：慢速开始和结束。
  - `linear`：匀速变化。
  - `ease-in`：慢速开始。
  - `ease-out`：慢速结束。
  - `cubic-bezier(n1, n2, n3, n4)`：自定义贝塞尔曲线。

#### 4.5 `animation-delay`
- 定义动画开始前的延迟时间。
- 示例：`0.5s` 表示延迟 0.5 秒后开始。

#### 4.6 `animation-iteration-count`
- 定义动画的播放次数。
- 常见取值：
  - 数字：如 `3` 表示播放 3 次。
  - `infinite`：无限循环播放。

#### 4.7 `animation-direction`
- 定义动画的播放方向。
- 常见取值：
  - `normal`（默认值）：正向播放。
  - `reverse`：反向播放。
  - `alternate`：正向和反向交替播放。
  - `alternate-reverse`：反向和正向交替播放。

#### 4.8 `animation-fill-mode`
- 定义动画结束后的样式。
- 常见取值：
  - `none`：动画结束后恢复初始状态。
  - `forwards`：动画结束后保持最后一帧的样式。
  - `backwards`：动画开始前应用第一帧的样式。
  - `both`：结合 `forwards` 和 `backwards`。

#### 4.9 `animation-play-state`
- 控制动画的播放状态。
- 常见取值：
  - `running`（默认值）：动画正在播放。
  - `paused`：暂停动画。

---

### 5. 注意事项

1. **性能优化**：
   - 尽量避免对昂贵的属性（如 `width` 和 `height`）进行动画处理。
   - 推荐使用 `transform` 和 `opacity`，因为它们由 GPU 加速，性能更高。

2. **兼容性**：
   - 关键帧动画在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

3. **命名规范**：
   - 动画名称应具有语义化，避免与其他样式冲突。

---

### 6. 示例分析

#### 示例 1：加载动画
```css
.loader {
    width: 50px;
    height: 50px;
    background-color: coral;
    animation: spin 2s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
```
效果：
- 元素会以 2 秒为周期旋转 360 度，形成一个加载动画。

---

#### 示例 2：按钮点击效果
```css
.button {
    background-color: lightblue;
    padding: 10px 20px;
    border: none;
    color: white;
    animation: pulse 1s ease-in-out infinite;
}

@keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.1); }
    100% { transform: scale(1); }
}
```
效果：
- 按钮会以 1 秒为周期放大和缩小，形成脉冲效果。

---

#### 示例 3：渐显文字
```css
.text {
    opacity: 0;
    animation: fadeIn 2s ease-in-out forwards;
}

@keyframes fadeIn {
    0% { opacity: 0; }
    100% { opacity: 1; }
}
```
效果：
- 文字会在 2 秒内逐渐显现出来，并保持最终状态。

---

### 7. 总结

- **核心功能**：
  - 关键帧动画允许定义多个状态，并在这些状态之间平滑过渡。
- **常用技巧**：
  - 使用 `transform` 和 `opacity` 提升性能。
  - 结合 `animation-iteration-count` 实现循环效果。
- **最佳实践**：
  - 明确命名动画，保持代码可读性。
  - 避免滥用动画，确保用户体验流畅。

通过合理使用关键帧动画，您可以轻松实现复杂且动态的视觉效果！