If you are using React or learning React, you must have heard of the term “Virtual DOM”. Now what is a Virtual DOM, and why does React use it?  
如果你正在使用 React 或者正在学习 React，你一定听说过“Virtual DOM”这个词。现在什么是虚拟 DOM，为什么 React 使用它？

# Real DOM 

First things first, DOM stands for “Document Object Model”. The DOM in simple words represents the UI of your application. Everytime there is a change in the state of your application UI, the DOM gets updated to represent that change. Now the catch is frequently manipulating the DOM affects performance, making it slow.  
首先，DOM 代表“文档对象模型”。简单来说，DOM 代表应用程序的 UI。每当应用程序 UI 的状态发生变化时，DOM 都会更新以表示该变化。现在的问题是频繁操作 DOM 会影响性能，使其变慢。

# What makes DOM manipulation slow?  
是什么导致 DOM 操作变慢？

The DOM is represented as a tree data structure. Because of that, the changes and updates to the DOM are fast. But after the change, the updated element and it’s children have to be re-rendered to update the application UI. The re-rendering or re-painting of the UI is what makes it slow. Therefore, the more UI components you have, the more expensive the DOM updates could be, since they would need to be re-rendered for every DOM update.  
DOM 表示为树形数据结构。因此，DOM 的更改和更新速度很快。但更改后，必须重新渲染更新的元素及其子元素才能更新应用程序 UI。 **UI 的重新渲染或重新绘制导致速度变慢**。**因此，拥有的 UI 组件越多，DOM 更新的成本就越高，因为每次 DOM 更新都需要重新渲染它们**。

# Virtual DOM 

That’s where the concept of virtual DOM comes in and performs significantly better than the real DOM. The virtual DOM is only a virtual representation of the DOM. ==Everytime the state of our application changes, the virtual DOM gets updated instead of the real DOM.==  
**这就是虚拟 DOM 概念的用武之地，它的性能明显优于真实 DOM**。虚拟 DOM 只是 DOM 的虚拟表示。==每当我们的应用程序的状态发生变化时，虚拟 DOM 而不是真实 DOM 就会更新。==

> _Well, you may ask ” Isn’t the virtual DOM doing the same thing as the real DOM, this sounds like double work? How can this be faster than just updating the real DOM?”  
> 好吧，你可能会问“虚拟 DOM 不是和真实 DOM 做同样的事情吗？这听起来像是双重工作吗？”这怎么可能比仅仅更新真实的 DOM 更快呢？”_

The answer is virtual DOM is much faster and efficient, here is why.  
答案是虚拟 DOM 更快、更高效，原因如下。

# How is Virtual DOM faster?  
虚拟 DOM 速度如何更快？

When new elements are added to the UI, a virtual DOM, which is represented as a tree is created. Each element is a node on this tree. If the state of any of these elements changes, a new virtual DOM tree is created. This tree is then compared or “diffed” with the previous virtual DOM tree.  
当新元素添加到 UI 时，会创建一个表示为树的虚拟 DOM。每个元素都是这棵树上的一个节点。**如果这些元素中的任何一个的状态发生变化，就会创建一个新的虚拟 DOM 树。然后将此树与先前的虚拟 DOM 树进行比较或“差异”**。

Once this is done, the virtual DOM calculates the best possible method to make these changes to the real DOM. This ensures that there are minimal operations on the real DOM. Hence, reducing the performance cost of updating the real DOM.  
**完成此操作后，虚拟 DOM 会计算对真实 DOM 进行这些更改的最佳方法。这确保了对真实 DOM 的操作最少。因此，降低了更新真实 DOM 的性能成本。**

The image below shows the virtual DOM tree and the diffing process.  
下图显示了虚拟 DOM 树和比较过程。

![[react/总结/media/76283c4c20fe227e6a5ad36939a61819_MD5.png]]

