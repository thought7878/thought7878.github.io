CSS 的`弹性布局（Flexbox，Flexible Box Layout）`是一种强大的布局模型，用于*在容器内对子元素进行灵活的排列、对齐和分配空间*。它*特别适合构建响应式布局*，能够轻松应对各种屏幕尺寸和动态内容。

## What？

### 1. 弹性布局的核心概念

#### 1.1 容器与项目
- **弹性容器（Flex Container）**：通过设置 `display: flex` 或 `display: inline-flex` 的元素成为弹性容器。
- **弹性项目（Flex Item）**：*弹性容器的直接子元素*称为弹性项目。

#### 1.2 主轴与交叉轴
- **主轴（Main Axis）**：*弹性项目的排列方向*，*默认*是水平方向（从左到右）。
- **交叉轴（Cross Axis）**：*垂直于主轴的方向*，默认是垂直方向。

![[_posts/base/css/待整理/media/d81f4c0cb469fb694190561231459bec_MD5.svg]]

---

### 2. 弹性容器的属性

#### 2.1 `display`
*定义一个弹性容器*：
```css
.container {
    display: flex; /* 块级弹性容器 */
    /* display: inline-flex; */ /* 行内弹性容器 */
}
```

#### 2.2 `flex-direction`
*定义主轴的方向*：
- `row`（默认值）：从左到右。
- `row-reverse`：从右到左。
- `column`：从上到下。
- `column-reverse`：从下到上。

```css
.container {
    flex-direction: row; /* 水平排列 */
}
```

![[_posts/base/css/待整理/media/a7cfb81648ce5ce1fa9d0b4dfef8c5cd_MD5.png]]

![[_posts/base/css/待整理/media/2ea13abb764a59309b0b8dc24f5ff378_MD5.png]]

![[_posts/base/css/待整理/media/7ab96ec82008c0e957f3f8016f3d390c_MD5.png]]

![[_posts/base/css/待整理/media/a65350837ed0042035d9ca2567065561_MD5.png]]

#### 2.3 `flex-wrap`
*控制项目是否换行*：
- `nowrap`（默认值）：不换行，所有项目排成一行。
- `wrap`：换行，从上到下排列。
- `wrap-reverse`：换行，从下到上排列。

```css
.container {
    flex-wrap: wrap; /* 允许换行 */
}
```

![[_posts/base/css/待整理/media/9ec2bd547327f3ca93d7662d91c24683_MD5.png]]

![[_posts/base/css/待整理/media/4cacaac785e910739615a25d84cade63_MD5.png]]

![[_posts/base/css/待整理/media/40bcb2887db262177ff1f6376685615f_MD5.png]]


#### 2.4 `justify-content`
定义*主轴上的对齐方式*：
- `flex-start`（默认值）：靠主轴起点对齐。
- `flex-end`：靠主轴终点对齐。
- `center`：居中对齐。
- `space-between`：两端对齐，项目之间的间距相等。
- `space-around`：每个项目两侧的间距相等。
- `space-evenly`：每个项目之间的间距以及与容器边缘的间距相等。

```css
.container {
    justify-content: center; /* 主轴居中对齐 */
}
```


![[_posts/base/css/待整理/media/34ab2716dfa7e89471f318813f4e8271_MD5.png]]

![[_posts/base/css/待整理/media/1d8a04d0083cfe58f30a05fe6284c4db_MD5.png]]

![[_posts/base/css/待整理/media/f8a8561d6745c81a145b761916ce4c74_MD5.png]]

![[_posts/base/css/待整理/media/91b2ffa1192561c55509cd9cea313ebc_MD5.png]]

![[_posts/base/css/待整理/media/031b6fb60b66949418969ec92fbc39fa_MD5.png]]

`space-evenly`：*扩展： 在父容器头尾添加空子节点，设置为两端对齐可实现所有子项目左右间隔相等*
![[_posts/base/css/待整理/media/f5f1741797ed9e3dec9b58252327b1fc_MD5.png]]


#### 2.5 `align-items`
定义*交叉轴上的对齐方式*：
- `stretch`（默认值）：拉伸以填满容器。
- `flex-start`：靠交叉轴起点对齐。
- `flex-end`：靠交叉轴终点对齐。
- `center`：居中对齐。
- `baseline`：基线对齐。

```css
.container {
    align-items: center; /* 交叉轴居中对齐 */
}
```


![[_posts/base/css/待整理/media/e196278837cd02afe847ebf950b71223_MD5.png]]

![[_posts/base/css/待整理/media/55146e13112be44f24c1ea0c3c7ca123_MD5.png]]

![[_posts/base/css/待整理/media/4aeacc19e50bf3b835f7670c6ff1c3a5_MD5.png]]

![[_posts/base/css/待整理/media/c78df7b515f4d9256442a7d0ff5fba7f_MD5.png]]

![[_posts/base/css/待整理/media/176a10dae9f49e7d7a12a8420455cf26_MD5.png]]


