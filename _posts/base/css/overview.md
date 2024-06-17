# 样式

# 布局

## Flex

### 容器属性

#### flex-direction：设置主轴方向

##### row|column|row-reverse|column-reverse;

#### flex-wrap：超出是否换行

##### nowrap|wrap|wrap-reverse

#### flex-flow：同时设置主轴线方向和超出是否换行

##### \<flex-direction\> \<flex-wrap\>

#### justify-content：子项目在主轴线上的对齐方式

##### flex-start|flex-end|center|space-between|space-around

#### align-items：子项目在交叉轴上的对齐方式

##### stretch|flex-start|flex-end|center|baseline

#### align-content：多行的对齐方式，只有多行才有效

##### stretch | flex-start | flex-end | center | space-between | space-around

### 项目属性

#### flex-grow：**主轴**剩余空间按所有各项目的放大比例分配；默认值 0，放大比例为 0，不占用剩余空间

#### flex-shrink：**主轴**缺少空间按所有各项目的缩放比例分配；默认值 1，缩小比例为 1，空间不足缩小

#### flex-basis：主轴方向的尺寸，优先级 width < flex-basis < max-width；在分配多余空间之前，项目占据的主轴空间；默认值 auto，即项目的本来大小

#### flex：\<flex-grow\> \<flex-shrink\> \<flex-basis\>简写属性；默认值 0 1 auto，不放大，缩小，尺寸本来大小

##### 两个快捷值：auto(1,1,auto)、none(0,0,auto)，优先使用 flex，浏览器会自动推算其他值

#### align-self：自身对齐方式；默认值 auto 继承父容器 align-items 的值，设置为其他值可覆盖继承值(父容器设置会超出换行时，覆盖无效)

##### auto | flex-start | flex-end | center | baseline | stretch

#### order：排列顺序

## Grid

### 如何画网格

#### grid 对比 flex

#### 应用场景

#### 网格的含义

#### grid-template-columns/grid-template-rows

##### 为什么

##### 是什么

##### 怎么用：语法

#### fr 单位

#### auto

#### minmax()

#### auto/fr/minmax 的三个要点

##### 空间占用优先级：minmax()>fr>auto

##### 最大空间和最小空间

##### 为了保证最小空间，网格可能会超出容器的范围

#### repeat()

##### auto-fill():根据尺寸尽可能多地动态地生成行列数

##### auto-fit():内部没有元素，单元格尺寸收缩为 0

#### gap：column-gap/row-gap

####

### 如何把元素放到网格的单元格中

#### 单元格中的元素尺寸

##### 默认自动填满单元格、也可小于/超出单元格；

##### 单元格是参考线

#### grid 中的 z-index：flex/grid 的 item 设置 z-index 为非 auto，就产生自己的 stacking context

#### 网格线

##### why/解决的问题：放置元素到指定的单元格

##### grid-row（grid-row-start/grid-row-end）

##### grid-colum（grid-colum-start/grid-colum-end）

##### 自定义网格线名称

#### 隐形单元格/网格

##### 是什么：元素数量超出了定义的单元格数量，自动产生新的行（grid-auto-flow:row），自动产生新的列（grid-auto-flow:column）

##### grid-auto-rows/grid-auto-columns：设置隐形网格的行列尺寸

### 单元格中的元素如何放置

#### justify-items/align-items：设置 grid 元素相对于单元格的定位。容器属性。

#### place-self = align-self/justify-self：设置 grid 元素相对于单元格的定位。项目属性。

### 整个网格在容器空间中如何放置

#### justify-content/align-content：设置整个网格相对于容器的定位。容器属性。

## 文档流/document flow

### 为什么？解决常规的常见的页面布局，从左到右，从上到下

### 是什么？文档流，块级/行内文档流，块级/行内元素的内部的排列规则。

#### 块级元素内部的文档流：可以是块级/行内/文字元素，从上到下，从左到右，排列

#### 行内元素内部的文档流：可以是行内/文字元素，先从左到右，排不下了，再从上到下，排列

### display 值的规则：设置元素的外部表现和内部表现

#### flex/grid 会让内部元素的 display 外部表现失效，只能设置元素的内部表现

### 文本流/text flow：行内元素/文字元素的外部排列规则，在块级元素内部有效，在 flex/grid 内部无效

## 浮动/float 属性

### 浮动的基本逻辑和效果:一定程度脱离文档流，但没完全脱离

### 浮动的两种副作用

#### 浮动元素之后的元素会补位、环绕的问题

#### 父元素的空间坍缩的问题

##### 解决方案 1：clear 属性

##### 解决方案 2：设置 overflow 非 visible

##### 解决方案 3：display:flow-root

### clear 属性

#### 为什么：解决浮动元素之后的元素会补位/环绕的问题

## BFC/IFC/FFC/GFC

###

## 定位 position

# 响应式布局

# 动画

# 选择器
