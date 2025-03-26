`align-items` 是 CSS 网格布局（Grid Layout）和弹性布局（Flexbox）中的一个属性，用于控制项目在**行轴（垂直方向）** 上的对齐方式。它作用于**所有项目**，可以将项目在单元格内沿垂直方向对齐。

---

### 1. `align-items` 的基本概念

#### 作用
- **主轴方向**：`align-items` 控制的是*项目在**单元格**内的**行轴**（垂直方向）* 的对齐方式。
- **适用范围**：该属性作用于整个网格容器或弹性容器，影响所有项目在单元格内的垂直对齐。

#### 语法
```css
align-items: start | end | center | stretch;
```

- **取值**：
  - `start`：项目在单元格的顶部对齐。
  - `end`：项目在单元格的底部对齐。
  - `center`：项目在单元格的垂直居中对齐。
  - `stretch`（*默认值*）：项目拉伸以填满单元格的高度。

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `align-items` 控制网格项目的垂直对齐方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Align-items Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: repeat(3, 100px); /* 三列，每列 100px */
            grid-template-rows: repeat(2, 100px); /* 两行，每行 100px */
            gap: 10px; /* 行间距和列间距 */
            align-items: center; /* 垂直居中对齐 */
            border: 1px solid #ccc;
            height: 300px;
        }
        .item {
            background-color: lightblue;
            text-align: center;
            padding: 10px;
            border: 1px solid #000;
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

### 3. 不同取值的效果

#### 3.1 `start`
项目靠单元格的顶部对齐。

```css
.container {
    align-items: start;
}
```

![](assets/排版-Grid-API/2023-09-19-19-06-13-image.png)

#### 3.2 `end`
项目靠单元格的底部对齐。

```css
.container {
    align-items: end;
}
```

![](assets/排版-Grid-API/2023-09-19-19-07-15-image.png)

#### 3.3 `center`
项目在单元格的垂直居中对齐。

```css
.container {
    align-items: center;
}
```

![](assets/排版-Grid-API/2023-09-19-19-07-45-image.png)

#### 3.4 `stretch`（默认值）
项目拉伸以填满单元格的高度。

```css
.container {
    align-items: stretch;
}
```

![](assets/排版-Grid-API/2023-09-19-19-08-17-image.png)

---

### 4. 单独调整某个项目
如果需要*单独调整某个项目的垂直对齐方式*，可以使用 `align-self` 属性。

**示例代码**：
```css
.item1 {
    align-self: start; /* 单个项目靠顶部对齐 */
}
.item2 {
    align-self: end; /* 单个项目靠底部对齐 */
}
```

**完整示例**：
```html
<div class="container">
    <div class="item item1">Item 1</div>
    <div class="item item2">Item 2</div>
    <div class="item">Item 3</div>
    <div class="item">Item 4</div>
</div>
```

---

### 5. 注意事项

1. **默认值**：
   - 如果未设置 `align-items`，默认值为 `stretch`，即项目会拉伸以填满单元格的高度。

2. **与 `justify-items` 的区别**：
   - `align-items` 控制的是**行轴（垂直方向）** 的对齐。
   - `justify-items` 控制的是**列轴（水平方向）** 的对齐。

3. **兼容性**：
   - `align-items` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 6. 总结

- **核心功能**：
  - `align-items` 控制所有项目*在单元格内*的垂直对齐方式。
- **常用取值**：
  - `start`、`end`、`center` 和 `stretch`。
- **最佳实践**：
  - 使用 `align-items` 统一控制项目的垂直对齐。
  - 使用 `align-self` *单独调整*某个项目的对齐方式。
