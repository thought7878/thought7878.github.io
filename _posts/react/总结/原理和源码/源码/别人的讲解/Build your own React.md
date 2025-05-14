自己动手构建 React

We are going to rewrite React from scratch. Step by step. Following the architecture from the real React code but without all the optimizations and non-essential features.  
我们将从头开始重写 React。一步一步地。_遵循真实 React 代码的架构，但省略所有优化和非必要的功能_。

If you’ve read any of [my previous “build your own React” posts](https://medium.com/hexacta-engineering/didact-learning-how-react-works-by-building-it-from-scratch-51007984e5c5), the difference is that this post is based on React 16.8, so we can now use hooks and drop all the code related to classes.  
如果你读过我之前的任何一篇“自己动手构建 React”的文章，那么这次的不同之处在于，**这篇文章基于 React 16.8**，因此我们现在可以使用 hooks，并可以删除所有与类相关的代码。

You can find the history with the old blog posts and the code on the [Didact repo](https://github.com/pomber/didact). There’s also a [talk covering the same content](https://youtu.be/8Kc2REHdwnQ). But this is a self-contained post.  
你可以在旧博客文章和 Didact 代码库中找到历史记录。还有一个涵盖相同内容的演讲。但这是一篇自成一体的文章。

Starting from scratch, these are all the things we’ll add to our version of React one by one:  
从头开始，我们将逐一添加到我们版本的 React 中：

- **Step I**: The `createElement` Function  
   第一步： `createElement`  函数
- **Step II**: The `render` Function  
   第二步： `render`  函数
- **Step III**: Concurrent Mode  
   第三步：并发模式
- **Step IV**: Fibers  
  步骤四：Fibers
- **Step V**: Render and Commit Phases  
   步骤 V：渲染和提交阶段
- **Step VI**: Reconciliation  
  步骤六：Reconciliation
- **Step VII**: Function Components  
   步骤 VII：函数组件
- **Step VIII**: Hooks  
  步骤八：Hooks

### Step Zero: Review  

步骤零：Review

```jsx
const element = <h1 title="foo">Hello</h1>;
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

![[_posts/react/总结/原理和源码/源码/别人的讲解/media/8b11e9e58d2c9579fbbcccba56d1a6db_MD5.jpeg]]

But first let’s review some basic concepts. You can skip this step if you already have a good idea of how React, JSX and DOM elements work.  
但首先让我们复习一些基本概念。如果你已经对 React、JSX 和 DOM 元素的工作方式有很好的了解，可以跳过这一步。
We’ll use this React app, just three lines of code. The first one defines a React element. The next one gets a node from the DOM. The last one renders the React element into the container.  
我们将使用这个 React 应用，只有三行代码。第一行定义了一个 React 元素。下一行从 DOM 中获取一个节点。最后一行将 React 元素渲染到容器中。
**Let’s remove all the React specific code and replace it with vanilla JavaScript**.  
让我们移除所有 React 特定的代码，并用纯 JavaScript 来替换。

On the first line we have the element, defined with JSX. It isn’t even valid JavaScript, so in order to replace it with vanilla JS, first we need to replace it with valid JS.  
在第一行，我们有一个用 JSX 定义的元素。它甚至不是有效的 JavaScript，所以在用纯 JS 替换它之前，我们首先需要用有效的 JS 来替换它。
JSX is transformed to JS by build tools like Babel. The transformation is usually simple: replace the code inside the tags with a call to `createElement`, passing the tag name, the props and the children as parameters.  
**JSX 是由像 Babel 这样的构建工具转换为 JS 的**。转换通常很简单：用调用  `createElement`  来替换标签内的代码，将标签名、属性和子元素作为参数传递。
![[_posts/react/总结/原理和源码/源码/别人的讲解/media/f687e693beff6f157bc0865039a5a40e_MD5.jpeg]]

`React.createElement` creates an object from its arguments. Besides some validations, that’s all it does. So we can safely replace the function call with its output.  
**`React.createElement`  根据它的参数创建一个对象**。除了进行一些验证外，它就做这些。所以我们可以安全地用它的输出替换函数调用。

```js
const element = React.createElement(
  "h1",
  { title: "foo" },
  "Hello"
)
​
const container = document.getElementById("root")
ReactDOM.render(element, container)
```

And this is what an element is, an object with two properties: `type` and `props` (well, [it has more](https://github.com/facebook/react/blob/f4cc45ce962adc9f307690e1d5cfa28a288418eb/packages/react/src/ReactElement.js#L111), but we only care about these two).  
这就是这个元素是什么，**一个有两个属性的对象：** `type`  和  `props` （嗯，它有更多，但我们只关心这两个）。
The `type` is a string that specifies the type of the DOM node we want to create, it’s the `tagName` you pass to `document.createElement` when you want to create an HTML element. It can also be a function, but we’ll leave that for Step VII.  
**`type`  是一个字符串**，它指定了我们想要创建的 DOM 节点的类型，这是当你想要*创建 HTML 元素时传递给  `document.createElement`  的  `tagName`* 。它*也可以是一个函数*，但我们将把它留到第七步。
`props` is another object, it has all the keys and values from the JSX attributes. It also has a special property: `children`.  
**`props`  是另一个对象**，它包含 JSX 属性的所有键和值。它还有一个特殊属性： `children` 。
`children` in this case is a string, but it’s usually an array with more elements. That’s why elements are also trees.  
`children`  在这个情况下是一个字符串，但它通常是一个包含更多元素的数组。这就是为什么元素也是树的原因。
![[_posts/react/总结/原理和源码/源码/别人的讲解/media/7b4eeb96d649a0de3e713e1657404752_MD5.jpeg]]

The other piece of React code we need to replace is the call to `ReactDOM.render`.  
我们需要替换的另一段 React 代码是调用  `ReactDOM.render`  的地方。
`render` is where React changes the DOM, so let’s do the updates ourselves.  
`render`  是 React 修改 DOM 的地方，所以让我们自己来做更新。
![[_posts/react/总结/原理和源码/源码/别人的讲解/media/17573e0770d097548d054da342d40525_MD5.jpeg]]

First we create a node* using the element `type`, in this case `h1`.  
首先我们使用元素  `type`  创建一个节点*，在这种情况下是  `h1` 。

Then we assign all the element `props` to that node. Here it’s just the title.  
然后我们将元素  `props`  分配给该节点。这里只是标题。

\_\* To avoid confusion, I’ll use “element” to refer to React elements and “node” for DOM elements.

- 为了避免混淆，我将使用“元素”来指代 React 元素，使用“节点”来指代 DOM 元素。\_

Then we create the nodes for the children. We only have a string as a child so we create a text node.  
然后我们为子元素创建节点。我们只有一个字符串作为子元素，所以我们创建一个文本节点。

Using `textNode` instead of setting `innerText` will allow us to treat all elements in the same way later. Note also how we set the `nodeValue` like we did it with the `h1` title, it’s almost as if the string had `props: {nodeValue: "hello"}`.  
使用  `textNode`  而不是设置  `innerText`  将允许我们在之后以相同的方式处理所有元素。请注意，我们如何像我们处理  `h1`  标题一样设置  `nodeValue` ，几乎就像字符串有  `props: {nodeValue: "hello"}`  一样。

Finally, we append the `textNode` to the `h1` and the `h1` to the `container`.  
最后，我们将  `textNode`  附加到  `h1`  并将  `h1`  附加到  `container` 。

And now we have the same app as before, but without using React.  
现在我们有了和之前一样的应用，但这次没有使用 React。

```js
const element = (
  <div id="foo">
    <a>bar</a>
    <b />
  </div>
);
const container = document.getElementById("root");
ReactDOM.render(element, container);
```

### Step I: The `createElement` Function

第一步： `createElement`  函数

Let’s start again with another app. This time we’ll replace React code with our own version of React.  
让我们再次开始一个新的应用。这次我们将 React 代码替换为我们自己的 React 版本。

We’ll start by writing our own `createElement`.  
我们将从编写我们自己的  `createElement`  开始。

Let’s transform the JSX to JS so we can see the `createElement` calls.  
让我们将 JSX 转换为 JS，以便我们可以看到  `createElement`  调用。

As we saw in the previous step, an element is an object with `type` and `props`. The only thing that our function needs to do is create that object.  
正如我们在上一步中看到的，一个元素是一个具有  `type`  和  `props`  的对象。我们的函数唯一需要做的就是创建那个对象。

We use the *spread operator* for the `props` and the *rest parameter syntax* for the `children`, this way the `children` prop will always be an array.  
我们使用展开操作符来处理  `props` ，使用剩余参数语法来处理  `children` ，这样  `children` prop 总会是一个数组。

For example, `createElement("div")` returns:  
例如， `createElement("div")`  返回：

{

"type": "div",

"props": { "children": [] }

}

`createElement("div", null, a)` returns:   `createElement("div", null, a)`  返回：

{

"type": "div",

"props": { "children": [a] }

}

and `createElement("div", null, a, b)` returns:   并且  `createElement("div", null, a, b)`  返回：

{

"type": "div",

"props": { "children": [a, b] }

}

The `children` array could also contain primitive values like strings or numbers. So we’ll wrap everything that isn’t an object inside its own element and create a special type for them: `TEXT_ELEMENT`.  
第 0#数组也可以包含字符串或数字等原始值。所以我们将所有不是对象的内容都包裹在自己的元素中，并为他们创建一个特殊类型： `TEXT_ELEMENT` 。

_React doesn’t wrap primitive values or create empty arrays when there aren’t `children`, but we do it because it will simplify our code, and for our library we prefer simple code than performant code.  
React 不会包装原始值或在没有第 0#时创建空数组，但我们这样做是因为这将简化我们的代码，而且对于我们这个库来说，我们更倾向于简单的代码而不是性能优越的代码。_

We are still using React’s `createElement`.  
我们仍在使用 React 的  `createElement` 。

In order to replace it, let’s give a name to our library. We need a name that sounds like React but also hints its *didactic* purpose.  
为了替换它，让我们给我们的库起个名字。我们需要一个听起来像 React 但也暗示其教学目的的名字。

We’ll call it Didact.   我们将它称为 Didact。

But we still want to use JSX here. How do we tell babel to use Didact’s `createElement` instead of React’s?  
但我们仍然想在这里使用 JSX。我们如何告诉 babel 使用 Didact 的  `createElement`  而不是 React 的？

If we have a comment like this one, when babel transpiles the JSX it will use the function we define.  
如果我们有一个像这样的注释，当 babel 转换 JSX 时，它将使用我们定义的函数。

### Step II: The `render` Function

第二步： `render`  函数

Next, we need to write our version of the `ReactDOM.render` function.  
接下来，我们需要编写我们自己的  `ReactDOM.render`  函数。

For now, we only care about adding stuff to the DOM. We’ll handle updating and deleting later.  
目前，我们只关心将内容添加到 DOM 中。稍后我们会处理更新和删除。

We start by creating the DOM node using the element type, and then append the new node to the container.  
我们首先使用元素类型创建 DOM 节点，然后将新节点追加到容器中。

We recursively do the same for each child.  
我们递归地对每个子元素做同样的处理。

We also need to handle text elements, if the element type is `TEXT_ELEMENT` we create a text node instead of a regular node.  
我们还需要处理文本元素，如果元素类型是  `TEXT_ELEMENT` ，我们会创建一个文本节点而不是普通节点。

The last thing we need to do here is assign the element props to the node.  
最后我们需要做的是将元素属性分配给节点。

And that’s it. We now have a library that can render JSX to the DOM.  
这就行了。我们现在有一个可以将 JSX 渲染到 DOM 的库。

Give it a try on [codesandbox](https://codesandbox.io/s/didact-2-k6rbj).  
在 codesandbox 上试试。

### Step III: Concurrent Mode

步骤三：并发模式

But… before we start adding more code we need a refactor.  
但是……在我们开始添加更多代码之前，我们需要重构。

```
function createElement(type, props, ...children) {  return {    type,    props: {      ...props,      children: children.map(child =>        typeof child === "object"          ? child          : createTextElement(child)      ),    },  }}​function createTextElement(text) {  return {    type: "TEXT_ELEMENT",    props: {      nodeValue: text,      children: [],    },  }}​function render(element, container) {  const dom =    element.type == "TEXT_ELEMENT"      ? document.createTextNode("")      : document.createElement(element.type)​  const isProperty = key => key !== "children"  Object.keys(element.props)    .filter(isProperty)    .forEach(name => {      dom[name] = element.props[name]    })​  element.props.children.forEach(child =>    render(child, dom)  )​  container.appendChild(dom)}​const Didact = {  createElement,  render,}​/** @jsx Didact.createElement */const element = (  <div id="foo">    <a>bar</a>    <b />  </div>)const container = document.getElementById("root")Didact.render(element, container)
```

There’s a problem with this recursive call.  
**这个递归调用存在问题。**
Once we start rendering, we won’t stop until we have rendered the complete element tree. If the element tree is big, it may block the main thread for too long. And if the browser needs to do high priority stuff like handling user input or keeping an animation smooth, it will have to wait until the render finishes.  
**一旦开始渲染，我们将不会停止，直到渲染完整的元素树。如果元素树很大，它可能会阻塞主线程太长时间。如果浏览器需要处理高优先级的事情，比如处理用户输入或保持动画流畅，它将不得不等待渲染完成。**
So we are going to break the work into small units, and after we finish each unit we’ll let the browser interrupt the rendering if there’s anything else that needs to be done.  
**所以我们将把工作分解成小的单元，每完成一个单元，如果浏览器有其他需要做的事情，我们将允许浏览器中断渲染。**

We use `requestIdleCallback` to make a loop. You can think of `requestIdleCallback` as a `setTimeout`, but instead of us telling it when to run, the browser will run the callback when the main thread is idle.  
我们使用  `requestIdleCallback`  来创建一个循环。你可以把  `requestIdleCallback`  看作是一个  `setTimeout` ，但不是由我们告诉它何时运行，而是**当主线程空闲时浏览器会运行回调**。
React [doesn’t use `requestIdleCallback` anymore](https://github.com/facebook/react/issues/11171#issuecomment-417349573). Now it uses the [scheduler package](https://github.com/facebook/react/tree/master/packages/scheduler). But for this use case it’s conceptually the same.  
_React 不再使用  `requestIdleCallback`  了。现在它使用调度器包_。但对于这个用例，在概念上是一样的。
`requestIdleCallback` also gives us a deadline parameter. We can use it to check how much time we have until the browser needs to take control again.  
`requestIdleCallback`  还给我们提供了一个截止日期参数。我们可以用它来检查浏览器再次需要控制之前还有多少时间。
As of November 2019, Concurrent Mode isn’t stable in React yet. The stable version of the loop looks more like this:  
截至 2019 年 11 月，React 中的并发模式（Concurrent Mode）尚未稳定。这个循环的稳定版本看起来更像是这样：

```js
while (nextUnitOfWork) {
  nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
}
```

To start using the loop we’ll need to set the first unit of work, and then write a `performUnitOfWork` function that not only performs the work but also returns the next unit of work.  
要开始使用循环，我们需要设置第一个工作单元，然后编写一个  `performUnitOfWork`  函数，该函数不仅执行工作，还返回下一个工作单元。

### Step IV: Fibers   步骤 IV: Fibers

To organize the units of work we’ll need a data structure: a fiber tree.  
**为了组织工作单元，我们需要一种数据结构：一个 fiber 树。**
We’ll have one fiber for each element and each fiber will be a unit of work.  
**每个元素有一个对应的 fiber，*每个 fiber 将是一个工作单元*。**

Let me show you with an example.  
让我举个例子给你看。

![[_posts/react/总结/原理和源码/源码/别人的讲解/media/7e28c45a17ef413a241614894ad0710d_MD5.png]]

![[_posts/react/总结/原理和源码/源码/别人的讲解/media/c10379616ff478356c35c1c2b8e349cb_MD5.png]]

Suppose we want to render an element tree like this one:  
假设我们想要渲染这样一个元素树：

```jsx
Didact.render(
  <div>
    <h1>
      <p />
      <a />
    </h1>
    <h2 />
  </div>,
  container
);
```

In the `render` we’ll create the root fiber and set it as the `nextUnitOfWork`. The rest of the work will happen on the `performUnitOfWork` function, there we will do three things for each fiber:  
在  `render`  中我们将创建根 fiber 并将其设置为  `nextUnitOfWork` 。其余的工作将在  `performUnitOfWork`  函数上完成，**在那里我们将为每个 fiber 做三件事：**

1. add the element to the DOM  
   将元素添加到 DOM 中
2. create the fibers for the element’s children  
   为元素的子项创建纤维
3. select the next unit of work  
   选择下一个工作单元

One of the goals of this data structure is to make it easy to find the next unit of work. That’s why each fiber has a link to its first child, its next sibling and its parent.  
**这个数据结构的一个目标是要让它容易找到下一个工作单元**。这就是为什么每个 fiber 都有一个链接到它的第一个子节点、下一个兄弟节点和父节点的链接。
When we finish performing work on a fiber, if it has a `child` that fiber will be the next unit of work.  
**当我们完成对一个 fiber 的工作时，如果它有一个  `child` ，那么这个 fiber 将是下一个工作单元。**
From our example, when we finish working on the `div` fiber the next unit of work will be the `h1` fiber.  
*从我们的例子中，当我们完成对  `div` fiber 的工作后，下一个工作单元将是  `h1` fiber。*
If the fiber doesn’t have a `child`, we use the `sibling` as the next unit of work.  
**如果该 fiber 没有  `child` ，我们使用  `sibling`  作为下一个工作单元。**
For example, the `p` fiber doesn’t have a `child` so we move to the `a` fiber after finishing it.  
*例如， `p`  纤维没有  `child` ，所以我们完成它后移动到  `a`  纤维。*
And if the fiber doesn’t have a `child` nor a `sibling` we go to the “uncle”: the `sibling` of the `parent`. Like `a` and `h2` fibers from the example.  
**如果纤维既没有  `child`  也没有  `sibling` ，我们就去找“叔叔”： `parent`  的  `sibling` 。就像例子中的  `a`  和  `h2`  纤维。**
Also, if the `parent` doesn’t have a `sibling`, we keep going up through the `parent`s until we find one with a `sibling` or until we reach the root. If we have reached the root, it means we have finished performing all the work for this `render`.  
**此外，如果  `parent`  没有  `sibling` ，我们就继续向上通过  `parent`  直到找到一个有  `sibling`  的，或者直到我们到达根。如果我们已经到达了根，这意味着我们已经完成了这个  `render`  的所有工作。**

Now let’s put it into code.  
现在让我们把它写成代码。

```
function createElement(type, props, ...children) {  return {    type,    props: {      ...props,      children: children.map(child =>        typeof child === "object"          ? child          : createTextElement(child)      ),    },  }}​function createTextElement(text) {  return {    type: "TEXT_ELEMENT",    props: {      nodeValue: text,      children: [],    },  }}​function render(element, container) {  const dom =    element.type == "TEXT_ELEMENT"      ? document.createTextNode("")      : document.createElement(element.type)​  const isProperty = key => key !== "children"  Object.keys(element.props)    .filter(isProperty)    .forEach(name => {      dom[name] = element.props[name]    })​  element.props.children.forEach(child =>    render(child, dom)  )​  container.appendChild(dom)}​let nextUnitOfWork = null​function workLoop(deadline) {  let shouldYield = false  while (nextUnitOfWork && !shouldYield) {    nextUnitOfWork = performUnitOfWork(      nextUnitOfWork    )    shouldYield = deadline.timeRemaining() < 1  }  requestIdleCallback(workLoop)}​requestIdleCallback(workLoop)​function performUnitOfWork(nextUnitOfWork) {  // TODO}​const Didact = {  createElement,  render,}​/** @jsx Didact.createElement */const element = (  <div id="foo">    <a>bar</a>    <b />  </div>)const container = document.getElementById("root")Didact.render(element, container)
```

First, let’s remove this code from the `render` function.  
首先，让我们从  `render`  函数中移除这段代码。

We keep the part that creates a DOM node in its own function, we are going to use it later.  
我们把创建 DOM 节点的部分放在自己的函数中，稍后我们会使用它。

In the `render` function we set `nextUnitOfWork` to the root of the fiber tree.  
在  `render`  函数中，我们将  `nextUnitOfWork`  设置为 fiber 树的根。

Then, when the browser is ready,it will call our `workLoop` and we’ll start working on the root.  
然后，当浏览器准备好时，它会调用我们的  `workLoop` ，然后我们将开始处理根节点。

First, we create a new node and append it to the DOM.  
首先，我们创建一个新的节点并将其添加到 DOM 中。

We keep track of the DOM node in the `fiber.dom` property.  
我们在  `fiber.dom`  属性中跟踪 DOM 节点。

Then for each child we create a new fiber.  
然后对于每个子元素，我们创建一个新的 fiber。

And we add it to the fiber tree setting it either as a child or as a sibling, depending on whether it’s the first child or not.  
然后我们将其添加到 fiber 树中，根据它是否是第一个子元素，将其设置为子元素或兄弟元素。

Finally we search for the next unit of work. We first try with the child, then with the sibling, then with the uncle, and so on.  
最后我们搜索下一个工作单元。我们首先尝试子节点，然后是兄弟节点，然后是叔节点，等等。

And that’s our `performUnitOfWork`.  
And that’s our `performUnitOfWork` .

### Step V: Render and Commit Phases

步骤 V：渲染和提交阶段

We have another problem here.  
我们在这里又有一个问题。

We are adding a new node to the DOM each time we work on an element. And, remember, the browser could interrupt our work before we finish rendering the whole tree. In that case, the user will see an incomplete UI. And we don’t want that.  
每次我们处理一个元素时，我们都在向 DOM 添加一个新的节点。记住，浏览器可能会在我们完成渲染整个树之前中断我们的工作。在这种情况下，用户会看到一个不完整的 UI。我们不希望这样。

So we need to remove the part that mutates the DOM from here.  
所以我们得从这里移除改变 DOM 的部分。

Instead, we’ll keep track of the root of the fiber tree. We call it the work in progress root or `wipRoot`.  
相反，我们将跟踪纤维树的根。我们称它为进行中的根或  `wipRoot` 。

And once we finish all the work (we know it because there isn’t a next unit of work) we commit the whole fiber tree to the DOM.  
一旦我们完成所有工作（我们知道这一点，因为不存在下一个工作单元），我们将整个 fiber 树提交到 DOM。

We do it in the `commitRoot` function. Here we recursively append all the nodes to the dom.  
我们在  `commitRoot`  函数中完成。在这里，我们递归地将所有节点附加到 dom 中。

### Step VI: Reconciliation   步骤六：Reconciliation

So far we only *added* stuff to the DOM, but what about updating or deleting nodes?  
到目前为止，我们只添加了内容到 DOM 中，但如何更新或删除节点呢？

That’s what we are going to do now, we need to compare the elements we receive on the `render` function to the last fiber tree we committed to the DOM.  
现在我们将做这个，我们需要比较我们在  `render`  函数中接收到的元素与最后提交到 DOM 的纤维树。

So we need to save a reference to that “last fiber tree we committed to the DOM” after we finish the commit. We call it `currentRoot`.  
所以我们需要在完成提交后保存对“最后提交到 DOM 的纤维树”的引用。我们称之为  `currentRoot` 。

We also add the `alternate` property to every fiber. This property is a link to the old fiber, the fiber that we committed to the DOM in the previous commit phase.  
我们还为每个纤维添加了  `alternate`  属性。这个属性是链接到旧的纤维，即我们在上一个提交阶段提交到 DOM 的纤维。

Now let’s extract the code from `performUnitOfWork` that creates the new fibers…  
现在让我们从  `performUnitOfWork`  中提取创建新纤维的代码…

…to a new `reconcileChildren` function.  
…到一个新的  `reconcileChildren`  函数。

Here we will reconcile the old fibers with the new elements.  
在这里我们将旧纤维与新元素进行协调。

We iterate at the same time over the children of the old fiber (`wipFiber.alternate`) and the array of elements we want to reconcile.  
我们同时遍历旧 fiber 的子元素 ( `wipFiber.alternate` ) 和我们要重新调和解的元素数组。

If we ignore all the boilerplate needed to iterate over an array and a linked list at the same time, we are left with what matters most inside this while: `oldFiber` and `element`. **The `element` is the thing we want to render to the DOM and the `oldFiber` is what we rendered the last time.**  
如果我们忽略所有同时迭代数组和链表所需的样板代码，我们就剩下 while 内部最重要的事情： `oldFiber`  和  `element` 。 `element`  是我们想要渲染到 DOM 的东西，而  `oldFiber`  是我们上次渲染的东西。

We need to compare them to see if there’s any change we need to apply to the DOM.  
我们需要将它们进行比较，看看是否需要对 DOM 进行任何更改。

To compare them we use the type:  
要比较它们，我们使用类型：

- if the old fiber and the new element have the same type, we can keep the DOM node and just update it with the new props  
   如果旧纤维和新元素具有相同的类型，我们可以保留 DOM 节点，并用新属性更新它
- if the type is different and there is a new element, it means we need to create a new DOM node  
   如果类型不同，并且有新元素，这意味着我们需要创建一个新的 DOM 节点
- and if the types are different and there is an old fiber, we need to remove the old node  
   如果类型不同并且存在旧 fiber，我们需要移除旧节点

_Here React also uses keys, that makes a better reconciliation. For example, it detects when children change places in the element array.  
这里 React 也使用 keys，这使得更好地 reconciliation。例如，它检测到当子元素在元素数组中位置变化时。_

When the old fiber and the element have the same type, we create a new fiber keeping the DOM node from the old fiber and the props from the element.  
当旧 fiber 和元素具有相同类型时，我们创建一个新的 fiber，保留旧 fiber 的 DOM 节点和元素的 props。

We also add a new property to the fiber: the `effectTag`. We’ll use this property later, during the commit phase.  
我们还在 fiber 中添加了一个新的属性： `effectTag` 。我们稍后，在提交阶段将使用这个属性。

Then for the case where the element needs a new DOM node we tag the new fiber with the `PLACEMENT` effect tag.  
然后对于元素需要一个新 DOM 节点的案例，我们将新 fiber 标记为  `PLACEMENT`  效果标签。

And for the case where we need to delete the node, we don’t have a new fiber so we add the effect tag to the old fiber.  
当我们需要删除节点时，我们没有新的 fiber，所以我们向旧的 fiber 添加 effect 标签。

But when we commit the fiber tree to the DOM we do it from the work in progress root, which doesn’t have the old fibers.  
但当我们将 fiber 树提交给 DOM 时，我们是从工作进行中的根提交的，它没有旧的 fibers。

So we need an array to keep track of the nodes we want to remove.  
所以我们需要一个数组来跟踪我们想要移除的节点。

And then, when we are commiting the changes to the DOM, we also use the fibers from that array.  
然后，当我们提交对 DOM 的更改时，我们也使用该数组中的 fiber。

Now, let’s change the `commitWork` function to handle the new `effectTags`.  
现在，让我们改变  `commitWork`  函数来处理新的  `effectTags` 。

If the fiber has a `PLACEMENT` effect tag we do the same as before, append the DOM node to the node from the parent fiber.  
如果 fiber 有一个  `PLACEMENT`  效果标签，我们执行之前相同的操作，将 DOM 节点附加到父 fiber 的节点上。

If it’s a `DELETION`, we do the opposite, remove the child.  
如果它是  `DELETION` ，我们做相反的操作，移除子元素。

And if it’s an `UPDATE`, we need to update the existing DOM node with the props that changed.  
如果它是  `UPDATE` ，我们需要用变化的属性更新现有的 DOM 节点。

We’ll do it in this `updateDom` function.  
我们将在这个  `updateDom`  函数中完成。

We compare the props from the old fiber to the props of the new fiber, remove the props that are gone, and set the props that are new or changed.  
我们将旧 fiber 的 props 与新 fiber 的 props 进行比较，移除已消失的 props，并设置新出现或发生变化的 props。

One special kind of prop that we need to update are event listeners, so if the prop name starts with the “on” prefix we’ll handle them differently.  
我们需要更新的特殊类型的属性是事件监听器，所以如果属性名以“on”前缀开头，我们将以不同的方式处理它们。

If the event handler changed we remove it from the node.  
如果事件处理器改变了，我们就把它从节点中移除。

And then we add the new handler.  
然后我们添加新的处理器。

Try the version with reconciliation on [codesandbox](https://codesandbox.io/s/didact-6-96533).  
在 codesandbox 上尝试带 reconciliation 的版本。

```
function createElement(type, props, ...children) {  return {    type,    props: {      ...props,      children: children.map(child =>        typeof child === "object"          ? child          : createTextElement(child)      ),    },  }}​function createTextElement(text) {  return {    type: "TEXT_ELEMENT",    props: {      nodeValue: text,      children: [],    },  }}​function createDom(fiber) {  const dom =    fiber.type == "TEXT_ELEMENT"      ? document.createTextNode("")      : document.createElement(fiber.type)​  updateDom(dom, {}, fiber.props)​  return dom}​const isEvent = key => key.startsWith("on")const isProperty = key =>  key !== "children" && !isEvent(key)const isNew = (prev, next) => key =>  prev[key] !== next[key]const isGone = (prev, next) => key => !(key in next)function updateDom(dom, prevProps, nextProps) {  //Remove old or changed event listeners  Object.keys(prevProps)    .filter(isEvent)    .filter(      key =>        !(key in nextProps) ||        isNew(prevProps, nextProps)(key)    )    .forEach(name => {      const eventType = name        .toLowerCase()        .substring(2)      dom.removeEventListener(        eventType,        prevProps[name]      )    })​  // Remove old properties  Object.keys(prevProps)    .filter(isProperty)    .filter(isGone(prevProps, nextProps))    .forEach(name => {      dom[name] = ""    })​  // Set new or changed properties  Object.keys(nextProps)    .filter(isProperty)    .filter(isNew(prevProps, nextProps))    .forEach(name => {      dom[name] = nextProps[name]    })​  // Add event listeners  Object.keys(nextProps)    .filter(isEvent)    .filter(isNew(prevProps, nextProps))    .forEach(name => {      const eventType = name        .toLowerCase()        .substring(2)      dom.addEventListener(        eventType,        nextProps[name]      )    })}​function commitRoot() {  deletions.forEach(commitWork)  commitWork(wipRoot.child)  currentRoot = wipRoot  wipRoot = null}​function commitWork(fiber) {  if (!fiber) {    return  }​  const domParent = fiber.parent.dom  if (    fiber.effectTag === "PLACEMENT" &&    fiber.dom != null  ) {    domParent.appendChild(fiber.dom)  } else if (    fiber.effectTag === "UPDATE" &&    fiber.dom != null  ) {    updateDom(      fiber.dom,      fiber.alternate.props,      fiber.props    )  } else if (fiber.effectTag === "DELETION") {    domParent.removeChild(fiber.dom)  }​  commitWork(fiber.child)  commitWork(fiber.sibling)}​function render(element, container) {  wipRoot = {    dom: container,    props: {      children: [element],    },    alternate: currentRoot,  }  deletions = []  nextUnitOfWork = wipRoot}​let nextUnitOfWork = nulllet currentRoot = nulllet wipRoot = nulllet deletions = null​function workLoop(deadline) {  let shouldYield = false  while (nextUnitOfWork && !shouldYield) {    nextUnitOfWork = performUnitOfWork(      nextUnitOfWork    )    shouldYield = deadline.timeRemaining() < 1  }​  if (!nextUnitOfWork && wipRoot) {    commitRoot()  }​  requestIdleCallback(workLoop)}​requestIdleCallback(workLoop)​function performUnitOfWork(fiber) {  if (!fiber.dom) {    fiber.dom = createDom(fiber)  }​  const elements = fiber.props.children  reconcileChildren(fiber, elements)​  if (fiber.child) {    return fiber.child  }  let nextFiber = fiber  while (nextFiber) {    if (nextFiber.sibling) {      return nextFiber.sibling    }    nextFiber = nextFiber.parent  }}​function reconcileChildren(wipFiber, elements) {  let index = 0  let oldFiber =    wipFiber.alternate && wipFiber.alternate.child  let prevSibling = null​  while (    index < elements.length ||    oldFiber != null  ) {    const element = elements[index]    let newFiber = null​    const sameType =      oldFiber &&      element &&      element.type == oldFiber.type​    if (sameType) {      newFiber = {        type: oldFiber.type,        props: element.props,        dom: oldFiber.dom,        parent: wipFiber,        alternate: oldFiber,        effectTag: "UPDATE",      }    }    if (element && !sameType) {      newFiber = {        type: element.type,        props: element.props,        dom: null,        parent: wipFiber,        alternate: null,        effectTag: "PLACEMENT",      }    }    if (oldFiber && !sameType) {      oldFiber.effectTag = "DELETION"      deletions.push(oldFiber)    }​    if (oldFiber) {      oldFiber = oldFiber.sibling    }​    if (index === 0) {      wipFiber.child = newFiber    } else if (element) {      prevSibling.sibling = newFiber    }​    prevSibling = newFiber    index++  }}​const Didact = {  createElement,  render,}​/** @jsx Didact.createElement */function App(props) {  return <h1>Hi {props.name}</h1>}const element = <App name="foo" />const container = document.getElementById("root")Didact.render(element, container)
```

### Step VII: Function Components

步骤 VII：函数组件

The next thing we need to add is support for function components.  
接下来我们需要添加对函数组件的支持。

First let’s change the example. We’ll use this simple function component, that returns an `h1` element.  
首先，让我们更改示例。我们将使用这个简单的函数组件，它返回一个  `h1`  元素。

Note that if we transform the jsx to js, it will be:  
注意，如果我们把 jsx 转换为 js，它将是：

function App(props) {

return Didact.createElement(

    "h1",

    null,

    "Hi ",

    props.name

)

}

const element = Didact.createElement(App, {

name: "foo",

})

Function components are differents in two ways:  
函数组件在两个方面有所不同：

- the fiber from a function component doesn’t have a DOM node  
   函数组件的 fiber 没有 DOM 节点
- and the children come from running the function instead of getting them directly from the `props`  
   子元素是从运行函数中获取的，而不是直接从  `props`  获取

We check if the fiber type is a function, and depending on that we go to a different update function.  
我们检查 fiber 类型是否为函数，根据这一点我们进入不同的更新函数。

In `updateHostComponent` we do the same as before.  
在  `updateHostComponent`  我们做和之前一样的事情。

And in `updateFunctionComponent` we run the function to get the children.  
在  `updateFunctionComponent`  中我们运行函数来获取子元素。

For our example, here the `fiber.type` is the `App` function and when we run it, it returns the `h1` element.  
在我们的示例中，这里的  `fiber.type`  是  `App`  函数，当我们运行它时，它返回  `h1`  元素。

Then, once we have the children, the reconciliation works in the same way, we don’t need to change anything there.  
然后，一旦我们有了子元素，重新渲染的工作方式相同，我们不需要在那里做任何改变。

What we need to change is the `commitWork` function.  
我们需要改变的是  `commitWork`  函数。

Now that we have fibers without DOM nodes we need to change two things.  
现在我们有了没有 DOM 节点的纤维，我们需要改变两件事。

First, to find the parent of a DOM node we’ll need to go up the fiber tree until we find a fiber with a DOM node.  
首先，要找到 DOM 节点的父节点，我们需要沿着 fiber 树向上遍历，直到找到一个带有 DOM 节点的 fiber。

And when removing a node we also need to keep going until we find a child with a DOM node.  
当删除一个节点时，我们也需要继续下去，直到找到一个带有 DOM 节点的子节点。

### Step VIII: Hooks   步骤八：Hooks

Last step. Now that we have function components let’s also add state.  
最后一步。既然我们已经有了函数组件，那么也让我们添加状态。

Let’s change our example to the classic counter component. Each time we click it, it increments the state by one.  
让我们将示例更改为经典的计数器组件。每次点击时，它都会将状态增加一。

Note that we are using `Didact.useState` to get and update the counter value.  
注意我们使用  `Didact.useState`  来获取和更新计数器值。

Here is where we call the `Counter` function from the example. And inside that function we call `useState`.  
这里是我们从示例中调用  `Counter`  函数的地方。在那个函数内部，我们调用  `useState` 。

We need to initialize some global variables before calling the function component so we can use them inside of the `useState` function.  
我们需要在调用函数组件之前初始化一些全局变量，以便在  `useState`  函数内部使用它们。

First we set the work in progress fiber.  
首先我们设置进行中的工作纤维。

We also add a `hooks` array to the fiber to support calling `useState` several times in the same component. And we keep track of the current hook index.  
我们还在纤维中添加一个  `hooks`  数组，以支持在同一个组件中多次调用  `useState` 。并且我们跟踪当前的钩子索引。

When the function component calls `useState`, we check if we have an old hook. We check in the `alternate` of the fiber using the hook index.  
当函数组件调用  `useState`  时，我们检查是否有一个旧的钩子。我们使用钩子索引在纤维的  `alternate`  中进行检查。

If we have an old hook, we copy the state from the old hook to the new hook, if we don’t we initialize the state.  
如果我们有旧的钩子，我们将旧钩子的状态复制到新钩子中，如果没有，我们将初始化状态。

Then we add the new hook to the fiber, increment the hook index by one, and return the state.  
然后我们将新的钩子添加到 fiber 中，将钩子索引加一，并返回状态。

`useState` should also return a function to update the state, so we define a `setState` function that receives an action (for the `Counter` example this action is the function that increments the state by one).  
`useState`  也应该返回一个更新状态的函数，所以我们定义一个  `setState`  函数，该函数接收一个动作（对于  `Counter`  示例，这个动作是使状态加一的函数）。

We push that action to a queue we added to the hook.  
我们将该操作推送到我们添加到钩子中的队列。

And then we do something similar to what we did in the `render` function, set a new work in progress root as the next unit of work so the work loop can start a new render phase.  
然后我们做了一些类似于  `render`  函数中做的事情，将一个新的工作进行中的根设置为下一个工作单元，以便工作循环可以开始一个新的渲染阶段。

But we haven’t run the action yet.  
但是我们还没有运行这个动作。

We do it the next time we are rendering the component, we get all the actions from the old hook queue, and then apply them one by one to the new hook state, so when we return the state it’s updated.  
我们在下次渲染组件时执行它，我们获取旧钩子队列中的所有动作，然后逐个将它们应用到新的钩子状态中，因此当我们返回状态时它是更新的。

And that’s all. We’ve built our own version of React.  
那就这样吧。我们已经构建了自己的 React 版本。

You can play with it on [codesandbox](https://codesandbox.io/s/didact-8-21ost) or [github](https://github.com/pomber/didact).  
您可以在 codesandbox 或 github 上尝试。

### Epilogue   后记

Besides helping you understand how React works, one of the goals of this post is to make it easier for you to dive deeper in the React codebase. That’s why we used the same variable and function names almost everywhere.  
除了帮助您理解 React 的工作原理之外，本文的另一个目标是为您更深入地探索 React 代码库提供便利。这就是为什么我们在几乎到处都使用了相同的变量和函数名称。

For example, if you add a breakpoint in one of your function components in a real React app, the call stack should show you:  
例如，如果您在真实 React 应用中的一个函数组件中添加了一个断点，调用栈应该会显示：

- `workLoop`
- `performUnitOfWork`
- `updateFunctionComponent`

We didn’t include a lot of React features and optimizations. For example, these are a few things that React does differently:  
我们没有包含很多 React 功能和优化。例如，以下是 React 的一些不同之处：

- In Didact, we are walking the whole tree during the render phase. React instead follows some hints and heuristics to skip entire sub-trees where nothing changed.  
   在 Didact 中，我们在渲染阶段遍历整个树。而 React 则遵循一些提示和启发式方法来跳过整个未发生变化的子树。
- We are also walking the whole tree in the commit phase. React keeps a linked list with just the fibers that have effects and only visit those fibers.  
   我们在提交阶段也在遍历整棵树。React 保留一个仅包含有副作用的 fiber 的链表，并且只访问这些 fiber。
- Every time we build a new work in progress tree, we create new objects for each fiber. React recycles the fibers from the previous trees.  
   每次我们构建一个新的工作进度树时，我们为每个 fiber 创建新的对象。React 会回收前一棵树中的 fibers。
- When Didact receives a new update during the render phase, it throws away the work in progress tree and starts again from the root. React tags each update with an expiration timestamp and uses it to decide which update has a higher priority.  
   当 Didact 在渲染阶段接收到新的更新时，它会丢弃工作进度树并从根节点重新开始。React 为每个更新标记过期时间戳，并使用它来决定哪个更新的优先级更高。
- And many more…   还有更多……

There are also a few features that you can add easily:  
还有几个功能可以轻松添加：

- use an object for the style prop  
   使用对象为样式属性
- [flatten children arrays   展平子元素数组](https://github.com/pomber/didact/issues/11)
- useEffect hook  useEffect 钩子
- reconciliation by key   通过键进行 reconciliation

If you add any of these or other features to Didact send a pull request to the [GitHub repo](https://github.com/pomber/didact), so others can see it.  
如果你添加了这些或其他功能到 Didact，请向 GitHub 仓库发送拉取请求，以便其他人可以看到。