#### 2.6 `align-content`
**当有多行项目时**，*定义这些行在交叉轴上的对齐方式*（类似于 `justify-content`）：
- `stretch`（默认值）：拉伸以填满容器。
- `flex-start`：靠交叉轴起点对齐。
- `flex-end`：靠交叉轴终点对齐。
- `center`：居中对齐。
- `space-between`：两端对齐。
- `space-around`：每个行两侧的间距相等。
- `space-evenly`：每个行之间的间距以及与容器边缘的间距相等。

```css
.container {
    align-content: space-between; /* 多行时，交叉轴两端对齐 */
}
```


![[_posts/base/css/待整理/media/3e28698ee2295ab41b5d321b53070a70_MD5.png]]

![[_posts/base/css/待整理/media/8c829b1ec56a310e76cfd6e49ec5175e_MD5.png]]

![[_posts/base/css/待整理/media/4829d64359e791cf619fdea0bfae8c68_MD5.png]]

![[_posts/base/css/待整理/media/8c1bcc4deef74ea2478ae7cf3f386189_MD5.png]]

![[_posts/base/css/待整理/media/74db8c6a0b769864c1e47b4e51646f09_MD5.png]]

![[_posts/base/css/待整理/media/bc3002b7cfe0d794e716e5aefe119790_MD5.png]]


#### 2.7 flex-flow
`flex-flow: <flex-direction> <flex-wrap>`，缩写属性，同时设置主轴线方向和超出是否换行

---

### 3. 弹性项目的属性

#### 3.1 `order`
*定义项目的排列顺序*，数值越小越靠前，默认值为 `0`。

```css
.item {
    order: 2; /* 排列顺序为第 2 */
}
```

![[_posts/base/css/待整理/media/28c1ec3a534221ef4e29fd489135fa11_MD5.png]]

#### 3.2 `flex-grow`
定义项目的*扩展比例*，*默认值为 `0`（不扩展）*。如果值大于 0，则项目会*按比例扩展*以填满剩余空间。_多出来的空间，按所有各项目的放大比例分配_

```css
.item {
    flex-grow: 1; /* 扩展比例为 1 */
}
```

![[_posts/base/css/待整理/media/07606aa920739fb3469fa505619fcd4e_MD5.png]]

#### 3.3 `flex-shrink`
定义项目的*收缩比例*，*默认值为 `1`（允许收缩）*。如果值为 `0`，则项目不会收缩。_缺少空间，按所有各项目的缩放比例分配_

```css
.item {
    flex-shrink: 0; /* 不允许收缩 */
}
```

![[_posts/base/css/待整理/media/8dfa0a4cb09677d104032acba9873829_MD5.png]]

#### 3.4 `flex-basis`
参考：[[flex-basis]]

定义项目*在主轴上的初始大小*，在分配多余空间之前，项目占据的主轴空间。_可设置值与 width 类似，**优先级：** width < flex-basis < max-width_，可以是长度值（如 `100px`）或百分比（如 `50%`），默认值为 `auto`。

```css
.item {
    flex-basis: 200px; /* 初始大小为 200px */
}
```

#### 3.5 `flex`
`flex` 是 `flex-grow`、`flex-shrink` 和 `flex-basis` 的简写形式，默认值为 `0 1 auto`。

```css
.item {
    flex: 1; /* 等价于 flex: 1 1 0 */
}
```

#### 3.6 `align-self`
*单独定义某个项目的**交叉轴对齐方式***，**覆盖容器的 `align-items` 属性**：
- `auto`（默认值）：继承父容器的 `align-items`。
- `flex-start`、`flex-end`、`center`、`baseline`、`stretch`。

```css
.item {
    align-self: center; /* 单独居中对齐 */
}
```

---

### 4. 示例代码

