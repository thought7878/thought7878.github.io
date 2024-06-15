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

#### 隐形网格

##### 是什么：元素数量超出了定义的单元格数量，自动产生新的行（grid-auto-flow:row），自动产生新的列（grid-auto-flow:column）

##### grid-auto-rows/grid-auto-columns：设置隐形网格的行列尺寸

####

####

### 单元格中的元素如何放置

#### justify-items/align-items：设置 grid 元素相对于单元格的定位。容器属性。

#### place-self = align-self/justify-self：设置 grid 元素相对于单元格的定位。项目属性。

### 整个网格在容器空间中如何放置

#### justify-content/align-content：设置整个网格相对于容器的定位。容器属性。
