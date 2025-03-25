`fr` 是 CSS 网格布局（Grid Layout）中的一种新型单位，表示“**fraction**”（分数）。它用于分配网格容器中的可用空间，是一种非常灵活且强大的方式。`fr` 单位的**核心思想**是将剩余的可用空间按比例分配给网格的列或行。

### 为什么使用fr

不同于设置`百分比`，**使用`fr`再设置`gap`并不会溢出容器**，**全部使用`百分比`设置网格宽度容易导致溢出容器。**

---

### 1. `fr` 单位的基本概念

- **定义**：`fr` 表示一个分数单位，用于*分配网格容器中未被占用的剩余空间*。
- **作用**：与传统的固定单位（如 `px` 或 `%`）不同，`fr` 允许动态调整网格项目的大小，使布局更加灵活和响应式。
- **计算方式**：
  - *计算公式*：网格宽度 = fr数 * (剩余空间 / 总fr数量)
  - 浏览器会*先扣除所有固定尺寸*（如 `px`、`%`）和内容所需的最小空间。
  - *剩余的空间按 `fr` 的比例进行分配*。

---

### 2. 使用场景

#### 2.1 分配剩余空间
`fr` 单位非常适合分配剩余空间。例如：

```css
.container {
    display: grid;
    grid-template-columns: 1fr 2fr 1fr; /* 第一列占 1 份，第二列占 2 份，第三列占 1 份 */
}
```

在这个例子中：
- 总共有 4 份（1 + 2 + 1）。
- 第二列的宽度是第一列和第三列的两倍。

---

#### 2.2 结合固定单位
`fr` 可以与其他单位（如 `px` 或 `%`）结合使用，创建混合布局。例如：

```css
.container {
    display: grid;
    grid-template-columns: 100px 1fr 2fr; /* 第一列固定 100px，第二列占 1 份，第三列占 2 份 */
}
```

在这个例子中：
- 第一列固定为 100px。
- 剩余空间按 1:2 的比例分配给第二列和第三列。

---

#### 2.3 动态响应式布局
`fr` 单位*非常适合响应式设计*，因为它可以根据容器的大小动态调整列或行的宽度。例如：

```css
.container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr)); /* 动态填充列 */
}
```

在这个例子中：
- 每列的宽度至少为 100px，最多为 1 份剩余空间。
- 浏览器会根据容器的大小自动调整列数。

---

### 3. 示例代码

以下是一个完整的示例，展示如何使用 `fr` 单位创建灵活的网格布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FR Unit Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: 1fr 2fr 1fr; /* 三列，比例为 1:2:1 */
            gap: 20px; /* 行间距和列间距 */
            height: 300px;
            border: 1px solid #ccc;
        }
        .item {
            background-color: lightblue;
            text-align: center;
            padding: 20px;
            border: 1px solid #000;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">Item 1</div>
        <div class="item">Item 2</div>
        <div class="item">Item 3</div>
    </div>
</body>
</html>
```

---

### 4. 运行结果分析

- **布局结构**：
  - 容器被分为三列，宽度比例为 1:2:1。
  - 第二列的宽度是第一列和第三列的两倍。
- **动态调整**：
  - 如果容器的宽度发生变化，列的宽度会按比例自动调整。

---

### 5. 高级用法

#### 5.1 结合 `minmax()`
`minmax()` 函数可以限制列或行的最小和最大尺寸，同时结合 `fr` 实现更灵活的布局。

```css
.container {
    display: grid;
    grid-template-columns: minmax(100px, 1fr) 2fr; /* 第一列最小 100px，最大 1fr；第二列占 2 份 */
}
```

#### 5.2 动态填充列
使用 `repeat()` 和 `auto-fill` 或 `auto-fit` 动态填充列。

```css
.container {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(100px, 1fr)); /* 动态填充列 */
}
```

---

### 6. 注意事项

1. **默认值**：
   - 如果未定义 `grid-template-columns` 或 `grid-template-rows`，则不会使用 `fr` 单位。

2. **与其他单位的优先级**：
   - 固定单位（如 `px` 或 `%`）会先占用空间，剩余空间才会按 `fr` 分配。

3. **兼容性**：
   - `fr` 单位在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 7. 总结

- **核心特点**：
  - `fr` 是一种灵活的单位，用于分配剩余空间。
  - 可以与其他单位（如 `px`、`%`）结合使用，实现复杂的布局。
- **主要优势**：
  - 提供动态调整的能力，适合响应式设计。
  - 简化了复杂布局的实现。
- **最佳实践**：
  - 使用 `fr` 创建灵活的比例布局。
  - 结合 `minmax()` 和 `repeat()` 实现动态响应式布局。
