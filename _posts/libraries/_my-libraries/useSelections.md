# useSelections

**useSelections：** 封装常见的 Checkbox（多选框）逻辑封装。支持多选、全选等操作。

我们先来看看 Checkbox 常用的场景，如下场景：

<p align=center><img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5fcc97f1d884624a96e5f2c35e2b81c~tplv-k3u1fbpfcp-watermark.image?" alt="image.png"  /></p>

## 需求

1. 两类按钮：全选按钮和单项按钮
2. 所有的按钮可单独点击，第一次点击为选中状态，再次点击为未选中状态；
3. 单项按钮：有一个选中，全选按钮为半选状态，全部选中为全选状态；
4. 全选按钮：点击后控制所有单项按钮为全选状态，再次点击，全部为未选中状态。



## 实现

### 入参
首先核心点是全选按钮与单项按钮的关联，单项按钮的状态要在 useSelections 中，所以 useSelections 的第一个参数是单项按钮的数组数据。
其次，分析下 Checkbox（Antd组件） 这个全选组件，选中通过 `checked` 属性、半选通过`indeterminate`、点击通过 `onClick` 属性。

useSelections 的内部状态和数据：
- **selected**：选中值数组，initValues: 初始化选定值的数组
- **selectedSet**：通过new Set 去掉重复的选中的数据,转化为数组需要使用Array.from。使用useCreation钩子初始化一个Set对象，该对象用于存储选中的元素，此方法确保仅在selected依赖项变化时才重新创建Set对象，以优化性能

### 出参

useSelections 的反参为：
- **selected**：选中值数组，initValues: 初始化选定值的数组；
- **isSelected**：检查给定数据是否被选中，通过判断数据是否在 selected 中；
- **selectAdd**：向选定集合selectedSet中，添加元素或数组
- **selectDel**：从selectedSet中，删除选中的元素
- **setSelect**：此函数用于更新选中项的集合，接受单个项或项的数组作为参数。它首先清除当前的选中项集合，然后根据传入的数据重新填充集合，最后更新选中的状态
- **toggle**：1 ～ 9 单个切换的方法，如果 selected 中存在对应的数据，则剔除，如果不存在，则增加；
- **noneSelected**：是否全部未选中
- **allSelected**：是否全部选中；
- **partiallySelected**：是否在半选状态。
- **selectAll**：全选所有项目。将所有列表项添加到选中集合中，并更新选中项数组
- **unSelectAll**：取消选择所有项目。
- **toggleAll**：切换所有项的选中状态。此函数根据当前所有项是否已选中来决定是调用 unSelectAll() 还是 selectAll()。它的作用是批量选择或取消选择所有项，具体行为取决于当前的选中状态

我们可以发现 *useSelections 实际上是对 1～9 状态的一个维护*，这里通过 new Set 去处理，原因是 Set 处理数据可以方便点，但要注意的是我们拿数据的时候要通过 **Array.from** 来处理。

此外，我们可以将增加、删除、设置的方法单独拿出来，同时可以直接赋给 useSelections 的第二个参数：initValues，用来设置初始默认值。这里就不过多赘述，我们直接就上代码：


```ts
import { useSafeState, useCreation } from "..";
const useSelections = <T,>(lists: T[], initValues: T[] = []) => {
  const [selected, setSelected] = useSafeState<T[]>(initValues);

  // 通过new Set去处理选中的数据,转化为数组需要使用Array.from
  const selectedSet = useCreation(() => new Set(selected), [selected]);

  const isSelected = (data: T) => selectedSet.has(data);

  // 增加
  const selectAdd = (data: T | T[]) => {
    if (Array.isArray(data)) {
      data.map((item) => selectedSet.add(item));
    } else {
      selectedSet.add(data);
    }
    return setSelected(Array.from(selectedSet));
  };

  // 删除
  const selectDel = (data: T | T[]) => {
    if (Array.isArray(data)) {
      data.map((item) => selectedSet.delete(item));
    } else {
      selectedSet.delete(data);
    }
    return setSelected(Array.from(selectedSet));
  };

  // 设置
  const setSelect = (data: T | T[]) => {
    selectedSet.clear();
    if (Array.isArray(data)) {
      data.map((item) => selectedSet.add(item));
    } else {
      selectedSet.add(data);
    }
    return setSelected(Array.from(selectedSet));
  };

  // 状态切换
  const toggle = (data: T) =>
    isSelected(data) ? selectDel(data) : selectAdd(data);

  // 全部未选中
  const noneSelected = useCreation(
    () => lists.every((ele) => !selectedSet.has(ele)),
    [lists, selectedSet]
  );

  // 全部选中
  const allSelected = useCreation(() => {
    return lists.every((ele) => selectedSet.has(ele));
  }, [lists, selectedSet]);

  // 是否半选
  const partiallySelected = useCreation(
    () => !noneSelected && !allSelected,
    [noneSelected, allSelected]
  );

  // 全选
  const selectAll = () => {
    lists.map((item) => selectedSet.add(item));
    setSelected(Array.from(selectedSet));
  };

  const unSelectAll = () => {
    lists.map((item) => selectedSet.delete(item));
    setSelected(Array.from(selectedSet));
  };

  const toggleAll = () => (allSelected ? unSelectAll() : selectAll());

  return {
    selected, // 以选择的元素组
    isSelected, // 是否被选中
    selectAdd,
    selectDel,
    toggle,
    setSelect,
    noneSelected,
    allSelected,
    partiallySelected,
    selectAll,
    unSelectAll,
    toggleAll,
  } as const;
};

export default useSelections;
```

**效果：**


<p align=center><img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/65f884125a414824add5ac3574929d4e~tplv-k3u1fbpfcp-watermark.image?" alt="img.gif"  /></p>


**useSelections 的单元测试：**

关于 useSelections 的单元测试实际非常简单，只需要简单地对其方法进行测试就行了，这里就没有必要讲述，感兴趣的小伙伴可以直接看代码，

<p align=center><img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1d1dba21fb9459585a0fa20fb8ff999~tplv-k3u1fbpfcp-watermark.image?" alt="image.png"  /></p>

> 实际上，useSelection 的实现并不难，只是它跟之前的 Hooks 略有不同，它是以实际场景为条件所创建的，依赖度相对较高。但这里有一个提醒：**Hooks 是基于逻辑的，而非 View 层面**。


## 参考
[[7.基础篇｜自定义 Hooks 之 Jest（三）：场景类 Hooks]]
