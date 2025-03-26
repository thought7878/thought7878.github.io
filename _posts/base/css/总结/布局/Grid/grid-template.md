`grid-template` 是 CSS 网格布局（Grid Layout）中的一个简写属性，用于一次性定义网格的列、行和命名区域。它将 `grid-template-rows`、`grid-template-columns` 和 `grid-template-areas` 组合在一起，从而简化代码并提高可读性。

---

### 1. `grid-template` 的基本概念

#### 作用
- **组合功能**：`grid-template` 允许同时定义：
  - 列宽（`grid-template-columns`）。
  - 行高（`grid-template-rows`）。
  - 命名区域（`grid-template-areas`）。
- **适用范围**：该属性作用于整个网格容器。

#### 语法
```css
grid-template: <grid-template-rows> / <grid-template-columns>;

grid-template: <grid-template-areas> <grid-template-rows> / <grid-template-columns>;
```

- 如果只提供 `<grid-template-rows>` 和 `<grid-template-columns`，则不会定义命名区域。
- 如果结合 `<grid-template-areas>` 使用，则可以同时定义命名区域、行高和列宽。

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `grid-template` 创建网格布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grid Template Example</title>
    <style>
        .container {
            display: grid;
            grid-template:
                "header header header" auto
                "sidebar main main" 1fr
                "footer footer footer" auto
                / 1fr 2fr 1fr; /* 定义三列 */
            gap: 10px; /* 行间距和列间距 */
            border: 1px solid #ccc;
            height: 400px;
        }
        .item-header {
            grid-area: header;
            background-color: lightcoral;
        }
        .item-sidebar {
            grid-area: sidebar;
            background-color: lightblue;
        }
        .item-main {
            grid-area: main;
            background-color: lightgreen;
        }
        .item-footer {
            grid-area: footer;
            background-color: lightgray;
        }
        .item {
            text-align: center;
            padding: 20px;
            border: 1px solid #000;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item-header">Header</div>
        <div class="item item-sidebar">Sidebar</div>
        <div class="item item-main">Main Content</div>
        <div class="item item-footer">Footer</div>
    </div>
</body>
</html>
```

---

### 3. 不同用法详解

#### 3.1 只定义行和列
如果不涉及命名区域，可以直接定义行和列。

```css
.container {
    grid-template: auto 1fr auto / 1fr 2fr 1fr;
}
```

等价于：
```css
.container {
    grid-template-rows: auto 1fr auto;
    grid-template-columns: 1fr 2fr 1fr;
}
```

效果：
- 定义了三行（高度分别为 `auto`、`1fr` 和 `auto`）。
- 定义了三列（宽度比例为 `1:2:1`）。

---

#### 3.2 定义命名区域
可以通过 `grid-template` 同时定义命名区域、行高和列宽。

```css
.container {
    grid-template:
        "header header header" auto
        "sidebar main main" 1fr
        "footer footer footer" auto
        / 1fr 2fr 1fr;
}
```

效果：
- 第一行命名为 `header`，跨越所有列，高度为 `auto`。
- 第二行分为 `sidebar` 和 `main` 区域，高度为 `1fr`。
- 第三行命名为 `footer`，跨越所有列，高度为 `auto`。
- 定义了三列（宽度比例为 `1:2:1`）。

---

#### 3.3 动态调整
可以结合 `minmax()` 和 `repeat()` 函数实现更灵活的布局。

```css
.container {
    grid-template:
        "header header header" minmax(50px, auto)
        "sidebar main main" 1fr
        "footer footer footer" minmax(50px, auto)
        / repeat(3, 1fr);
}
```

效果：
- 第一行和第三行的高度在 `50px` 和 `auto` 之间动态调整。
- 定义了三列，每列宽度相等。

---

### 4. 注意事项

1. **默认值**：
   - 如果未显式设置 `grid-template`，则默认值为 `none`，表示没有定义网格结构。

2. **与单独属性的关系**：
   - `grid-template` 是 `grid-template-rows`、`grid-template-columns` 和 `grid-template-areas` 的组合。
   - 如果需要更精细的控制，可以单独使用这些属性。

3. **兼容性**：
   - `grid-template` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 5. 示例分析

假设网格容器有 3 列 3 行，以下是不同 `grid-template` 的效果：

| **CSS 代码**                                                                 | **效果描述**                                                                 |
|------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `grid-template: auto 1fr auto / 1fr 2fr 1fr;`                               | 定义三行（高度为 `auto`、`1fr` 和 `auto`），三列（宽度比例为 `1:2:1`）。       |
| `grid-template: "header header header" auto "sidebar main main" 1fr "footer footer footer" auto / 1fr 2fr 1fr;` | 定义三行三列，并分配命名区域（如 `header`、`sidebar`、`main` 和 `footer`）。 |

---

### 6. 总结

- **核心功能**：
  - `grid-template` 同时定义网格的行、列和命名区域。
- **常用技巧**：
  - 结合命名区域创建语义化布局。
  - 使用简写形式简化代码。
- **最佳实践**：
  - 使用 `grid-template` 快速定义网格结构。
  - 对于复杂的布局，单独使用 `grid-template-rows`、`grid-template-columns` 和 `grid-template-areas` 更灵活。

通过合理使用 `grid-template`，您可以轻松实现灵活且美观的网格布局！