_source:_ [_https://www.oreilly.com/library/view/learning-react-native/9781491929049/ch02.html_](https://www.oreilly.com/library/view/learning-react-native/9781491929049/ch02.html)  

The red circles represent the nodes that have changed. These nodes represent the UI elements that have had their state changed. The difference between the previous version of the virtual DOM tree and the current virtual DOM tree is then calculated. The whole parent subtree then gets re-rendered to give the updated UI. This updated tree is then batch updated to the real DOM.  
**红色圆圈代表已更改的节点。这些节点代表状态已更改的 UI 元素。然后计算先前版本的虚拟 DOM 树和当前虚拟 DOM 树之间的差异。然后重新渲染整个父子树以提供更新的 UI。然后，这个更新后的树会批量更新到真实的 DOM。**

# How does React use Virtual DOM  
React 如何使用虚拟 DOM

Now that you have a fair understanding of what a Virtual DOM is, and how it can help with performance of your app, lets look into how React leverages the virtual DOM.  
现在您已经对虚拟 DOM 是什么以及它如何帮助提高应用程序的性能有了一定的了解，让我们看看 React 如何利用虚拟 DOM。

In React every UI piece is a component, and each component has a state. React follows the observable pattern and listens for state changes. When the state of a component changes, React updates the virtual DOM tree. Once the virtual DOM has been updated, React then compares the current version of the virtual DOM with the previous version of the virtual DOM. This process is called **“diffing”**.  
在 React 中，每个 UI 部分都是一个组件，每个组件都有一个状态。 React 遵循可观察模式并监听状态变化。*当组件的状态发生变化时，React 会更新虚拟 DOM 树。一旦虚拟 DOM 更新，React 就会将虚拟 DOM 的当前版本与虚拟 DOM 的先前版本进行比较*。这个过程称为 **“差异”**。

Once React knows which virtual DOM objects have changed, then React updates **only** those objects, in the real DOM. This makes the performance far better when compared to manipulating the real DOM directly. This makes React standout as a high performance JavaScript library.  
一旦 React 知道哪些虚拟 DOM 对象发生了变化，React 就**只**更新真实 DOM 中的那些对象。与直接操作真实 DOM 相比，这使得性能要好得多。这使得 React 作为高性能 JavaScript 库脱颖而出。

> _In simple words, you tell React what state you want the UI to be in, and it makes sure that the DOM matches that state. The great benefit here is that as a developer, you would not need to know how the attribute manipulation, event handling or the manual DOM updates happen behind the scenes.  
> 简而言之，您告诉 React 您希望 UI 处于什么状态，并且它确保 DOM 与该状态匹配。这里的最大好处是，作为开发人员，您不需要知道属性操作、事件处理或手动 DOM 更新是如何在幕后发生的。_

All of these details are abstracted away from React developers. All you need to do is update the states of your component as and when needed and React takes care of the rest. This ensures a superior developer experience when using React.  
所有这些细节都是从 React 开发人员那里抽象出来的。**您所需要做的就是在需要时更新组件的状态，React 会处理剩下的事情。这确保了开发人员在使用 React 时获得卓越的体验**。

# React render() function

_render()_ is where the UI gets updated and rendered. _render()_ is the required lifecycle method in React. You can learn more about React lifecycle methods in detail from my [blog post](https://programmingwithmosh.com/javascript/react-lifecycle-methods/).  
_render()_ 是 UI 更新和渲染的地方。 _render()_ 是 React 中必需的生命周期方法。您可以从我的[博文](https://programmingwithmosh.com/javascript/react-lifecycle-methods/)中详细了解 React 生命周期方法。

_render()_ function is the point of entry where the tree of React elements are created. When a _state_or _prop_ within the component is updated, the _render()_ will return a different tree of React elements. If you use _setState()_ within the component, React immediately detects the state change and re-renders the component.  
_render()_ 函数是创建 React 元素树的入口点。当组件内的 _state_ 或 _prop_ 更新时， _render()_ 将返回不同的 React 元素树。如果您在组件内使用 _setState()_ ，React 会立即检测到状态更改并重新渲染组件。

React then figures out how to efficiently update the UI to match the most recent tree changes.  
然后 React 找出如何有效地更新 UI 以匹配最近的树更改。

This is when React updates its virtual DOM first and updates only the object that have changed in the real DOM.  
这是 React 首先更新其虚拟 DOM 并仅更新真实 DOM 中已更改的对象的情况。

**Batch Update**

批量更新

React follows a batch update mechanism to update the real DOM. Hence, leading to increased performance. This means that updates to the real DOM are sent in batches, instead of sending updates for every single change in state.  
**React遵循批量更新机制来更新真实的DOM**。因此，导致性能提高。这意味着对真实 DOM 的更新是批量发送的，而不是针对每个状态更改发送更新。

The repainting of the UI is the most expensive part, and React efficiently ensures that the real DOM receives only batched updates to repaint the UI.  
UI 的重绘是最昂贵的部分，React 有效地确保真实 DOM 仅接收批量更新来重绘 UI。

# Recap 回顾

- Frequent DOM manipulations are expensive and performance heavy.  
    频繁的 DOM 操作成本高昂且性能较高。
- Virtual DOM is a virtual representation of the real DOM.  
    虚拟 DOM 是真实 DOM 的虚拟表示。
- When state changes occur, the virtual DOM is updated and the previous and current version of virtual DOM is compared. This is called “diffing”.  
    当状态发生变化时，虚拟 DOM 会更新，并且会比较虚拟 DOM 的先前版本和当前版本。这称为“差异”。
- The virtual DOM then sends a batch update to the real DOM to update the UI.  
    然后，虚拟 DOM 向真实 DOM 发送批量更新以更新 UI。
- React uses virtual DOM to enhance its performance.  
    React 使用虚拟 DOM 来增强其性能。
- It uses the observable to detect state and prop changes.  
    它使用 observable 来检测状态和 prop 的变化。
- React uses an efficient diff algorithm to compare the versions of virtual DOM.  
    React 使用高效的 diff 算法来比较虚拟 DOM 的版本。
- It then makes sure that batched updates are sent to the real DOM for repainting or re-rendering of the UI.  
    然后，它确保将批量更新发送到真实 DOM，以重新绘制或重新渲染 UI。