# Why

- **优化用户交互体验**
    - **避免阻塞高优先级任务**：在 React 应用中，当一个复杂的状态更新（如大量数据的重新计算或渲染）正在进行时，可能会阻塞用户的其他交互操作。例如，在一个数据密集型的表格组件中，当用户在搜索框中输入内容来过滤表格数据时，如果同时触发了一个复杂的表格重新计算（如重新计算每行数据的统计信息），这个计算过程可能会使搜索框的输入响应变得迟缓。使用`useTransition`可以将这种复杂的计算标记为非紧急更新，让搜索框的输入（高优先级任务）能够及时响应，避免用户感觉到卡顿。
    - **提升视觉反馈的即时性**：对于一些需要即时视觉反馈的操作，如按钮点击、滚动等，`useTransition`可以确保这些操作的反馈（如按钮按下效果、滚动条的动态显示）不会被其他低优先级的更新所延迟。例如，在一个具有动画效果的按钮组件中，当用户点击按钮时，按钮的按下动画是高优先级的视觉反馈。如果此时有一个低优先级的更新（如后台的数据加载更新）正在进行，使用`useTransition`可以让按钮的动画优先展示，提升了用户体验的即时性。
- **有效管理更新优先级**
    - **区分紧急和非紧急更新**：在复杂的应用场景中，存在多种不同类型的更新，有些更新需要立即处理，而有些更新可以稍微延迟。`useTransition`提供了一种明确的方式来区分这两种类型的更新。例如，在一个包含实时聊天功能和数据同步功能的应用中，用户发送聊天消息是紧急更新，需要立即处理和显示；*而后台的数据同步更新可以是非紧急更新，通过`useTransition`可以将它们合理地安排*，使得聊天消息的发送和显示不会被数据同步过程所干扰。

# What

`useTransition`是 React 提供的一个 Hook，用于**将更新标记为非紧急（non - urgent）更新**。它允许**将某些状态更新延迟处理，使得高优先级的任务（如用户输入响应）能够优先处理，从而提升用户体验的流畅性**。

# How

**基本语法**：
```jsx
const [isPending, startTransition] = useTransition();
```
- `isPending`：是一个布尔值，用于表示*是否有过渡（transition）正在进行*。当`startTransition`函数被调用后，在过渡期间，`isPending`的值为`true`，*可以用于在 UI 上显示加载指示器*或者其他过渡相关的提示。
- `startTransition`：是一个函数，*用于启动一个过渡更新*。它接受一个函数作为参数，这个函数内部*包含了要标记为非紧急的更新逻辑*。例如，可以在这个函数内部更新一个状态，这个状态的更新就会被视为非紧急更新。    

**示例**：

```jsx
import React, { useState, useTransition } from'react';
function InputAndList() {
  const [inputValue, setInputValue] = useState('');
  const [listData, setListData] = useState([]);
  // 
  const [isPending, startTransition] = useTransition();
  
  const handleInputChange = (e) => {
    setInputValue(e.target.value);
    startTransition(() => {
	  // 包含了要标记为非紧急的更新逻辑
      // 模拟一个耗时的列表数据更新
      const newData = [];
      for (let i = 0; i < 1000; i++) {
        newData.push(inputValue +'-'+ i);
      }
      setListData(newData);
    });
  };
  
  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={handleInputChange}
      />
      {
	    // isPending
      }
      {isPending && <p>正在更新列表...</p>}
      <ul>
        {listData.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

**示例说明**：

在`InputAndList`组件中，有一个输入框和一个列表。当用户在输入框中输入内容时，`handleInputChange`函数会被调用。首先，通过`setInputValue`更新输入框的值，这是一个紧急更新，确保输入框能够即时响应用户输入。然后，使用`startTransition`函数包裹一个模拟耗时的列表数据更新操作。这个更新操作被标记为非紧急更新。在更新过程中，`isPending`的值为`true`，可以在 UI 上显示一个 “正在更新列表...” 的提示。这样，用户在输入框中输入内容时，输入框的响应不会被列表数据更新所阻塞，提升了交互体验。