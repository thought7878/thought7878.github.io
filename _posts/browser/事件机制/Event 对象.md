在 JavaScript 中，`Event` 对象是所有事件对象的*基类*。每当用户与页面交互（如点击按钮、移动鼠标）或浏览器本身发生某些动作（如加载页面、触发定时器），都会创建一个 `Event` 对象，并将其传递给相应的事件处理程序。**这个对象包含了有关事件的各种信息**，例如事件类型、触发时间、事件目标等。

以下是 `Event` 对象的一些重要属性和方法：

### 属性

- **`type`**: 一个字符串，表示*事件的名称*。
- **`target`**: 引用*触发事件的实际 DOM 元素*。
- **`currentTarget`**: 引用*当前事件监听器附加到的元素*。在事件冒泡过程中，`target` 和 `currentTarget` 可能指向不同的元素。
- **`eventPhase`**: 表示事件流的当前阶段：捕获阶段（1）、处于目标（2）、冒泡阶段（3）。
- **`bubbles`**: 布尔值，表示该事件是否会在 DOM 树中冒泡。
- **`cancelable`**: 布尔值，表示是否可以调用 `preventDefault()` 来取消事件的默认行为。
- **`defaultPrevented`**: 返回一个布尔值，表示是否已经调用了 `preventDefault()`。
- **`timeStamp`**: 表示事件创建的时间戳。
- **`isTrusted`**: 布尔值，表示事件是由用户操作触发（true），还是由脚本创建（false）。

### 方法

- **`preventDefault()`**: 如果事件可取消，则调用此方法来阻止其默认行为。
- **`stopPropagation()`**: 阻止事件进一步传播到其他元素（阻止冒泡或捕获）。
- **`stopImmediatePropagation()`**: 不仅阻止事件传播，还阻止同一事件类型的其他监听器在同一元素上被调用。
- **`initEvent(typeArg, canBubbleArg, cancelableArg)`**: 初始化事件对象（不推荐使用，因为它是遗留 API）。

### 构造函数

- **`new Event(type, options)`**: 创建一个新的 `Event` 对象。`options` 是一个可选的对象，它可以包含两个布尔属性：`bubbles` 和 `cancelable`。

### 示例代码

```javascript
document.getElementById('myButton').addEventListener('click', function(event) {
    console.log('事件类型:', event.type);
    console.log('触发事件的元素:', event.target);
    console.log('当前事件监听器绑定的元素:', event.currentTarget);
    
    // 阻止默认行为（如果有的话）
    if (event.defaultPrevented !== true) { // 检查是否已经被阻止
        event.preventDefault();
    }
    
    // 阻止事件冒泡
    event.stopPropagation();
});
```

这段代码展示了如何访问 `Event` 对象的属性以及如何调用它的方法。当 `#myButton` 被点击时，它会打印出相关信息，并且阻止任何默认行为和事件冒泡。

`Event` 对象是一个基础类，许多特定类型的事件（如键盘事件 `KeyboardEvent`、鼠标事件 `MouseEvent` 等）都是基于 `Event` 类扩展而来，它们提供了额外的属性和方法以适应特定类型的交互。