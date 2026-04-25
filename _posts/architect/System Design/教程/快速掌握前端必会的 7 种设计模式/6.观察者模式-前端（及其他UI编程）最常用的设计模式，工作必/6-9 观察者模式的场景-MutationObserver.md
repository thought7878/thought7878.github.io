本视频讲述了*MutationObserver API的原理与使用方法*，通过代码*演示了其作为前端观察者模式典型实现的监听、回调、配置及启停机制*，并强调其在DOM变化监控中的核心作用。

![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/6.观察者模式-前端（及其他UI编程）最常用的设计模式，工作必/media/400b149f028c6e3a2f8d37804ec32c7e_MD5.webp]]

## MutationObserver 概念
[00:03](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=3)

- **核心概念定义**  
    MutationObserver 是用于监控 DOM 变化的内置 API。
- **命名逻辑说明**  
    `observer` 即“观察者”，与设计模式中的“观察者模式”名称完全契合。

## MutationObserver 案例
![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/6.观察者模式-前端（及其他UI编程）最常用的设计模式，工作必/media/a19ad73fab5c740ce859b97534bb8c40_MD5.webp]]

### HTML 示例结构搭建 
[00:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=15)

- **页面元素初始化**  
    在 `index.html` 中创建 `<div id="container">`，内部包含两个 `<p>` 标签：`<p>A</p>` 和 `<p>B</p>`。

### TypeScript 回调函数定义 
[00:50](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=50)

- **回调函数签名**  
    `callback(records: MutationRecord[], observer: MutationObserver)`
- **参数类型说明**
    - `records`: `MutationRecord[]` 类型数组，由浏览器自动传入，记录所有发生的 DOM 变更。
    - `observer`: `MutationObserver` 类型，指向当前实例。
- **遍历与调试**  
    使用 `for (const record of records)` 遍历并 `console.log(record)` 输出每条变更记录。

### MutationObserver 实例创建 
[02:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=141)

- **实例化语法**  
    `const observer = new MutationObserver(callback)`
- **实例性质说明**  
    此为 `MutationObserver` 的一个具体实例，尚未启动监听。

### 目标元素获取与监听启动 
[02:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=162)

- **元素定位**  
    `const container = document.getElementById('container')`
- **监听触发条件**  
    若 `container` 存在，则执行 `observer.observe(container, config)` 启动监听。

### 监听配置对象详解 
[03:19](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=199)

- **配置项结构**  
    第二个参数为配置对象，控制监听粒度与范围。
- **`attributes: true`**  
    监听目标元素自身属性（如 `class`、`id`、`style`）的变化。
- **`attributeOldValue: true`**  
    若启用，`MutationRecord` 中将包含 `oldValue` 字段，记录属性变更前的旧值。
- **`childList: true`**  
    监听子节点的增删（如 `appendChild`、`removeChild`）。
- **`characterData: true`**  
    监听文本节点内容变化（适用于 `<span>`、`<b>` 等仅含文本的元素）。
- **`subtree: true`**  
    递归监听目标元素及其所有后代节点；若为 `false`（默认），仅监听目标元素直接子节点。

### 监听生命周期控制 
[06:09](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=369)

- **停止监听**  
    `observer.disconnect()` —— 暂停所有监听，不销毁实例。
- **重启监听**  
    再次调用 `observer.observe(element, config)` 即可恢复监听。
- **控制特性总结**  
    启动、暂停、重启全程可控，符合观察者模式动态管理特征。

![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/6.观察者模式-前端（及其他UI编程）最常用的设计模式，工作必/media/a19ad73fab5c740ce859b97534bb8c40_MD5.webp]]

### DOM 变更实测演示一：innerHTML 覆盖 
[07:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=435)

- **操作方式**  
    `container.innerHTML = '123'`
- **监听响应**  
    触发回调，`records.type === 'childList'`，`records.removedNodes` 包含被移除的全部原节点（A、B 对应的 `<p>` 元素）。

### DOM 变更实测演示二：appendChild 新增节点 
[08:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=495)

- **操作方式**
    ```ts
    const p1 = document.createElement('p');  
    p1.innerHTML = 'C';  
    container.appendChild(p1);
    ```
    
- **监听响应**  
    触发回调，`records.type === 'childList'`，`records.addedNodes` 包含新添加的 `<p>C</p>` 节点。

## 设计模式本质重申 
[09:59](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=599)

- **观察者模式验证**
    - 存在明确的“被观察目标”（DOM 元素）、“观察者实例”（`MutationObserver`）、“回调通知机制”（`callback`）。
    - 行为符合“一对多依赖关系，当目标状态改变时，所有依赖者自动得到通知并更新”。
- **前端广泛性佐证**
    - DOM 事件（如 `click`）、组件生命周期钩子（Vue `mounted`/`updated`）、`watch`、`Promise.then`、`setTimeout`、Node.js 流（`readline`）、HTTP Server 请求回调等，均属观察者模式变体。

## 后续学习指引 
[10:40](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=640)

- **模式对比预告**  
    下一环节将深入对比“观察者模式”与“发布-订阅模式”的异同，二者虽常被混用，但在解耦程度与中介角色上存在本质差异。