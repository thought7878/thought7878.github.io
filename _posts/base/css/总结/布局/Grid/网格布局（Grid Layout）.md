CSS `网格布局（Grid Layout）`是一种*强大的二维布局系统*，允许开发者*同时控制行和列的布局*。与 Flexbox 的一维布局（水平或垂直方向）不同，Grid 布局可以*在两个维度上进行精确控制*，非常*适合构建复杂的网页布局*。

---

### 1. 网格布局的核心概念

#### 1.1 网格容器（Grid Container）
通过*设置 `display: grid` 或 `display: inline-grid` 的元素*成为网格容器。

```css
.container {
    display: grid; /* 块级网格容器 */
    /* display: inline-grid; */ /* 行内网格容器 */
}
```

#### 1.2 网格项目（Grid Item）
网格容器的*直接子元素*称为网格项目。

#### 1.3 网格线（Grid Line）
- 网格由水平和垂直的网格线组成（概念上的，并不显示，如下图紫色线条为第二列网格线）。
- 网格线用于定义网格轨道（行和列）的边界。
![](https://raw.githubusercontent.com/ShaoWeibin/images/master/grid-line.png)

#### 1.4 网格单元格（Grid Cell）
- 由相邻的四条网格线围成的区域（如下图第一条第二条行网格线和第二第三列网格线交汇的紫色区域）。
- 类似于表格中的单元格。

![](https://github.com/ShaoWeibin/images/blob/master/grid-cell.png?raw=true)

#### 1.5 网格区域（Grid Area）
- *由多个网格单元格组成*的矩形区域。
- 可以跨越多行或多列。

![](https://github.com/ShaoWeibin/images/blob/master/grid-area.png?raw=true)

#### 1.6 网格轨道（Grid Track）
两条*相邻*网格线之间的空间，即`网格行或列`。如下图第二条和第三条行网格线之间的紫色区域。  

![](https://github.com/ShaoWeibin/images/blob/master/grid-track.png?raw=true)

---

### 2. 网格容器的属性

#### 2.1 `grid-template-columns` 和 `grid-template-rows`
*定义网格的列、行的大小、数量*。空格代表网格线。

参考：[[grid-template-rows & grid-template-columns]]

```css
.container {
    grid-template-columns: 40px 50px auto 50px 40px; /* 定义五列 */
    grid-template-rows: 25px 100px auto; /* 定义三行 */
}
```

![](assets/排版-Grid-API/2023-09-19-13-57-06-image.png)

#### `grid-template`
是的简写形式。

参考：[[grid-template]]


#### 2.2  `gap`、`row-gap`、`column-gap`
定义*网格之间的间距*（行间距和列间距）。
gap 是 row-gap 和 column-gap 的简写。

```css
.container {
    gap: <row-gap> <column-gap>;
    
    gap: 20px; /* 行间距和列间距均为 20px */
    /* row-gap: 20px; column-gap: 10px; */ /* 分别定义行间距和列间距 */

	grid-template-columns: 100px 50px 100px;
    grid-template-rows: 80px auto 80px;
    column-gap: 10px;
    row-gap: 15px;
}
```

![](assets/排版-Grid-API/2023-09-19-18-32-37-image.png)

#### 2.3 `grid-auto-columns` 和 `grid-auto-rows`
*定义隐式网格轨道的大小*（当网格项目*超出显式定义的行列时*）。

```css
.container {
    grid-auto-rows: 100px; /* 隐式行的高度为 100px */
}
```

#### 2.4 `grid-template-areas`
通过命名区域创建语义化的布局。

参考：[[grid-template-areas]]

#### 2.5 `justify-items` 和 `align-items`
定义所有项目的对齐方式：
- `justify-items`：水平方向（列轴）的对齐方式。
- `align-items`：垂直方向（行轴）的对齐方式。

参考：[[justify-items]]、[[align-items]]

```css
.container {
    justify-items: center; /* 水平居中对齐 */
    align-items: start;    /* 垂直顶部对齐 */
}
```

#### 2.6 `place-items`
是`align-items` 和 `justify-items`的简写形式。

```css
place-items: <align-items> <justify-items>;
```

参考：[[place-items]]

#### 2.7 `justify-content` 和 `align-content`
定义*整个网格在容器内的对齐方式*（当网格尺寸小于容器时）：
- `justify-content`：水平方向（列轴）的对齐方式。
- `align-content`：垂直方向（行轴）的对齐方式。

参考：[[justify-content]]、[[align-content]]

```css
.container {
    justify-content: space-around; /* 水平均匀分布 */
    align-content: center;         /* 垂直居中对齐 */
}
```

#### 2.8 `place-content`
是 align-content 和 justify-content的缩写。

```css
place-content:<align-content>/<justify-content>
```


---

### 3. 网格项目的属性

#### 3.1 `grid-column-start`、`grid-column-end`、`grid-row-start`、`grid-row-end`
以上这几个属性，用于**控制项目的位置、项目所占网格大小**，*参考特定的网格线确定项目在网格内的位置*。 

- **grid-column-start:**  指定项目在列轴上的起始位置
- **grid-column-end:**  指定项目在列轴上的结束位置
- **grid-row-start:**  指定项目在行轴上的起始位置
- **grid-row-end:**  指定项目在行轴上的结束位置

参考：[[grid-column-start、grid-column-end、grid-row-start、grid-row-end]]

#### 3.2 `grid-column` 和 `grid-row`
是 grid-column-start、grid-column-end、grid-row-start、grid-row-end 的*简写形式*。定义*项目占据的列和行范围*。

参考：[[grid-column-start、grid-column-end、grid-row-start、grid-row-end#5. `grid-column` 和 `grid-row`]]

```css
.item {
    grid-column: 1 / 3; /* 从第 1 条网格线到第 3 条网格线 */
    grid-row: 2 / 4;    /* 从第 2 条网格线到第 4 条网格线 */
}
```

#### 3.3 `grid-area`
定义*项目占据的网格区域*（可以结合 `grid-template-areas` 使用）。

参考：[[grid-area]]

```css
.item {
	/* 分配命名区域 */
	grid-area: <area-name>;

	/* 指定位置和跨越范围 */
	grid-area: <row-start> / <column-start> / <row-end> / <column-end>;
	
    grid-area: 1 / 2 / 3 / 4; /* 起始行/起始列/结束行/结束列 */
}
```

#### 3.4 `justify-self` 和 `align-self`
参考：[[justify-self]]、[[align-self]]

*单独*定义某个项目的对齐方式：
- `justify-self`：水平方向（*列*轴）的对齐方式。
- `align-self`：垂直方向（*行*轴）的对齐方式。

```css
.item {
    justify-self: end; /* 水平靠右对齐 */
    align-self: center; /* 垂直居中对齐 */
}
```

#### 3.5 `place-self`
该属性为`justify-self`和`align-self`的简写模式。
如果省略第二个值，则将第一个值分配给这两个属性。

参考：[[place-self#3. 不同取值的效果]]

```css
place-self: <align-self> <justify-self>;
```


```css
.item-a {
  place-self: center;
}
```

![](assets/排版-Grid-API/2023-09-19-21-18-35-image.png)

```css
.item-a {
  place-self: center stretch;
}
```

![](assets/排版-Grid-API/2023-09-19-21-20-55-image.png)



---

### 4. 示例代码

以下是一个完整的示例，展示如何使用 Grid 构建一个简单的网页布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS Grid Example</title>
    <style>
        .container {
            display: grid;
            grid-template-columns: 1fr 3fr; /* 两列，比例为 1:3 */
            grid-template-rows: auto 1fr auto; /* 三行 */
            gap: 20px;
            height: 100vh;
        }
        .header, .footer {
            background-color: lightcoral;
            padding: 20px;
            text-align: center;
        }
        .sidebar {
            background-color: lightblue;
            padding: 20px;
        }
        .main {
            background-color: lightgreen;
            padding: 20px;
        }
        .header {
            grid-column: 1 / -1; /* 占据所有列 */
        }
        .footer {
            grid-column: 1 / -1; /* 占据所有列 */
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">Header</div>
        <div class="sidebar">Sidebar</div>
        <div class="main">Main Content</div>
        <div class="footer">Footer</div>
    </div>
</body>
</html>
```

---

### 5. 网格布局的优点

1. **二维布局**：
   - 同时控制行和列，适合复杂布局。
2. **响应式设计**：
   - 使用 `fr` 单位和媒体查询，轻松实现响应式布局。
3. **语义化布局**：
   - 通过 `grid-template-areas` 创建直观的布局结构。
4. **灵活性**：
   - 支持动态调整网格项目的大小和位置。

---

### 6. 注意事项

1. **兼容性**：
   - 现代浏览器普遍支持 CSS Grid，但在一些老旧浏览器中可能需要回退方案。
2. **嵌套问题**：
   - 避免过度嵌套网格布局，可能导致性能问题或复杂性增加。
3. **与 Flexbox 的对比**：
   - 如果只需要一维布局，Flexbox 更加轻量。
   - 如果需要同时控制行和列，Grid 是更好的选择。

---

### 7. 总结

CSS 网格布局是一种强大的工具，能够帮助开发者轻松实现复杂的二维布局。无论是简单的响应式设计还是复杂的多列布局，Grid 都能胜任。通过掌握上述核心概念和属性，您可以灵活地构建现代化的网页布局！


### 参考资料
- [A Complete Guide to CSS Grid | CSS-Tricks - CSS-Tricks](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-column-row-start-end)
- [[2019-12-18-排版-Grid]]
- [[2019-12-18-排版-Grid-API]]
- [[2019-12-18_Grid]]