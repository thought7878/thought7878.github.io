`grid-template-areas` 是 CSS 网格布局（Grid Layout）中的一个强大属性，它允许开发者*通过定义命名的网格区域*来创建直观、语义化的布局。这种方式*特别适合构建复杂的网页结构*，因为它可以*将布局可视化地描述为一个“网格模板”*。

---

### `grid-template-areas` 的基本概念

#### 作用
`grid-template-areas` 通过*命名网格区域*的方式定义网格布局。*每个区域由一个名称表示*，并且可以通过这些名称*轻松控制项目的排列*。

#### 语法
```css
grid-template-areas:
    "<area-name> <area-name> ..."
    "<area-name> <area-name> ...";
```

- `<area-name>`：表示一个*网格区域的名称*。
- 每一行用*引号*括起来，表示网格的一行。
- 名称之间用*空格*分隔，表示列的划分。
- 使用*点号 `.`* 表示空白区域。

---

### 使用方法

#### 定义网格区域
在父容器中使用 `grid-template-areas` *定义网格区域*，**为每一个单元格命名**：

```css
.container {
    display: grid;
    grid-template-columns: 50px 50px 50px 50px; /* 定义四列 */
    grid-template-rows: 50px 50px 50px; /* 定义三行 */
    grid-template-areas:
        "header header header header"
        "main main . sidebar"
        "footer footer footer footer";
}
```

在这个例子中：
- 第一行，一个名为 `header` 的区域，跨越所有列。
- 第二行，三个区域：左侧是 `main`，右侧是 `sidebar`，中间是一个空的单元格。
- 第三行，一个名为 `footer` 的区域，跨越所有列。

---

#### 分配项目到区域
通过 `grid-area` 属性*将子项目分配到对应的网格区域*：

```css
.item-header {
    grid-area: header; /* 分配到 header 区域 */
}
.item-sidebar {
    grid-area: sidebar; /* 分配到 sidebar 区域 */
}
.item-main {
    grid-area: main; /* 分配到 main 区域 */
}
.item-footer {
    grid-area: footer; /* 分配到 footer 区域 */
}
```

![](assets/排版-Grid-API/2023-09-19-14-47-21-image.png)

---

### 示例代码

以下是一个完整的示例，展示如何使用 `grid-template-areas` 创建一个典型的网页布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS Grid Template Areas</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: 1fr 3fr; /* 两列，比例为 1:3 */
            grid-template-rows: auto 1fr auto; /* 三行 */
            grid-template-areas:
                "header header"
                "sidebar main"
                "footer footer";
            gap: 20px; /* 行间距和列间距 */
            height: 100vh;
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
            padding: 20px;
            text-align: center;
            border: 1px solid #ccc;
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

**运行结果分析：**

- **布局结构**：
  - `header` 跨越所有列，位于第一行。
  - `sidebar` 和 `main` 分别位于第二行的左侧和右侧。
  - `footer` 跨越所有列，位于第三行。
- **动态调整**：
  - 列宽按 `1fr 3fr` 的比例分配。
  - 行高根据内容自动调整，中间行占据剩余空间。

---

### 高级用法

#### 跨越多列或多行
可以通过重复命名区域来实现跨越多列或多行的效果。例如：

```css
.container {
    grid-template-areas:
        "header header header"
        "sidebar main main"
        "sidebar footer footer";
}
```

在这个例子中：
- `header` 跨越所有列。
- `sidebar` 跨越第二行和第三行。
- `main` 和 `footer` 分别位于不同的行。

---

#### 空白区域
使用点号 `.` 表示*空白区域*。例如：

```css
.container {
    grid-template-areas:
        "header header header"
        ". main main"
        "footer footer footer";
}
```

在这个例子中：
- 第二行的第一列为空白区域。

---

### 注意事项

1. **区域必须连续**：
   - 区域名称必须连续分布，不能跳过网格线或形成不规则形状。

2. **兼容性**：
   - `grid-template-areas` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

3. **结合其他属性**：
   - `grid-template-areas` 通常与 `grid-template-columns` 和 `grid-template-rows` 结合使用，以定义完整的网格结构。

---

### 总结

- **核心特点**：
  - 使用命名区域*简化复杂布局*。
  - 提供*语义化*的方式描述布局结构。
- **主要优势**：
  - *直观易读*，适合团队协作。
  - 支持跨越多列或多行的区域。
- **最佳实践**：
  - 使用 `grid-template-areas` 创建清晰的布局模板。
  - 结合 `grid-template-columns` 和 `grid-template-rows` 定义精确的网格尺寸。