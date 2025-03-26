在 CSS 网格布局（Grid Layout）中，`align-content` 是一个用于控制**整个网格容器在交叉轴方向（通常是垂直方向）上的分布方式**的属性。它决定了多行网格整体如何在容器内排列，并处理剩余空间的分配。

---

### 1. `align-content` 的核心概念

#### 作用
- **交叉轴方向**：`align-content` 控制的是**行轴（垂直方向）** 的分布方式。
- **适用范围**：
  - 该属性*仅在网格有多行时生效*。
  - 如果*网格只有一行*，则 `align-content` 不会生效，因为没有多行需要分布。
- **触发条件**：只有当*网格容器的高度大于网格内容的总高度时*，`align-content` 才会生效。

#### 语法
```css
align-content: start | end | center | space-between | space-around | space-evenly | stretch;
```

- **取值**：
  - `start`：网格靠交叉轴起点对齐。
  - `end`：网格靠交叉轴终点对齐。
  - `center`：网格在交叉轴上居中对齐。
  - `space-between`：网格均匀分布，两端对齐，网格之间的间距相等。
  - `space-around`：每个网格两侧的间距相等，网格之间的间距是网格与容器边缘间距的两倍。
  - `space-evenly`：每个网格之间的间距以及网格与容器边缘的间距相等。
  - `stretch`（*默认值*）：网格拉伸以填满容器的高度。

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `align-content` 控制网格的整体分布方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grid Align-content Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: repeat(3, 50px); /* 定义三列 */
            grid-template-rows: repeat(2, 50px); /* 定义两行 */
            gap: 10px; /* 行间距和列间距 */
            align-content: space-between; /* 交叉轴分布方式 */
            border: 1px solid #ccc;
            height: 400px; /* 容器高度大于网格总高度 */
        }
        .item {
            background-color: lightblue;
            text-align: center;
            line-height: 50px;
            border: 1px solid #000;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">1</div>
        <div class="item">2</div>
        <div class="item">3</div>
        <div class="item">4</div>
        <div class="item">5</div>
        <div class="item">6</div>
    </div>
</body>
</html>
```

---

### 3. 不同取值的效果

#### 3.1 `start`
网格靠交叉轴起点对齐。

```css
.container {
    align-content: start;
}
```

效果：
- 网格整体靠顶部对齐，底部留有空白。

![](assets/排版-Grid-API/2023-09-19-19-29-36-image.png)

---

#### 3.2 `end`
网格靠交叉轴终点对齐。

```css
.container {
    align-content: end;
}
```

效果：
- 网格整体靠底部对齐，顶部留有空白。

![](assets/排版-Grid-API/2023-09-19-19-30-05-image.png)

---

#### 3.3 `center`
网格在交叉轴上居中对齐。

```css
.container {
    align-content: center;
}
```

效果：
- 网格整体在容器内垂直居中对齐。

![](assets/排版-Grid-API/2023-09-19-19-30-33-image.png)

---

#### 3.4 `space-between`
网格均匀分布，两端对齐，网格之间的间距相等。

```css
.container {
    align-content: space-between;
}
```

效果：
- 第一行靠顶部，最后一行靠底部，中间行均匀分布。

![](assets/排版-Grid-API/2023-09-19-19-33-01-image.png)

---

#### 3.5 `space-around`
每个网格两侧的间距相等，网格之间的间距是网格与容器边缘间距的两倍。

```css
.container {
    align-content: space-around;
}
```

效果：
- 每个网格两侧的间距相等，边缘间距是中间间距的一半。

![](assets/排版-Grid-API/2023-09-19-19-32-11-image.png)

---

#### 3.6 `space-evenly`
每个网格之间的间距以及网格与容器边缘的间距相等。

```css
.container {
    align-content: space-evenly;
}
```

效果：
- 网格之间以及网格与容器边缘的间距完全相等。

![](assets/排版-Grid-API/2023-09-19-19-33-55-image.png)

---

#### 3.7 `stretch`（默认值）
网格拉伸以填满容器的高度。

```css
.container {
    align-content: stretch;
}
```

效果：
- 网格的行高被拉伸，以填满容器的可用高度。

![](assets/排版-Grid-API/2023-09-19-19-31-00-image.png)

---

### 4. 注意事项

1. **单行网格的影响**：
   - 如果网格只有一行，则 `align-content` 不会生效，因为没有多行需要分布。

2. **与 `justify-content` 的区别**：
   - `justify-content` 控制的是**主轴方向（水平方向）** 的分布。
   - `align-content` 控制的是**交叉轴方向（垂直方向）** 的分布。

3. **兼容性**：
   - `align-content` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 5. 总结

- **核心功能**：
  - `align-content` 控制整个网格在交叉轴方向上的分布方式。
- **常用取值**：
  - `start`、`end`、`center`、`space-between`、`space-around`、`space-evenly` 和 `stretch`。
- **最佳实践**：
  - 使用 `align-content` 实现灵活的多行网格分布。
  - 结合 `justify-content` 处理主轴方向上的分布。
  - 确保容器的高度大于网格的总高度以激活 `align-content` 的效果。
