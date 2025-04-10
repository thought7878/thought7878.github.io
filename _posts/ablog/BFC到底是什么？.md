## Introduction

前端面试题集锦中，CSS部分经常会问到关于BFC的理解。以我最近在看的一个面试题合集为例，它给的解释是：

> BFC 即块级格式上下文，根据盒模型可知，每个元素都被定义为一个矩形盒子，然而盒子的布局会受到尺寸，定位，盒子的子元素或兄弟元素，视口的尺寸等因素决定，*所以这里有一个浏览器计算的过程*，*计算的规则*就是由一个叫做`视觉格式化模型`的东西所定义的，BFC 就是来自这个概念，它是 CSS 视觉渲染的一部分，用于决定块级盒的布局及浮动相互影响范围的一个区域。
> 
> 作者：vortesnail 链接：[https://juejin.cn/post/7061588533214969892](https://juejin.cn/post/7061588533214969892)

读完感觉听到了什么，又感觉没听懂。所以我决定写一篇文章来解释一下BFC到底是个什么。

## 定义

BFC的全称是Block Formatting Context，块级格式上下文。

1. Formatting Context: 我们把它拆解开来，先看`Formatting Context`格式上下文，这个概念是`CSS2.1`中引入的，用来*描述元素如何在文档中定位和相互作用*，页面上所有内容都是格式上下文的 一部分。再通俗一点，*格式上下文就是一块区域，这块区域里的元素按照一定的规则进行布局*。
    
2. Block：块级，块级元素会在新的一行开始，自上而下垂直排列，比如`div`、`p`、`h1`等。
    

**合在一起看，Block Formatting Context（BFC）就是一个根据块布局规则布置子元素的区域，与其他区域相互独立。**

除此之外，格式上下文还有`flex formatting context`（flex格式上下文）、`grid formatting context`（grid格式上下文）等。

## BFC的特性

在W3C的规范中，BFC有以下特性：

- Boxes are laid out one after the other, vertically, beginning at the top of a containing block
- The vertical distance between two sibling boxes is determined by the 'margin' properties
- Vertical margins between adjacent block-level boxes in a block formatting context collapse

中文版本：

- 在块格式上下文中，框从包含块的顶部开始，一个接一个地垂直布局。
- 两个同级框之间的垂直距离由“边距”属性确定。
- 块格式上下文中相邻块级框之间的垂直边距**折叠**。

> [!Tip] 对于第三点，垂直边距折叠是指，当两个相邻的块级元素的*上下边距发生重叠时*，它们的边距会取两者中较大的那个值。例如：
> 
> ```html
> <div style="margin-top: 20px;">div1</div>
> <div style="margin-top: 30px;">div2</div>
> ```
> 
> 这两个`div`元素之间的垂直边距是30px，而不是20px+30px=50px。

## BFC的创建

在W3C规范中，只用简单的一句话就概括了BFC的创建规则：

> Floats, absolutely positioned elements, block containers (such as inline-blocks, table-cells, and table-captions) that are not block boxes, and block boxes with 'overflow' other than 'visible' (except when that value has been propagated to the viewport) establish new block formatting contexts for their contents.

也就以下这些东西会导致BFC的产生：

- Float 浮动元素
- 绝对定位的元素
- 不是块级盒子的块容器（如inline-block内联块、table-cells表格单元格和table-captions表标题）
- 有overflow属性的块级盒子（除了visible）

## BFC的应用

### 1. 防止margin重叠

举例：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <style>
      .box2 {
        width: 100px;
        height: 100px;
        background: deepskyblue;
        margin-top: 50px;
      }
      .box1 {
        width: 100px;
        height: 100px;
        background: red;
        margin-bottom: 100px;
      }
      /* .container {
        overflow: hidden;
      } */
    </style>
  </head>
  <body>
    <!-- <div class="container"> -->
    <div class="box1">box1</div>
    <!-- </div> -->
    <div class="box2">box2</div>
  </body>
</html>
```

这两个`div`元素之间的垂直边距是100px，而不是100px+100px=200px。这是因为它们都在同一个BFC中。默认情况下，页面的根元素`<html>`和`<body>`元素都是BFC，所以这两个`div`元素都在同一个BFC中。

1. 方法1 我们可以给其中一个`div`元素创建一个新的BFC。如上面代码中，我们给`box1`元素的父元素`container`添加`overflow: hidden`属性，这样`box1`元素就会创建一个新的BFC，从而防止margin重叠。
    
2. 方法2 根据上面我们学到的创建BFC的几个条件，我们也可以把其中一个`div`元素改成行内块元素，如下：
    

```html
<div class="box1" style="display: inline-block;">box1</div>
```

### 2.清除浮动

尽管浮动可以让元素脱离文档流，但是它会导致父元素的高度塌陷，这时候我们可以给父元素创建一个新的BFC来清除浮动。

例如：

```html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <!-- <div class="clear"></div> class="clear"-->
</ul>
<div class="content"></div>
```

```css
ul li {
  list-style: none;
  width: 200px;
  height: 100px;
  background-color: #c58585;
  float: left;
}
.content {
  width: 100%;
  height: 200px;
  background-color: rgb(87, 179, 182);
}
```

下面的蓝色content会和1，2，3重叠，因为`ul`的高度塌陷了，我们可以给`ul`添加`overflow:hidden`属性，让`ul`创建一个新的BFC，从而清除浮动。 或者在`ul`后面添加一个空的`div`，给这个`div`添加一个clear类，这个类里面设置`clear:both`属性，也可以清除浮动。

### 3.自适应两栏布局

想实现左侧固定，右侧自适应的两栏布局，有以下几种方法：

1. 浮动

```html
<div class="outer">
  <div class="left">左侧</div>
  <div class="right">右侧</div>
</div>
```

利用浮动，左边元素宽度固定 ，设置向左浮动。

```css
.left {
  width: 200px;
  height: 200px;
  background-color: lightcoral;
  float: left;
}
```

将右边元素的`margin-left`设置为左边元素的宽度，且右边元素的 `width`默认为`auto`，这样右边元素会自适应父元素的宽度，且留出左侧元素的空间。

```css
.right {
  height: 200px;
  background-color: lightseagreen;
  margin-left: 200px;
}
```

2. 浮动加BFC

```css
.outer {
  height: 100px;
}
.left {
  float: left;
  width: 200px;
  height: 100%;
  background: lightcoral;
}
.right {
  overflow: auto;
  height: 100%;
  background: lightseagreen;
}
```

左边元素宽度固定 ，设置向左浮动。右侧元素设置`overflow: hidden`; 这样右边就触发了 BFC ，BFC 的区域不会与浮动元素发生重叠，所以两侧就不会发生重叠。

3. flex布局

```css
.outer {
  display: flex;
  height: 100px;
}
.left {
  width: 200px;
  height: 100%;
  background: lightcoral;
}
.right {
  flex: 1;
  height: 100%;
  background: lightseagreen;
}
```

`flex:1`是`flex-grow`、`flex-shrink`和`flex-basis`的缩写，`flex-grow`是指定项目的放大比例，默认为0，即如果存在剩余空间，也不放大。`flex-shrink`是指定项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。`flex-basis`是指定项目的初始大小，默认为auto，即项目的本来大小。 全部设置为1，表示项目将等分剩余空间。

## 参考资料

[1] [https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flow_layout/Introduction_to_formatting_contexts](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flow_layout/Introduction_to_formatting_contexts)

[2] [https://www.w3.org/TR/CSS2/visuren.html#normal-flow](https://www.w3.org/TR/CSS2/visuren.html#normal-flow)

[3] [https://juejin.cn/post/7301201921204011049](https://juejin.cn/post/7301201921204011049)