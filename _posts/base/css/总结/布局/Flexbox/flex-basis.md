`flex-basis` 是 CSS 弹性布局（Flexbox）中的一个重要属性，用于定义弹性项目*在主轴上的初始大小*。它是 `flex` 属性的一部分，*与 `flex-grow` 和 `flex-shrink` 一起决定了弹性项目的最终尺寸*。

---

### 1. `flex-basis` 的作用

`flex-basis` 定义了弹性*项目在**主轴**上的初始大小*。它类似于 `width` 或 `height`（取决于主轴的方向），但在 Flexbox 中具有更高的优先级。

#### 主要用途
- **指定弹性项目的初始大小**：可以设置为固定值（如 `100px`）、百分比（如 `50%`）或关键字（如 `auto`）。
- **替代 `width` 或 `height`**：在 Flexbox 中，`flex-basis` 通常*会覆盖 `width` 或 `height` 的值*。
- **动态调整的基础**：结合 `flex-grow` 和 `flex-shrink`，决定弹性项目的扩展和收缩行为。

---

### 2. 取值范围

| **取值**         | **含义**                                       |
| -------------- | -------------------------------------------- |
| `auto`         | *默认值*。根据内容的固有尺寸或 `width`/`height` 的值来决定初始大小。 |
| `<length>`     | 固定长度值，如 `100px`、`20em` 等。                    |
| `<percentage>` | 相对于父容器主轴方向的尺寸的百分比，如 `50%`。                   |
| `content`      | 根据内容的固有尺寸自动调整大小（较新的取值）。                      |

---

### 3. 与 `width` 和 `height` 的关系

#### 主轴方向
  - 如果主轴是水平方向（默认情况），`flex-basis` 会*影响宽度*。
  - 如果主轴是垂直方向，`flex-basis` 会*影响高度*。
#### **优先级**
  - 在 Flexbox 中，*`flex-basis` 的优先级高于 `width` 和 `height`*。
  - 如果同时设置了 `flex-basis` 和 `width`，*`flex-basis` 会覆盖 `width`*。

---

### 4. 示例代码

以下示例展示了 `flex-basis` 的使用及其与其他属性的关系：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flex-basis Example</title>
    <style>
        .container {
            display: flex;
            border: 1px solid #ccc;
        }
        .item {
            background-color: lightblue;
            margin: 10px;
            padding: 20px;
            text-align: center;
        }
        .item1 {
            flex-basis: 100px; /* 初始宽度为 100px */
        }
        .item2 {
            flex-basis: auto; /* 根据内容的固有尺寸决定宽度 */
        }
        .item3 {
            flex-basis: 50%; /* 占据父容器主轴方向的 50% */
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">Item 1 (100px)</div>
        <div class="item item2">Item 2 (Auto)</div>
        <div class="item item3">Item 3 (50%)</div>
    </div>
</body>
</html>
```

---

### 5. 动态调整（结合 `flex-grow` 和 `flex-shrink`）

`flex-basis` 是弹性项目动态调整的基础。当容器空间不足或多余时，`flex-grow` 和 `flex-shrink` 决定了项目的扩展和收缩行为。

**示例代码：**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flex-basis with Grow and Shrink</title>
    <style>
        .container {
            display: flex;
            border: 1px solid #ccc;
        }
        .item {
            background-color: lightblue;
            margin: 10px;
            padding: 20px;
            text-align: center;
        }
        .item1 {
            flex: 1 1 100px; /* 初始宽度 100px，允许扩展和收缩 */
        }
        .item2 {
            flex: 0 1 auto; /* 初始大小为内容尺寸，不允许扩展，但允许收缩 */
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">Item 1 (Flex Basis 100px)</div>
        <div class="item item2">Item 2 (Auto)</div>
    </div>
</body>
</html>
```

---

### 6. 注意事项

1. **默认值为 `auto`**：
   - 如果未显式设置 `flex-basis`，则默认值为 `auto`，表示根据内容的固有尺寸或 `width`/`height` 的值决定初始大小。

2. **与 `flex` 的关系**：
   - `flex` 是 `flex-grow`、`flex-shrink` 和 `flex-basis` 的简写形式，默认值为 `0 1 auto`。
   - 示例：
     ```css
     .item {
         flex: 1; /* 等价于 flex: 1 1 0 */
     }
     ```

3. **兼容性**：
   - `flex-basis` 在现代浏览器中广泛支持，但在一些老旧浏览器中可能需要使用前缀或回退方案。

---

### 7. 总结

- **核心作用**：`flex-basis` 定义了弹性项目在主轴上的初始大小。
- **优先级**：在 Flexbox 中，`flex-basis` 的优先级高于 `width` 和 `height`。
- **动态调整**：结合 `flex-grow` 和 `flex-shrink`，可以实现灵活的扩展和收缩行为。
- **最佳实践**：
  - 使用 `flex-basis` 来明确弹性项目的初始大小。
  - 避免过度依赖 `width` 或 `height`，以确保布局更加灵活。

通过合理使用 `flex-basis`，您可以更精确地控制弹性项目的尺寸，从而构建出更加灵活和响应式的布局！