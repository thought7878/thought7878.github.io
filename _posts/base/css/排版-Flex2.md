# flex 布局介绍

传统的布局，父层无法很好地控制子层行列的适应，子层需依据文档流，或者通过破坏文档流的形式进行排列。2009 年，W3C 提出了一种新的方案—-**flex 布局，可以很好的控制子层在父层中的行列适应，在一维空间为所欲为，能极大的控制子层的排列及对齐**。目前各大浏览器都已经支持，部分旧版本可引入 postcss 用旧 flex 布局进行兼容。

## 浏览器支持

- new: 使用新版本的 flex 布局 (display: flex)
- tweener：使用过渡版本的 flexbox 布局 (display: flexbox)
- old：使用旧版本的 box 布局 (display: box)
  ![flex-hack](https://pawellin.github.io/css-layout/img/flex-hack.png)

_参考文章链接：_[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)、[ A Visual Guide to CSS3 Flexbox Properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties)

### 一、布局原理及组成

flex 布局基于主轴线及交叉轴进行排列布局，交叉轴跟随主轴线方向改变，始终与主轴线相互垂直。由父容器 container 及子项目 item 组成。

![summary](https://pawellin.github.io/css-layout/img/summary.svg)
`.container {
    display: flex;
}`
设为 Flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效。

_注：以下 flex 属性示例图，子项目宽高默认为 100px × 100px_

### 二、flex 容器属性

[Flex 布局语法教程 | 菜鸟教程](https://www.runoob.com/w3cnote/flex-grammar.html)

[常用的css布局演示](https://pawellin.github.io/css-layout/index.html)

- **flex-direction**
- **flex-wrap**
- **flex-flow**
- **justify-content**
- **align-items**
- **align-content**

**2. 1 flex-direction( 确定主轴线方向 )**
`.box { flex-direction: row | row-reverse | column | column-reverse }`_依次为水平左右方向，垂直上下方向_

![row-reverse](https://pawellin.github.io/css-layout/img/row.png)
![row-reverse](https://pawellin.github.io/css-layout/img/row-reverse.png)
![row-reverse](https://pawellin.github.io/css-layout/img/column.png)
![column-reverse](https://pawellin.github.io/css-layout/img/column-reverse.png)

**2. 2 flex-wrap ( 超出是否换行 )**
`.box { flex-wrap: nowrap | wrap | wrap-reverse }`_超过一行时，换行才有效_
![flex-shrink](https://pawellin.github.io/css-layout/img/nowrap.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/wrap.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/wrap-reverse.png)

**2. 3 flex-flow ( flex-direction + flex-wrap )**
`.box { flex-flow: <flex-direction> <flex-wrap> }`_同时设置主轴线方向和超出是否换行_

**2. 4 justify-content ( 子项目在主轴线上的对齐方式 )**
`.box { justify-content: flex-start | flex-end | center | space-between | space-around }`_依次为主轴线起点对齐、终点对齐、居中对齐、两端对齐、两端间隔相等对齐_
![flex-shrink](https://pawellin.github.io/css-layout/img/flex-start.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/flex-end.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/center.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/space-between.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/space-around.png)
**_扩展： 在父容器头尾添加空子节点，设置为两端对齐可实现所有子项目左右间隔相等_**
![flex-shrink](https://pawellin.github.io/css-layout/img/space-between1.png)

**2. 5 align-items ( 子项目在交叉轴上的对齐方式 )**
`.box { align-items: stretch | flex-start | flex-end | center | baseline }`_依次为占满父容器高度、交叉轴起点对齐、终点对齐、中心对齐、子项目第一行文字基线对齐_
![flex-shrink](https://pawellin.github.io/css-layout/img/1stretch.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/1flex-start.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/1flex-end.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/1center.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/1baseline.png)

**2. 6 align-content ( 多根轴线的对齐方式 )**
`.box { align-content: stretch | flex-start | flex-end | center | space-between | space-around }`
![flex-shrink](https://pawellin.github.io/css-layout/img/stretch.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/sflex-start.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/sflex-end.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/scenter.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/sspace-between.png)
![flex-shrink](https://pawellin.github.io/css-layout/img/sspace-around.png)

### 三、flex 项目属性

- **order**
- **flex-grow**
- **flex-shrink**
- **flex-basis**
- **flex**
- **align-self**

**3. 1 order(排列顺序)**
`.item { order: <Integer || 0> }`_数值越小，排列越靠近主线轴的起点_
![order](https://pawellin.github.io/css-layout/img/order.png)

**3. 2 flex-grow(放大比例)**
`.item { flex-grow: <number || 0> }`_剩余空间按所有各项目的放大比例分配_
![flex-grow](https://pawellin.github.io/css-layout/img/flex-grow.png)

**3. 3 flex-shrink(缩小比例)**
`.item { flex-shrink: <number || 1> }`_缺少空间按所有各项目的缩放比例分配_
![flex-shrink](https://pawellin.github.io/css-layout/img/flex-shrink.png)

**3. 4 flex-basis**

在分配多余空间之前，项目占据的主轴空间，默认值为auto，即项目的本来大小。

`.item { flex-basis: <length || auto> }`

_可设置值与 width 类似，优先级 width < flex-basis < max-width_

缩放比例为 1 且超出父容器的空间，缩放计算公式为：(分配前占的空间/子项目的总空间)_需缩放空间
下图的计算结果为：(200 / 1000) _ 200 = 40；(100 / 1000) \* 200 = 20;
![flex-basis](https://pawellin.github.io/css-layout/img/flex-basis1.png)
![flex-basis](https://pawellin.github.io/css-layout/img/flex-basis2.png)

**3. 5 flex ( flex-grow + flex-shrink + flex-basis )**
`.item { flex: (<flex-grow> <flex-shrink> <flex-basis>) || auto }`
_两个快捷值：auto(1,1,auto)、none(0,0,auto)，优先使用 flex，浏览器会自动推算其他值_

**3. 6 align-self (自身对齐方式)**
`.item { align-self: auto | flex-start | flex-end | center | baseline | stretch }`_默认 auto 值继承父容器 align-items 的值，设置为其他值**可覆盖继承值(父容器设置会超出换行时，覆盖无效**)_
![align-self](https://pawellin.github.io/css-layout/img/align-self.png)

### 四、flex 实例

- 管理系统简易布局
- 水平垂直居中弹层
- 三栏布局自适应布局
- tab 层菜单自适应布局

### 五、grid 布局扩展

flex 布局解决了在一维空间中的行和列中内容如何适应，但在二维空间上还是比较乏力，如**一行排列的项目，其中一个项目的高度超出同等的项目，该高度将会占据整一行，导致下一行的项目无法紧凑排列**。如下图：
![flex-out](https://pawellin.github.io/css-layout/img/flex-out.png)
而 grid 布局在行列的自由布局非常强大，虽然浏览器的兼容性还不大好。

#### 兼容

![grid-hack](https://pawellin.github.io/css-layout/img/grid-hack.png)
