`place-self` 是 CSS 网格布局（Grid Layout）和弹性布局（Flexbox）中的一个简写属性，用于同时设置 `align-self` 和 `justify-self`。它允许开发者通过一个属性控制单个项目在单元格内的**水平和垂直对齐方式**，从而简化代码并提高可读性。

---

### 1. `place-self` 的基本概念

#### 作用
- **主轴方向**：`justify-self` 控制的是**列轴（水平方向）** 的对齐方式。
- **交叉轴方向**：`align-self` 控制的是**行轴（垂直方向）** 的对齐方式。
- **简写形式**：`place-self` 同时设置这两个方向的对齐方式。

#### 语法
```css
place-self: <align-self> <justify-self>;
```

- 如果只提供一个值，则该值会同时应用于 `align-self` 和 `justify-self`。
- 如果提供两个值：
  - 第一个值表示 `align-self`（垂直方向）。
  - 第二个值表示 `justify-self`（水平方向）。

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `place-self` 单独调整某个项目的水平和垂直对齐方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Place-self Example</title>
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
            place-self: start end; /* 垂直靠顶部，水平靠右侧 */
        }
        .item2 {
            place-self: center; /* 水平和垂直居中对齐 */
        }
        .item3 {
            place-self: stretch; /* 拉伸以填满单元格 */
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item item1">Item 1</div>
        <div class="item item2">Item 2</div>
        <div class="item item3">Item 3</div>
    </div>
</body>
</html>
```

---

### 3. 不同取值的效果

#### 3.1 单值语法
如果只提供一个值，则该值会同时应用于 `align-self` 和 `justify-self`。

```css
.item {
    place-self: center; /* 水平和垂直居中对齐 */
}
```

等价于：
```css
.item {
    align-self: center;
    justify-self: center;
}
```

![](assets/排版-Grid-API/2023-09-19-21-18-35-image.png)

#### 3.2 双值语法
如果提供两个值：
- 第一个值表示 `align-self`（垂直方向）。
- 第二个值表示 `justify-self`（水平方向）。

```css
.item {
    place-self: start end; /* 垂直靠顶部，水平靠右侧 */
}
```

等价于：
```css
.item {
    align-self: start;
    justify-self: end;
}
```

---

### 4. 支持的取值

| **取值**     | **含义**                                                                 |
|--------------|-------------------------------------------------------------------------|
| `start`      | 项目靠单元格的起点对齐（垂直方向为顶部，水平方向为左侧）。                 |
| `end`        | 项目靠单元格的终点对齐（垂直方向为底部，水平方向为右侧）。                 |
| `center`     | 项目在单元格内居中对齐（垂直和水平方向均居中）。                          |
| `stretch`    | 项目拉伸以填满单元格的高度或宽度（默认值）。                              |

---

### 5. 注意事项

1. **默认值**：
   - 如果未显式设置 `place-self`，默认值为 `stretch`。

2. **与单独属性的关系**：
   - `place-self` 是 `align-self` 和 `justify-self` 的组合。
   - 如果需要更精细的控制，可以单独使用 `align-self` 或 `justify-self`。

3. **兼容性**：
   - `place-self` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 6. 总结

- **核心功能**：
  - `place-self` 同时控制单个项目的垂直和水平对齐方式。
- **常用取值**：
  - `start`、`end`、`center` 和 `stretch`。
- **最佳实践**：
  - 使用 `place-self` 简化代码，统一控制对齐方式。
  - 如果需要单独调整，可以使用 `align-self` 或 `justify-self`。
