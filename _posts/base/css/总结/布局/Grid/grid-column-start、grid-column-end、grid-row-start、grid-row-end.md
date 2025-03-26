`grid-column-start`、`grid-column-end`、`grid-row-start` 和 `grid-row-end` 是 CSS 网格布局（Grid Layout）中的核心属性，用于精确*控制网格项目在网格中的位置和跨越范围*。它们允许开发者指定项目的起始和结束位置，从而实现灵活的布局。

---

### 1. 基本概念

#### 作用
- 这些属性用于*定义网格项目在网格中的位置*：
  - `grid-column-start` 和 `grid-column-end`：*控制项目在列方向上的起始和结束位置*。
  - `grid-row-start` 和 `grid-row-end`：*控制项目在行方向上的起始和结束位置*。
- 它们通过**网格线（grid line）** 来定位项目。

---

### 2. 语法

```css
/* 单独设置 */
grid-column-start: <line>;
grid-column-end: <line>;
grid-row-start: <line>;
grid-row-end: <line>;

/* 组合设置 */
grid-column: <start-line> / <end-line>;
grid-row: <start-line> / <end-line>;
```

- `<line>`：表示网格线的*编号或名称*。
  - 编号从 `1` 开始，依次递增。
  - *负数*表示从右向左或从下向上的反向计数。
  - 如果网格线被命名，可以直接使用名称。

- `span <number>` – *项目将跨越提供的网格轨道数*
- 如果没有声明  `grid-column-end` / `grid-row-end` ，*则默认情况下该项目将跨越 1 个轨道*。
- *项目可以相互重叠*。您可以使用  `z-index`  来控制它们的堆叠顺序。

**例子：**

```css
.item-a {
  grid-column-start: 2;
  grid-column-end: five;
  grid-row-start: row1-start;
  grid-row-end: 3;
}
```

![](assets/排版-Grid-API/2023-09-19-20-00-21-image.png)

```css
.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start;
  grid-row-start: 2;
  grid-row-end: span 2;
}
```

![](assets/排版-Grid-API/2023-09-19-20-03-27-image.png)


---

### 3. 使用方法

以下是一个完整的示例，展示如何使用这些属性控制网格项目的位置和跨越范围：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grid Line Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: repeat(4, 1fr); /* 定义四列 */
            grid-template-rows: repeat(3, 100px); /* 定义三行 */
            gap: 10px; /* 行间距和列间距 */
            border: 1px solid #ccc;
            height: 400px;
        }
        .item {
            background-color: lightblue;
            text-align: center;
            line-height: 100px;
            border: 1px solid #000;
        }
        .item1 {
            grid-column-start: 1;
            grid-column-end: 3; /* 跨越两列 */
            grid-row-start: 1;
            grid-row-end: 2;
        }
        .item2 {
            grid-column: 3 / 5; /* 起始列线 3，结束列线 5 */
            grid-row: 2 / 4; /* 起始行线 2，结束行线 4 */
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">Item 1</div>
        <div class="item item2">Item 2</div>
    </div>
</body>
</html>
```

---

### 4. 属性详解

#### 4.1 `grid-column-start` 和 `grid-column-end`
- **作用**：定义*项目在列方向上的起始和结束位置*。
- **取值**：
  - 网格线*编号*（如 `1`, `2`, `-1` 等）。
  - 网格线*名称*（如果定义了命名网格线）。
  - `span <number>`：表示**跨越的列数**。
- **示例**：
  ```css
  .item {
      grid-column-start: 1; /* 起始于第 1 条列线 */
      grid-column-end: 4;   /* 结束于第 4 条列线 */
  }
  ```

#### 4.2 `grid-row-start` 和 `grid-row-end`
- **作用**：定义*项目在行方向上的起始和结束位置*。
- **取值**：
  - 网格线编号（如 `1`, `2`, `-1` 等）。
  - 网格线名称（如果定义了命名网格线）。
  - `span <number>`：表示**跨越的行数**。
- **示例**：
  ```css
  .item {
      grid-row-start: 2; /* 起始于第 2 条行线 */
      grid-row-end: span 2; /* 跨越 2 行 */
  }
  ```

---

### 5. `grid-column` 和 `grid-row`

为了简化代码，可以使用 `grid-column` 和 `grid-row` 的**简写形式**：

```css
/* 等价于单独设置 grid-column-start 和 grid-column-end */
grid-column: <start-line> / <end-line>;

/* 等价于单独设置 grid-row-start 和 grid-row-end */
grid-row: <start-line> / <end-line>;
```

- **示例**：
  ```css
  .item {
      grid-column: 3 / span 2; /* 起始于第 3 条列线，跨越 2 列 */
      grid-row: third-line / 4; /* 起始于 third-line 行线，结束于第 4 条行线 */
  }
  ```

![](assets/排版-Grid-API/2023-09-19-20-08-35-image.png)

---

### 6. 高级用法

#### 6.1 使用命名网格线
可以通过 `grid-template-columns` 和 `grid-template-rows` *定义命名网格线*，然后在项目中引用这些名称。

```css
.container {
    display: grid;
    grid-template-columns: [start] 1fr [middle] 1fr [end];
    grid-template-rows: [top] 100px [center] 100px [bottom];
}

.item {
    grid-column: start / middle; /* 起始于 start，结束于 middle */
    grid-row: top / center; /* 起始于 top，结束于 center */
}
```

#### 6.2 动态跨越
使用 `span` 关键字动态*跨越多列或多行*。

```css
.item {
    grid-column: 2 / span 3; /* 从第 2 条列线开始，跨越 3 列 */
    grid-row: 1 / span 2; /* 从第 1 条行线开始，跨越 2 行 */
}
```

---

### 7. 注意事项

1. **默认值**：
   - 如果未显式设置这些属性，则项目会自动*放置在下一个可用的网格单元中*。

2. **负数索引**：
   - 负数索引从右向左或从下向上计数，例如 `-1` 表示最后一列或最后一行。

3. **兼容性**：
   - 这些属性在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 8. 总结

- **核心功能**：
  - `grid-column-start` 和 `grid-column-end` 控制项目在列方向上的位置和跨越范围。
  - `grid-row-start` 和 `grid-row-end` 控制项目在行方向上的位置和跨越范围。
- **常用技巧**：
  - 使用 `span` 实现动态跨越。
  - 使用命名网格线提高可读性。
- **最佳实践**：
  - 结合 `grid-template-columns` 和 `grid-template-rows` *明确网格结构*。
  - 使用简写形式简化代码。
