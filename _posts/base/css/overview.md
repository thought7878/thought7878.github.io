# 样式

# 布局

## Flex

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

###
