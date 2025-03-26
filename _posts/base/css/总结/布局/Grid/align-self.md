`align-self` 是 CSS 网格布局（Grid Layout）和弹性布局（Flexbox）中的一个重要属性，用于控制**单个网格项目在行轴（垂直方向）上的对齐方式**。它允许开发者单独调整某个项目在单元格内的垂直对齐方式，而不会影响其他项目。

---

### **1. `align-self` 的基本概念**

#### **作用**
- **交叉轴方向**：`align-self` 控制的是**行轴（垂直方向）**的对齐方式。
- **适用范围**：该属性作用于单个网格项目，而不是整个容器。
- **触发条件**：只有当项目的高度小于其所在单元格的高度时，`align-self` 才会生效。

#### **语法**
```css
align-self: start | end | center | stretch;
```

- **取值**：
  - `start`：项目靠单元格的顶部对齐。
  - `end`：项目靠单元格的底部对齐。
  - `center`：项目在单元格内垂直居中对齐。
  - `stretch`（默认值）：项目拉伸以填满单元格的高度。

---

### **2. 使用方法**

#### **示例代码**
以下是一个完整的示例，展示如何使用 `align-self` 单独调整某个项目的垂直对齐方式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Align-self Example</title>
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
            align-self: start; /* 靠顶部对齐 */
        }
        .item2 {
            align-self: end; /* 靠底部对齐 */
        }
        .item3 {
            align-self: center; /* 垂直居中对齐 */
        }
        .item4 {
            align-self: stretch; /* 拉伸以填满单元格 */
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

### **3. 不同取值的效果**

#### **3.1 `start`**
项目靠单元格的顶部对齐。

```css
.item1 {
    align-self: start;
}
```

效果：
- 项目紧贴单元格的顶部边缘。

---

#### **3.2 `end`**
项目靠单元格的底部对齐。

```css
.item2 {
    align-self: end;
}
```

效果：
- 项目紧贴单元格的底部边缘。

---

#### **3.3 `center`**
项目在单元格内垂直居中对齐。

```css
.item3 {
    align-self: center;
}
```

效果：
- 项目在单元格内垂直居中。

---

#### **3.4 `stretch`（默认值）**
项目拉伸以填满单元格的高度。

```css
.item4 {
    align-self: stretch;
}
```

效果：
- 项目被拉伸，完全填满单元格的高度。

---

### **4. 注意事项**

1. **与 `align-items` 的区别**：
   - `align-items` 影响所有项目，作用于整个容器。
   - `align-self` 仅影响单个项目，可以覆盖容器的 `align-items` 设置。

2. **默认值**：
   - 如果未显式设置 `align-self`，默认值为 `stretch`。

3. **兼容性**：
   - `align-self` 在现代浏览器中广泛支持，但在老旧浏览器中可能需要回退方案。

---

### **5. 示例分析**

假设网格容器有 3 列 2 行，以下是不同 `align-self` 的效果：

| **CSS 代码**                | **效果描述**                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| `align-self: start;`       | 项目靠单元格的顶部对齐。                                                     |
| `align-self: end;`         | 项目靠单元格的底部对齐。                                                     |
| `align-self: center;`      | 项目在单元格内垂直居中对齐。                                                 |
| `align-self: stretch;`     | 项目拉伸以填满单元格的高度。                                                 |

---

### **6. 总结**

- **核心功能**：
  - `align-self` 控制单个网格项目在行轴（垂直方向）上的对齐方式。
- **常用取值**：
  - `start`、`end`、`center` 和 `stretch`。
- **最佳实践**：
  - 使用 `align-self` 单独调整某个项目的垂直对齐方式。
  - 结合 `align-items` 统一控制其他项目的对齐方式。

通过合理使用 `align-self`，您可以轻松实现灵活且美观的网格布局！