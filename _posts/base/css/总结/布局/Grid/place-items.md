`place-items` 是 CSS 中的一个简写属性，用于同时设置 `align-items` 和 `justify-items`。它在网格布局（Grid Layout）和弹性布局（Flexbox）中都非常有用，能够*简化代码*并提高可读性。

---

### 1. `place-items` 的基本概念

#### 作用
- `place-items` 同时控制项目在**行轴（垂直方向）** 和**列轴（水平方向）** 上的对齐方式。
- 它是 `align-items` 和 `justify-items` 的组合。

#### **语法**
```css
place-items: <align-items> <justify-items>;
```

- 如果*只提供一个值*，则该值会同时应用于 `align-items` 和 `justify-items`。
- 如果*提供两个值*：
  - 第一个值表示 `align-items`（垂直方向）。
  - 第二个值表示 `justify-items`（水平方向）。

---

### 2. 使用方法

以下是一个完整的示例，展示如何使用 `place-items` 控制网格项目的对齐方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Place-items Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: repeat(3, 100px); /* 三列，每列 100px */
            grid-template-rows: repeat(2, 100px); /* 两行，每行 100px */
            gap: 10px; /* 行间距和列间距 */
            place-items: center; /* 水平和垂直居中对齐 */
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

#### **3.1 单值语法**
如果只提供一个值，则该值会同时应用于 `align-items` 和 `justify-items`。

```css
.container {
    place-items: center; /* 水平和垂直居中对齐 */
}
```

等价于：
```css
.container {
    align-items: center;
    justify-items: center;
}
```

#### 3.2 双值语法
如果提供两个值：
- 第一个值表示 `align-items`（垂直方向）。
- 第二个值表示 `justify-items`（水平方向）。

```css
.container {
    place-items: start end; /* 垂直靠顶部对齐，水平靠右侧对齐 */
}
```

等价于：
```css
.container {
    align-items: start;
    justify-items: end;
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

### 5. 示例效果

#### 5.1 水平和垂直居中对齐
```css
.container {
    place-items: center;
}
```
- 所有项目在单元格内水平和垂直居中对齐。

#### 5.2 垂直靠顶部，水平靠左侧对齐
```css
.container {
    place-items: start;
}
```
- 所有项目靠单元格的左上角对齐。

#### 5.3 垂直靠底部，水平靠右侧对齐
```css
.container {
    place-items: end;
}
```
- 所有项目靠单元格的右下角对齐。

#### 5.4 拉伸填充单元格
```css
.container {
    place-items: stretch;
}
```
- 所有项目拉伸以填满单元格的高度和宽度。

---

### 6. 注意事项

1. **默认值**：
   - 如果未设置 `place-items`，默认值为 `stretch`，即项目会拉伸以填满单元格的高度和宽度。

2. **与单独属性的关系**：
   - `place-items` 是 `align-items` 和 `justify-items` 的简写形式。
   - 如果需要更精细的控制，可以单独使用 `align-items` 或 `justify-items`。

3. **兼容性**：
   - `place-items` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### 7. 总结

- **核心功能**：
  - `place-items` 同时控制项目的垂直和水平对齐方式。
- **常用取值**：
  - `start`、`end`、`center` 和 `stretch`。
- **最佳实践**：
  - 使用 `place-items` 简化代码，统一控制对齐方式。
  - 如果需要单独调整，可以使用 `align-items` 或 `justify-items`。

通过合理使用 `place-items`，您可以轻松实现灵活且美观的布局！