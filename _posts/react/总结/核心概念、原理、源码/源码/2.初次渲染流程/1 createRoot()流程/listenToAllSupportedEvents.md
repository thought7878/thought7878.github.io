我来为您解释这段代码，它是 React DOM 事件系统的一部分，位于 [listenToAllSupportedEvents](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-dom/src/events/DOMPluginEventSystem.js#L385-L413) 函数中。

这段代码的**作用**是*在根容器元素上监听所有受支持的原生浏览器事件*。

```javascript
export function listenToAllSupportedEvents(rootContainerElement: EventTarget) {
  // 检查根容器元素是否已经被标记为正在监听，防止重复监听
  if (!(rootContainerElement: any)[listeningMarker]) {
    (rootContainerElement: any)[listeningMarker] = true;
    
    // 遍历所有原生事件
    allNativeEvents.forEach(domEventName => {
      // selectionchange 事件需要单独处理，因为它不会冒泡，
      // 并且需要绑定到 document 上
      if (domEventName !== 'selectionchange') {
        // 如果不是非委托事件，则对所有事件既添加捕获阶段监听又添加冒泡阶段监听
        if (!nonDelegatedEvents.has(domEventName)) {
          // 添加捕获阶段监听（第三个参数 false 表示监听捕获阶段）
          listenToNativeEvent(domEventName, false, rootContainerElement);
        }
        // 添加冒泡阶段监听（第四个参数 true 表示监听冒泡阶段）
        listenToNativeEvent(domEventName, true, rootContainerElement);
      }
    });
    
    // 获取文档对象
    const ownerDocument =
      (rootContainerElement: any).nodeType === DOCUMENT_NODE
        ? rootContainerElement
        : (rootContainerElement: any).ownerDocument;
        
    if (ownerDocument !== null) {
      // selectionchange 事件也需要去重处理
      // 但它需要绑定到 document 上
      if (!(ownerDocument: any)[listeningMarker]) {
        (ownerDocument: any)[listeningMarker] = true;
        // 在 document 上监听 selectionchange 事件
        listenToNativeEvent('selectionchange', false, ownerDocument);
      }
    }
  }
}
```

这段代码的主要功能包括：

1. 首先检查根容器元素是否已经添加了事件监听标记，如果还没有，则开始监听过程
2. 为根容器元素添加标记，防止重复监听
3. 遍历所有原生事件类型，对大部分事件添加捕获和冒泡两个阶段的监听
4. 对于非委托事件（nonDelegatedEvents），只在冒泡阶段监听
5. 特别处理 `selectionchange` 事件，因为它不会冒泡，需要专门在文档对象上监听
6. 确保文档对象也只被添加一次监听器

这种设计允许 React 在根级别集中处理所有事件，利用事件冒泡机制来处理子元素的事件，这是 React 事件系统的核心原理之一。通过这种方式，React 可以实现事件委托，提高性能并提供统一的事件处理接口。