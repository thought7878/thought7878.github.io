# Why
- **优化用户体验**
    - **避免阻塞 UI 渲染**：在 React 应用中，*当某个状态更新会导致昂贵的计算或者网络请求等耗时操作时，可能会阻塞 UI 的渲染，导致用户体验下降*。例如，在一个搜索框组件中，用户输入内容后，可能需要立即发送网络请求来获取搜索结果，并且根据搜索结果更新组件的展示。如果这个网络请求或者结果处理过程比较复杂，可能会使得组件在请求处理期间无法响应用户其他操作，出现卡顿现象。`useDeferredValue`可以*将这些非关键的更新（如搜索结果的展示）延迟，先完成当前 UI 的渲染，保证用户可以继续进行其他操作，如继续输入搜索内容或者切换页面等，然后在空闲时间再更新延迟的值，这样就避免了阻塞 UI 渲染*。
    - **提升响应速度和交互流畅性**：对于一些复杂的 UI 组件，可能有多个状态更新同时发生，其中一些更新对于即时的用户交互不是那么关键。例如，在一个包含图表和数据表格的数据分析应用中，用户对图表的缩放操作可能会触发图表的重新渲染，同时数据表格也可能因为关联数据的更新而需要重新渲染。如果数据表格的重新渲染比较复杂，可以使用`useDeferredValue`将其延迟，优先完成图表的缩放操作和渲染，使得用户能够更快地看到图表的变化，提升交互的流畅性。


# What

`useDeferredValue`是 React 提供的一个 Hook，用于在不阻塞 UI 更新的情况下，延迟处理某些非关键部分的值更新。*它返回一个新的延迟值，这个值会在当前渲染完成后，在空闲时间（idle time）更新*。

# How
**基本语法**：

```jsx
const deferredValue = useDeferredValue(value, options);
```

- `value`是要延迟处理的值，通常是一个状态值或者计算结果。
- `options`是一个可选的配置对象，目前可以用于配置延迟更新的优先级等参数。

**示例**：

```jsx
import React, { useState, useDeferredValue } from'react';
function SearchComponent() {
  const [inputValue, setInputValue] = useState('');
  //
  const deferredInputValue = useDeferredValue(inputValue);
  // 模拟一个耗时的搜索结果处理函数
  const searchResults = useMemo(() => {
    // 假设这里是一个复杂的搜索结果处理，比如发送网络请求并处理数据
    console.log('处理搜索结果');
    return deferredInputValue.split(' ').map((word) => word +'结果');
  }, [deferredInputValue]);
  
  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
      <ul>
        {searchResults.map((result, index) => (
          <li key={index}>{result}</li>
        ))}
      </ul>
    </div>
  );
}
```

**示例说明**：

在这个`SearchComponent`中，有一个输入框，其值通过`inputValue`状态来管理。`useDeferredValue`用于创建一个延迟的值`deferredInputValue`。在`searchResults`的计算中，使用了`deferredInputValue`，并且通过`useMemo`来缓存计算结果。*当用户在输入框中输入内容时，`inputValue`会立即更新，但是`deferredInputValue`会延迟更新*。*这样，在用户输入过程中，组件可以快速响应用户的输入操作（如更新输入框的显示）*，**而`searchResults`的处理（模拟为一个复杂的搜索结果处理）会在空闲时间进行**，避免了因为复杂的搜索结果处理而阻塞 UI 渲染，提升了用户体验。