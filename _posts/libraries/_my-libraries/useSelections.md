# useSelections

**useSelections：** 封装常见的 Checkbox（多选框）逻辑封装。支持多选、全选等操作。

我们先来看看 Checkbox 常用的场景，如下场景：
![[interview/简历/media/f52bf6384b0dc2fffe717158f9000877_MD5.png]]

<p align=center><img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5fcc97f1d884624a96e5f2c35e2b81c~tplv-k3u1fbpfcp-watermark.image?" alt="image.png"  /></p>

## 需求

1. 两类按钮：全选按钮和列表项按钮
2. 所有的按钮可单独点击，第一次点击为选中状态，再次点击为未选中状态；
3. 列表项按钮：有一个选中，全选按钮为半选状态，全部选中为全选状态；
4. 全选按钮：点击后控制所有列表项按钮为全选状态，再次点击，全部为未选中状态。


## 实现

### 入参
**lists: T[]**
多选列表项的数组。

**initValues: T[]**
被选中数组的初始默认值

首先核心点是全选按钮与列表项按钮的关联，列表项按钮的状态要在 useSelections 中，所以 useSelections 的第一个参数是列表项按钮的数组数据。

其次，分析下 Checkbox（Antd组件） 这个全选组件，选中通过 `checked` 属性、半选通过`indeterminate`、点击通过 `onClick` 属性。


### 出参：数据和修改数据的函数
useSelections 的内部状态和数据：
- **selected**：选中值数组，状态
- **selectedSet**：通过new Set 去掉重复的选中的数据，转化为数组需要使用Array.from。使用useCreation钩子初始化一个Set对象，该对象用于存储选中的元素，此方法确保仅在selected依赖项变化时才重新创建Set对象，以优化性能

useSelections 的反参为：
- **selected**：选中值数组，状态


- **addSelect**：添加元素或数组
- **addAll**：全选所有项目。将所有列表项添加到选中集合中，并更新选中项数组

- **deleteSelect**：删除选中的元素
- **deleteAll**：取消选择所有项目。

- **updateSelect**：更新选中项。此函数用于更新选中项的集合，接受单个项或项的数组作为参数。它首先清除当前的选中项集合，然后根据传入的数据重新填充集合，最后更新选中的状态
- **toggleItem**：切换列表项按钮的状态。如果 selected 中存在对应的数据，则剔除，如果不存在，则增加；
- **toggleAll**：切换所有项的选中状态。此函数根据当前所有项是否已选中来决定是调用 unSelectAll() 还是 selectAll()。它的作用是批量选择或取消选择所有项，具体行为取决于当前的选中状态

- **isSelected**：检查给定数据是否被选中，通过判断数据是否在 selected 中；
- **isSelectedNone**：是否全部未选中
- **isSelectedAll**：是否全部选中；
- **isSelectedPartially**：是否在半选状态。

**数据/状态：selected**

useSelections 维护了选中的列表项按钮的状态。通过 *new Set* 更方便过滤重复数据，但要注意的是拿数据的时候要通过 Array.from 来处理。

**修改数据的方法：增加、删除、更新、查询等的方法**

直接就上代码：

```ts

```

**效果：**

<p align=center><img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65f884125a414824add5ac3574929d4e~tplv-k3u1fbpfcp-watermark.image?" alt="img.gif"  /></p>


**useSelections 的单元测试：**

关于 useSelections 的单元测试实际非常简单，只需要简单地对其方法进行测试就行了，这里就没有必要讲述，感兴趣的小伙伴可以直接看代码，

<p align=center><img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1d1dba21fb9459585a0fa20fb8ff999~tplv-k3u1fbpfcp-watermark.image?" alt="image.png"  /></p>

> 实际上，useSelection 的实现并不难，它是以实际场景为条件所创建的，依赖度相对较高。但这里有一个提醒：**Hooks 是基于逻辑的，而非 View 层面**。


## 参考
[[7.基础篇｜自定义 Hooks 之 Jest（三）：场景类 Hooks]]