以下是一个完整的示例，展示如何使用 Flexbox 构建一个简单的响应式布局：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flexbox Example</title>
    <style>
        .container {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
            align-items: center;
            height: 300px;
            border: 1px solid #ccc;
        }
        .item {
            flex: 1 1 100px; /* 扩展、收缩，初始大小为 100px */
            margin: 10px;
            background-color: lightblue;
            text-align: center;
            padding: 20px;
            border: 1px solid #000;
        }
        .item:nth-child(odd) {
            background-color: lightcoral;
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
    </div>
</body>
</html>
```


---


### 5. 适用场景
Flexbox 特别适合以下场景：
1. **一维布局**：水平或垂直方向上的排列（如导航栏、按钮组）。
2. **动态内容**：*内容数量或尺寸不确定时*的布局。
3. **对齐与分布**：需要精确控制对齐方式（如居中、两端对齐）。
4. **响应式设计**：需要根据屏幕尺寸调整布局。
5. **表单布局**：如输入框和标签的对齐。

---

### 6. 缺点
尽管 Flexbox 非常强大，但它也有一些局限性：
1. **二维布局不足**：Flexbox 主要适用于一维布局（水平或垂直）。如果需要同时控制行和列的布局，建议使用 CSS Grid。
2. **老旧浏览器兼容性**：虽然现代浏览器普遍支持 Flexbox，但在一些老旧浏览器中可能需要使用前缀或回退方案。

---


### 7. 注意事项
1. **兼容性**：现代浏览器普遍支持 Flexbox，但在一些老旧浏览器中可能需要使用前缀或回退方案。
2. **嵌套问题**：避免过度嵌套 Flexbox，可能导致性能问题或复杂性增加。


## Why？

弹性布局（Flexbox）是一种现代化的 CSS 布局模型，它被设计**用于解决传统布局方式（如浮动和定位）的局限性**。

以下是*使用 Flexbox 的主要原因及其优点：*

---

### 1. 解决传统布局的痛点
传统的布局方式（如 `float` 和 `position`）在处理复杂布局时存在以下问题：
- **对齐困难**：传统方法难以实现水平或垂直居中等常见需求。
- **响应式设计复杂**：需要手动计算宽度、高度以及间距，增加了代码复杂度。
- **动态内容适应性差**：当内容数量或尺寸变化时，布局容易崩塌。

Flexbox 提供了一种更直观、灵活的方式，能够轻松应对这些问题。

---

### 2. 弹性布局的**核心优势**

#### 2.1 灵活的空间分配
Flexbox 可以*根据容器的可用空间*动态调整子元素的大小和位置。例如：
- 子元素可以*自动扩展或收缩以填满剩余空间*（通过 `flex-grow` 和 `flex-shrink`）。
- 支持*按比例分配空间*（通过 `flex` 属性）。

这使得布局更加灵活，特别适合动态内容或响应式设计。

#### 2.2 简化的对齐方式
Flexbox 提供了丰富的对齐选项，使得对齐变得非常简单：
- **主轴对齐**：通过 `justify-content` 实现水平方向的对齐。
- **交叉轴对齐**：通过 `align-items` 和 `align-self` 实现垂直方向的对齐。
- **多行对齐**：通过 `align-content` 处理多行项目的对齐。

例如，使用 Flexbox 实现水平和垂直居中只需要几行代码：
```css
.container {
    display: flex;
    justify-content: center; /* 水平居中 */
    align-items: center;     /* 垂直居中 */
}
```

#### 2.3 自动换行
Flexbox 支持项目自动换行（通过 `flex-wrap`），解决了传统布局中项目超出容器的问题。例如：
```css
.container {
    display: flex;
    flex-wrap: wrap; /* 允许换行 */
}
```
这非常适合构建网格布局或响应式布局。

#### 2.4 独立的顺序控制
Flexbox 允许通过 `order` 属性改变项目的排列顺序，而无需修改 HTML 结构。例如：
```css
.item1 {
    order: 2; /* 排列顺序为第 2 */
}
.item2 {
    order: 1; /* 排列顺序为第 1 */
}
```
这在动态调整布局或实现特定视觉效果时非常有用。

---

### 3. 响应式设计的支持
Flexbox 是响应式设计的理想选择，因为它能够根据屏幕尺寸和容器大小自动调整布局。例如：
- 使用 `flex-direction` 切换主轴方向（从行到列）。
- 使用 `flex-wrap` 实现多行布局。
- 结合媒体查询，可以轻松实现不同屏幕尺寸下的布局变化。

示例：根据屏幕宽度切换主轴方向
```css
.container {
    display: flex;
    flex-direction: row; /* 默认水平排列 */
}

@media (max-width: 600px) {
    .container {
        flex-direction: column; /* 小屏幕下垂直排列 */
    }
}
```

---

### 4. 减少代码复杂度
*相比于传统的布局方式，Flexbox 能够显著减少代码量*。例如：
- 不再需要复杂的 `float` 清除技巧。
- 不再需要手动计算宽度和间距。
- 不再需要额外的辅助元素（如清除浮动的 `<div>`）。

示例对比：
- **传统布局**（使用 `float`）：
```css
.container {
    overflow: hidden; /* 清除浮动 */
}
.item {
    float: left;
    width: 50%;
}
.clearfix::after {
    content: "";
    display: table;
    clear: both;
}
```

- **Flexbox 布局**：
```css
.container {
    display: flex;
}
.item {
    flex: 1; /* 平均分配空间 */
}
```

---

### 5. 更好的性能
Flexbox 的布局计算由浏览器优化完成，通常比手动计算位置和尺寸的性能更高。此外，Flexbox 的声明式语法减少了开发者的工作量，降低了出错的可能性。

---

### 总结
使用 Flexbox 的主要原因是它提供了一种更直观、灵活且强大的布局方式，能够显著简化开发过程并提升用户体验。无论是简单的对齐还是复杂的响应式布局，Flexbox 都能胜任，并且在大多数情况下是优于传统布局方式的选择。
