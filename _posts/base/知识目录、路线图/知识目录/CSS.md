- [ ] 整理CSS知识目录 [created:: 2025-03-22T21:23:02] [start:: 2025-03-22T21:23:09] [🍅:: 8]  ^pbfg


学习CSS（Cascading Style Sheets，层叠样式表）是前端开发的重要一环。CSS用于*控制网页的外观和布局*，与HTML结合使用可以创建美观且功能强大的网页。以下是一个详细的学习内容和学习计划，帮助你系统地掌握CSS。


# 基础知识
## CSS简介
参考：[[CSS是什么？为什么使用CSS？]]

- CSS是什么？
- 为什么使用CSS？
- CSS如何与HTML协作？参考：[[CSS与HTML的协作]]
	- 内联样式、内部样式表、外部样式表。
	- 三者的区别？
	- 层叠规则？参考：[[层叠规则]]

## CSS的基本语法
- 规则结构：选择器、声明块、属性和值。
     ```css
     selector {
         property: value;
     }
     ```

## 单位

- 绝对单位：`px`（像素）。参考：[[单位]]
- 相对单位：`em`（父元素字体倍数）、`rem`（根元素字体倍数）、`%`、`vw/vh`（视窗比例）
- 现代单位：`min()`/`max()`、`clamp()`（动态适配）。参考：[[_posts/base/css/总结/函数|函数]]

## 颜色表示
参考：[[颜色]]
- 十六进制：`#RRGGBB`
- RGB/RGBA：`rgb(255, 0, 0)` / `rgba(255, 0, 0, 0.5)`
- 关键词：`red`, `transparent`

## 选择器
### 基本选择器
- 元素选择器：`div`
- 类选择器：`.class`
- ID 选择器：`#id`
- 通配符：`*`

### 组合选择器
- 后代：`div p`
- 子元素：`div > p`
- 相邻兄弟：`div + p`
- 通用兄弟：`div ~ p`
### 伪类与伪元素
- 伪类：`:hover`、`:active`、`:nth-child()`。参考：[[伪类选择器]]
- 伪元素：`::before`、`::after`

### **层叠规则、优先级计算**
参考：[[层叠规则]]

- 层叠规则：`!important` > 行内样式 > ID > 类/伪类/属性 > 元素/伪元素
- 权重公式：`(0,0,0,0)` → 分别对应 ID、类、元素、数量级

# 核心属性
## 文本样式
- 字体：`font-family`、`font-size`、`font-weight`
- 颜色：`color`、`background-color`
- 文本对齐：`text-align`、`line-height`
- 文本装饰：`text-decoration`、`text-transform`

## **盒模型**
参考：[[盒子模型（Box Model）]]

- 组成：`content` → `padding` → `border` → `margin`
- `box-sizing`属性：`content-box`（默认，宽度仅内容）、`border-box`（宽度包含 padding + border）

## 背景与边框
- 背景颜色：`background-color`
- 背景图片：`background-image`、`background-repeat`、`background-position`
- 边框样式：`border-style`、`border-radius`

# 布局
## 经典布局
### 定位
- `position`属性：
	- `static`（默认）
	- `relative`（相对自身偏移）
	- `absolute`（相对最近非 static 父级）
	- `fixed`（相对视口）
	- `sticky`（滚动吸附）

### 浮动布局
- 浮动布局：`float` 和 `clear`

## **弹性布局（Flexbox）**
- [ ] [[弹性布局（Flexbox）]] [created:: 2025-03-25T13:36:42] [start:: 2025-03-25T13:36:47] [🍅:: 3] [completion:: 2025-03-25T16:49:32]  ^1a9z
- 为什么使用？
- 主轴、交叉轴
- 容器属性
- 子项属性

## **网格布局（Grid）**
- [ ] [[网格布局（Grid Layout）]] [created:: 2025-03-25T20:21:40] [start:: 2025-03-25T20:21:44] [completion:: 2025-03-26T22:20:48]  [🍅:: 10]  ^hzca
- **定义网格容器**：`display: grid`
- **划分行和列**：`grid-template-rows`、`grid-template-columns`
- **定位子项**：`grid-row`、`grid-column`

## **响应式设计**
- [ ] [[响应式设计]] [created:: 2025-03-26T22:22:14] 
- 媒体查询（Media Queries）：`@media`规则
- 移动优先设计原则。
- 使用`meta`标签设置视口


# 动画与交互
参考：[[动画]]

## 过渡（Transition）
参考：[[过渡（Transition）]]

- 为什么需要？

## 关键帧动画（Animation）
参考：[[关键帧动画（Animation）]]

- 为什么需要？

## 变换（Transform）
参考：[[变换（Transform）]]

- 


# 现代特性
## CSS变量（Custom Properties）
- 定义和使用变量

## 现代框架实践
- Tailwind CSS：原子化工具类
- CSS Modules：局部作用域样式

## 预处理工具
- Sass/Less：支持变量、嵌套、混合（Mixin）等功能。

## 性能优化
- 减少重排和重绘。
- 使用简写属性。

## 跨浏览器兼容性
- 使用工具（如Autoprefixer）解决浏览器前缀问题。
- 测试网页在主流浏览器中的表现。

