`justify-self` 是 CSS 网格布局（Grid Layout）中的一个属性，用于控制**单个网格项目在列轴（水平方向）上的对齐方式**。它允许开发者单独调整某个项目在单元格内的水平对齐方式，而不会影响其他项目。

---

### 1. `justify-self` 的基本概念

#### 作用
- **主轴方向**：`justify-self` 控制的是**列轴（水平方向）** 的对齐方式。
- **适用范围**：该属性作用于*单个网格项目*，而不是整个容器。
- **触发条件**：只有*当项目的宽度小于其所在单元格的宽度时*，`justify-self` 才会生效。

#### 语法
```css
justify-self: start | end | center | stretch;
```

- **取值**：
  - `start`：项目靠单元格的左侧对齐。
  - `end`：项目靠单元格的右侧对齐。
  - `center`：项目在单元格内水平居中对齐。
  - `stretch`（*默认值*）：项目拉伸以填满单元格的宽度。

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `justify-self` 单独调整某个项目的水平对齐方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Justify-self Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: repeat(3, 100px); /* 定义三列 */
            grid-template-rows: repeat(2, 100px); /* 定义两行 */
            gap: 10px; /* 行间距和列间距 */
            border: 1px solid #ccc;
            height: 300px;
        }
        .item {
            background-color: lightblue;
            text-align: center;
            line-height: 50px;
            border: 1px solid #000;
        }
        .item1 {
            justify-self: start; /* 靠左侧对齐 */
        }
        .item2 {
            justify-self: end; /* 靠右侧对齐 */
        }
        .item3 {
            justify-self: center; /* 水平居中对齐 */
        }
        .item4 {
            justify-self: stretch; /* 拉伸以填满单元格 */
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">Item 1</div>
        <div class="item item2">Item 2</div>
        <div class="item item3">Item 3</div>
        <div class="item item4">Item 4</div>
    </div>
</body>
</html>
```

---

### 3. 不同取值的效果

#### 3.1 `start`
项目靠单元格的左侧对齐。

```css
.item1 {
    justify-self: start;
}
```

效果：
- 项目紧贴单元格的左侧边缘。

![](assets/排版-Grid-API/2023-09-19-21-09-19-image.png)

---

#### 3.2 `end`
项目靠单元格的右侧对齐。

```css
.item2 {
    justify-self: end;
}
```

效果：
- 项目紧贴单元格的右侧边缘。

![](assets/排版-Grid-API/2023-09-19-21-09-56-image.png)

---

#### 3.3 `center`
项目在单元格内水平居中对齐。

```css
.item3 {
    justify-self: center;
}
```

效果：
- 项目在单元格内水平居中。

![](assets/排版-Grid-API/2023-09-19-21-10-34-image.png)

---

#### 3.4 `stretch`（默认值）
项目拉伸以填满单元格的宽度。

```css
.item4 {
    justify-self: stretch;
}
```

效果：
- 项目的宽度被拉伸，完全填满单元格。

![](assets/排版-Grid-API/2023-09-19-21-10-59-image.png)

---

### 4. 注意事项

1. **与 `justify-items` 的区别**：
   - `justify-items` *影响所有项目*，作用于整个容器。
   - `justify-self` *仅影响单个项目*，可以覆盖容器的 `justify-items` 设置。

2. **默认值**：
   - 如果未显式设置 `justify-self`，默认值为 `stretch`。

3. **兼容性**：
   - `justify-self` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 6. 总结

- **核心功能**：
  - `justify-self` 控制单个网格项目在列轴（水平方向）上的对齐方式。
- **常用取值**：
  - `start`、`end`、`center` 和 `stretch`。
- **最佳实践**：
  - 使用 `justify-self` 单独调整某个项目的水平对齐方式。
  - 结合 `justify-items` 统一控制其他项目的对齐方式。
