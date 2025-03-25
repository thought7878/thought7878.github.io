`grid-template-columns` 和 `grid-template-rows` 是 CSS 网格布局（Grid Layout）中两个核心属性，用于*定义网格容器的列和行的大小*。它们允许开发者精确控制网格的结构和尺寸，是实现复杂布局的基础。

---

### 1. `grid-template-columns`

#### 作用
`grid-template-columns` 定义了*网格的列数和每列的宽度*。通过设置该属性，可以创建一个具有固定或动态宽度的多列布局。

#### 语法
```css
grid-template-columns: <track-size> ...;
```

- `<track-size>`：表示列的宽度，可以是固定值（如 `px`）、比例值（如 `fr`）、百分比、`auto` 或 `minmax()` 等。

#### 常用取值

| **取值**               | **含义**                                        |
| -------------------- | --------------------------------------------- |
| `100px`              | 固定宽度为 100px 的列。                               |
| `20%`                | 列宽占*父容器宽度*的 20%。                              |
| `1fr`                | 使用分数单位（fraction），表示可用空间的比例。                   |
| `auto`               | 列宽*根据内容自动调整*。                                 |
| `minmax(100px, 1fr)` | 列宽在 100px 和 1fr 之间动态调整。                       |
| `repeat(3, 1fr)`     | 使用 `repeat()` 函数*重复定义列*，这里表示创建 3 列，每列宽度为 1fr。 |

**示例：**
```css
.container {
    display: grid;
    grid-template-columns: 100px 200px auto; /* 第一列 100px，第二列 200px，第三列自动 */
}
```

---

### 2. `grid-template-rows`

#### 作用
`grid-template-rows` 定义了*网格的行数和每行的高度*。与 `grid-template-columns` 类似，它允许开发者控制网格的垂直方向布局。

#### 语法
```css
grid-template-rows: <track-size> ...;
```

- `<track-size>`：表示行的高度，可以是固定值（如 `px`）、比例值（如 `fr`）、百分比、`auto` 或 `minmax()` 等。

#### 常用取值
参考：[[fr单位]]

| **取值**         | **含义**                                                                 |
|------------------|-------------------------------------------------------------------------|
| `50px`           | 固定高度为 50px 的行。                                                   |
| `1fr`            | 使用分数单位（fraction），表示可用空间的比例。                             |
| `auto`           | 行高根据内容自动调整。                                                   |
| `minmax(50px, 1fr)` | 行高在 50px 和 1fr 之间动态调整。                                      |
| `repeat(2, 100px)` | 使用 `repeat()` 函数重复定义行，这里表示创建 2 行，每行高度为 100px。      |

**示例：**
```css
.container {
    display: grid;
    grid-template-rows: 50px auto 100px; /* 第一行 50px，第二行自动，第三行 100px */
}
```

---

### 3. 组合使用 `grid-template-columns` 和 `grid-template-rows`

**示例代码：**
以下是一个完整的示例，展示如何同时使用 `grid-template-columns` 和 `grid-template-rows` 创建一个简单的网格布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS Grid Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: 1fr 2fr 1fr; /* 三列，比例为 1:2:1 */
            grid-template-rows: 100px auto 50px; /* 三行，高度分别为 100px、自动、50px */
            gap: 10px; /* 行间距和列间距 */
            height: 100vh;
        }
        .item {
            background-color: lightblue;
            text-align: center;
            padding: 20px;
            border: 1px solid #ccc;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">Item 1</div>
        <div class="item">Item 2</div>
        <div class="item">Item 3</div>
        <div class="item">Item 4</div>
        <div class="item">Item 5</div>
        <div class="item">Item 6</div>
    </div>
</body>
</html>
```

---

### 4. 高级用法

#### 4.1 使用 `repeat()` 函数
`repeat()` 函数可以*简化重复的列或行定义*。

```css
.container {
    grid-template-columns: repeat(4, 1fr); /* 创建 4 列，每列宽度为 1fr */
    grid-template-rows: repeat(3, 100px); /* 创建 3 行，每行高度为 100px */
}
```

#### 4.2 使用 `minmax()` 函数
`minmax()` 函数可以定义一个范围，使列或行的大小*在最小值和最大值之间动态调整*。

```css
.container {
    grid-template-columns: minmax(100px, 1fr) 200px; /* 第一列最小 100px，最大 1fr；第二列固定 200px */
}
```

#### 4.3 使用 `auto-fill` 和 `auto-fit`
`auto-fill` 和 `auto-fit` 可以动态填充列或行，*适合响应式设计*。

- `auto-fill`：尽可能多地填充列或行，即使某些列为空。
- `auto-fit`：类似于 `auto-fill`，但会拉伸剩余空间。

```css
.container {
    grid-template-columns: repeat(auto-fill, minmax(100px, 1fr)); /* 动态填充列 */
}
```

#### 4.4 命名 grid line
- 命名网格线。请注意名称的括号语法：

```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

![](assets/排版-Grid-API/2023-09-19-14-06-03-image.png)![Grid with user named lines](https://css-tricks.com/wp-content/uploads/2018/11/template-column-rows-02.svg)

- 网格线可以*有多个名称*。例如，这里第二行将有两个名称：row1-end 和 row2-start：

```css
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```


---

### 5. 注意事项

1. **默认值**：
   - 如果未定义 `grid-template-columns` 或 `grid-template-rows`，则网格容器不会显示任何列或行。

2. **隐式网格轨道**：
   - 如果网格项目超出了显式定义的行列范围，浏览器会创建隐式网格轨道，默认大小为 `auto`。可以通过 `grid-auto-columns` 和 `grid-auto-rows` 自定义隐式轨道的大小。

3. **兼容性**：
   - 现代浏览器普遍支持这些属性，但在老旧浏览器中可能需要回退方案。

---

### 6. 总结

- **`grid-template-columns`**：定义网格的列数和每列的宽度。
- **`grid-template-rows`**：定义网格的行数和每行的高度。
- **核心功能**：
  - 支持固定值、比例值、百分比、`auto` 和动态函数（如 `minmax()` 和 `repeat()`）。
  - 提供强大的灵活性，适合构建复杂的二维布局。
- **最佳实践**：
  - 使用 `fr` 单位实现灵活的比例分配。
  - 结合 `minmax()` 和 `repeat()` 实现动态响应式布局。
