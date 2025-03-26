在 CSS 网格布局（Grid Layout）中，`justify-content` 是一个重要的属性，用于控制整个网格容器在主轴方向（通常是水平方向）上的分布方式。它决定了*网格整体如何在容器内排列*，并处理剩余空间的分配。

---

### 1. `justify-content` 的基本概念

#### 作用
**当网格总尺寸小于容器尺寸时使用。** 用于设置*水平方向*，*相对于容器*的对齐方式。

- **主轴方向**：`justify-content` 控制的是**列轴（水平方向）** 的分布方式。
- **适用范围**：该属性作用于整个网格容器，影响*所有网格项目*在主轴上的排列和分布。

#### 语法
```css
justify-content: start | end | center | space-between | space-around | space-evenly;
```

- **取值**：
  - `start`：网格靠主轴起点对齐。
  - `end`：网格靠主轴终点对齐。
  - `center`：网格在主轴上居中对齐。
  - `space-between`：网格均匀分布，两端对齐，网格之间的间距相等。
  - `space-around`：每个网格两侧的间距相等，网格之间的间距是网格与容器边缘间距的两倍。
  - `space-evenly`：每个网格之间的间距以及网格与容器边缘的间距相等。
  - `stretch`（默认值）

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `justify-content` 控制网格的整体分布方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grid Justify-content Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: repeat(3, 50px); /* 定义三列 */
            grid-template-rows: repeat(2, 50px); /* 定义两行 */
            gap: 10px; /* 行间距和列间距 */
            justify-content: space-between; /* 主轴分布方式 */
            border: 1px solid #ccc;
            width: 400px; /* 容器宽度大于网格总宽度 */
            height: 200px;
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
网格靠主轴起点对齐。

```css
.container {
    justify-content: start;
}
```
![](assets/排版-Grid-API/2023-09-19-19-13-53-image.png)

#### 3.2 `end`
网格靠主轴终点对齐。

```css
.container {
    justify-content: end;
}
```
![](assets/排版-Grid-API/2023-09-19-19-14-52-image.png)

#### 3.3 `center`
网格在主轴上居中对齐。

```css
.container {
    justify-content: center;
}
```
![](assets/排版-Grid-API/2023-09-19-19-15-23-image.png)

#### 3.4 `space-between`
网格均匀分布，两端对齐，网格之间的间距相等。

```css
.container {
    justify-content: space-between;
}
```
![](assets/排版-Grid-API/2023-09-19-19-18-15-image.png)

#### 3.5 `space-around`
每个网格两侧的间距相等，网格之间的间距是网格与容器边缘间距的两倍。

```css
.container {
    justify-content: space-around;
}
```
![](assets/排版-Grid-API/2023-09-19-19-17-39-image.png)

#### 3.6 `space-evenly`
每个网格之间的间距以及网格与容器边缘的间距相等。

```css
.container {
    justify-content: space-evenly;
}
```
![](assets/排版-Grid-API/2023-09-19-19-19-22-image.png)

#### `stretch`（默认值？）
比如设置`grid-template-columns: 100px auto 100px`，第二列的网格没有设置一个固定尺寸，然后再设置以下的值（该值为默认值），*则会拉伸*。如果设置的是固定尺寸(px)，*则无法拉伸*。

```css
.container {
  justify-content: stretch;
}
```
![](assets/排版-Grid-API/2023-09-19-19-16-01-image.png)

---

### 4. 注意事项

1. **剩余空间的影响**：
   - 如果网格容器的宽度小于或等于网格的总宽度，则 `justify-content` 不会生效，因为没有剩余空间可供分配。

2. **与 `align-content` 的区别**：
   - `justify-content` 控制的是**主轴方向（水平方向）**的分布。
   - `align-content` 控制的是**交叉轴方向（垂直方向）**的分布。

3. **兼容性**：
   - `justify-content` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。


---

### 5. 总结

- **核心功能**：
  - `justify-content` 控制*整个网格在主轴方向*上的分布方式。
- **常用取值**：
  - `start`、`end`、`center`、`space-between`、`space-around` 和 `space-evenly`。
- **最佳实践**：
  - 使用 `justify-content` 实现灵活的网格分布。
  - 结合 `align-content` 处理交叉轴上的分布。
