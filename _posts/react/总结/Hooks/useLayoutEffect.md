# Why
- **确保 DOM 操作的同步性和即时性**
    - **读取和修改 DOM 布局**：*在某些场景下，需要在 DOM 更新后立即读取或修改 DOM 布局相关的信息*。例如，在一个自定义的拖放组件中，当一个元素被拖动时，可能需要立即获取其新的位置信息，并根据这个位置信息来调整其他元素的位置或者进行碰撞检测等操作。`useLayoutEffect`可以*保证在 DOM 更新完成后（例如，拖动元素的位置更新），立即执行相关的布局读取或修改操作*，**使得这些操作能够*在浏览器绘制之前*完成**，确保用户看到的是一个连贯、即时更新的界面。

    - **避免视觉闪烁**：如果使用`useEffect`来进行一些影响布局的 DOM 操作，**可能会导致视觉闪烁**。*因为`useEffect`是在浏览器绘制之后异步执行的，这可能会导致在浏览器已经绘制了初始状态后，又进行布局修改，从而产生闪烁现象*。**`useLayoutEffect`通过在绘制之前执行布局相关的操作，避免了这种视觉闪烁**，提供了更平滑的用户体验。

- **与第三方布局库的集成**
    - **确保库的正确操作顺序**：当使用第三方布局库时，这些库可能依赖于在正确的时机获取和修改 DOM 布局。例如，一个动画库可能需要在元素位置更新后立即获取其新的位置来计算动画的起始点。`useLayoutEffect`可以确保在 DOM 更新后，这些第三方库能够立即获取到准确的布局信息，从而正确地执行它们的功能，如正确地启动动画或者进行布局调整。

# What
`useLayoutEffect`是 React 提供的一个 Hook，它的功能与`useEffect`类似，都是用于处理副作用。不过，`useLayoutEffect`会*在 DOM 更新后同步执行，在浏览器进行绘制（paint）之前。这意味着它可以读取 DOM 布局并同步触发重排（reflow）*。

# How

**基本语法**：
```jsx
useLayoutEffect(() => { 
// 副作用逻辑 
}, [dependency1, dependency2,...]);
```

- 第一个参数是一个包含副作用逻辑的函数，与`useEffect`类似
- 第二个参数是一个依赖项数组。这个依赖项数组用于*控制副作用函数的执行时机*。
	- 如果依赖项数组为空（`[]`），副作用函数*只会在组件挂载和卸载时执行一次*；
	- 如果依赖项中有元素发生变化，副作用函数会在 DOM 更新后同步执行。

**示例**：

```jsx
import React, { useState, useLayoutEffect } from'react';
function ResizeComponent() {
  const [width, setWidth] = useState(window.innerWidth);
  //
  useLayoutEffect(() => {
    const handleResize = () => {
      setWidth(window.innerWidth);
    };
    window.addEventListener('resize', handleResize);
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);
  
  return (
    <div>
      <p>窗口宽度: {width}px</p>
    </div>
  );
}
```

**示例说明**：

在`ResizeComponent`中，`useLayoutEffect`用于监听浏览器窗口的大小变化。当组件挂载时（依赖项数组为空，所以只执行一次），它添加了一个`resize`事件监听器。在监听器函数中，通过`setWidth`更新组件的`width`状态来反映窗口的新宽度。因为`useLayoutEffect`是在 DOM 更新后同步执行的，所以当窗口大小改变时，`width`状态的更新和相关的 DOM 更新（展示新的宽度值）会在浏览器绘制之前完成，避免了可能出现的视觉闪烁。同时，在组件卸载时，通过返回的清理函数移除`resize`事件监听器，防止内存泄漏。