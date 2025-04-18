
通过对上一个课时的学习，你已经对 React 15 的生命周期有了系统的掌握和理解。

本课时，我将在此基础上，*对 React 16 以来的生命周期进行剖析*。在理解“是什么”的基础上，*对比新旧两个版本生命周期之间的差异，并探寻变化背后的原因*。*将明白 React 团队“动作频频”背后的思量与野心，同时也将对时下大热的 Fiber 架构形成初步的认知*。

### 进化的生命周期方法：React 16 生命周期工作流详解

关于 React 16 以来的生命周期，[这个民间开源项目](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)为我们提供了目前公认的比较优秀的流程大图（在下不才，自己动手画了半天仍然自觉无法超越下图，所以这里就直接引用过来辅助讲解）。我们先来看 React 16.3 的大图：

![Drawing 0.png](/assets/images/react/CgqCHl-FVVeAaMJvAAKXOyLlUwM592.png)

这里之所以特意将版本号精确到了小数点后面一位，是因为在**React 16.4**之后，React 生命周期在之前版本的基础上又经历了一次微调。不过你先不用着急，在理解 16.3 生命周期的基础上，掌握这个“微调”对你来说将易如反掌。

接下来，我会先把上面这张 React 16.3 生命周期大图中所涉及的内容讲清楚，然后再对 16.4 的改动进行介绍。还是老规矩，这里我先提供一个 Demo，它将辅助你理解新的生命周期。Demo 代码如下：

```jsx
import React from "react";
import ReactDOM from "react-dom";
// 定义子组件
class LifeCycle extends React.Component {
  constructor(props) {
    console.log("进入constructor");
    super(props);
    // state 可以在 constructor 里初始化
    this.state = { text: "子组件的文本" };
  }
  // 初始化/更新时调用
  static getDerivedStateFromProps(props, state) {
    console.log("getDerivedStateFromProps方法执行");
    return {
      fatherText: props.text
    }
  }
  // 初始化渲染时调用
  componentDidMount() {
    console.log("componentDidMount方法执行");
  }
  // 组件更新时调用
  shouldComponentUpdate(prevProps, nextState) {
    console.log("shouldComponentUpdate方法执行");
    return true;
  }

  // 组件更新时调用
  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log("getSnapshotBeforeUpdate方法执行");
    return "haha";
  }
  // 组件更新后调用
  componentDidUpdate(preProps, preState, valueFromSnapshot) {
    console.log("componentDidUpdate方法执行");
    console.log("从 getSnapshotBeforeUpdate 获取到的值是", valueFromSnapshot);
  }
  // 组件卸载时调用
  componentWillUnmount() {
    console.log("子组件的componentWillUnmount方法执行");
  }
  // 点击按钮，修改子组件文本内容的方法
  changeText = () => {
    this.setState({
      text: "修改后的子组件文本"
    });
  };
  render() {
    console.log("render方法执行");
    return (
      <div className="container">
        <button onClick={this.changeText} className="changeText">
          修改子组件文本内容
        </button>
        <p className="textContent">{this.state.text}</p>
        <p className="fatherContent">{this.props.text}</p>
      </div>
    );
  }
}
// 定义 LifeCycle 组件的父组件
class LifeCycleContainer extends React.Component {

  // state 也可以像这样用属性声明的形式初始化
  state = {
    text: "父组件的文本",
    hideChild: false
  };
  // 点击按钮，修改父组件文本的方法
  changeText = () => {
    this.setState({
      text: "修改后的父组件文本"
    });
  };
  // 点击按钮，隐藏（卸载）LifeCycle 组件的方法
  hideChild = () => {
    this.setState({
      hideChild: true
    });
  };
  render() {
    return (
      <div className="fatherContainer">
        <button onClick={this.changeText} className="changeText">
          修改父组件文本内容
        </button>
        <button onClick={this.hideChild} className="hideChild">
          隐藏子组件
        </button>
        {this.state.hideChild ? null : <LifeCycle text={this.state.text} />}
      </div>
    );
  }
}
ReactDOM.render(<LifeCycleContainer />, document.getElementById("root"));
```

*React 16 以来的生命周期也可以按照“挂载”“更新”和“卸载”三个阶段*来看，所以接下来我们要做的事情仍然是分阶段拆解工作流程。在这个过程中，我将把 React 16 新增的生命周期方法，以及流程上相对于 React 15 产生的一些差异，作为我们学习的重点。对于和 React 15 保持一致的部分，这里不再重复讲解。

#### Mounting 阶段：组件的初始化渲染（挂载）

为了凸显 16 和 15 两个版本生命周期之间的差异，我将两个流程绘制到了同一张大图里，请看下面这张图：

![图片1.png](/assets/images/react/Ciqc1F-Klv6AIeOPAADAZZgLu7U105.png)

你现在可以打开开篇我给出的 Demo，将你的 React 版本更新到 16.3，然后运行这个项目，你就可以在控制台看到新的生命周期执行过程了。控制台的输出如图所示：

![Drawing 2.png](/assets/images/react/Ciqc1F-FVW6AAX_PAADMEGvjdFI487.png)

##### 消失的 componentWillMount，新增的 getDerivedStateFromProps

从上图中不难看出，React 15 生命周期和 React 16.3 生命周期在挂载阶段的主要差异在于，**废弃了 componentWillMount，新增了 getDerivedStateFromProps**。

> 注：细心的你可能记得，React 16 对 render 方法也进行了一些改进。React 16 之前，render 方法必须返回单个元素，而 <u>React 16 允许我们返回元素数组和字符串</u>。但本课时我们更加侧重讨论的是生命周期升级过程中的“主要矛盾”，也就是“工作流”层面的改变，故对现有方法的迭代细节，以及不在主要工作流里的[componentDidCatch](https://zh-hans.reactjs.org/docs/react-component.html#componentdidcatch) 等生命周期不再予以赘述。

一些同学在初次发现这个改变的时候，倾向于认为是 React 16.3 在试图用 getDerivedStateFromProps 代替 componentWillMount，这种想法是不严谨的。

##### getDerivedStateFromProps 不是 componentWillMount 的替代品

事实上，**componentWillMount 的存在不仅“鸡肋”而且危险，因此它并不值得被“代替”，它就应该被废弃。** 为了证明这点，我将在本文后续的“透过现象看本质”环节为大家细数 componentWillMount 的几宗“罪”。

**getDerivedStateFromProps 这个 API，其设计的初衷不是试图替换掉 componentWillMount，而是试图替换掉 componentWillReceiveProps**，因此它有且仅有一个用途：**使用 props 来派生/更新 state**。React 团队为了确保 getDerivedStateFromProps 这个生命周期的纯洁性，直接从命名层面约束了它的用途（getDerivedStateFromProps 直译过来就是“从 Props 里派生 State”）。所以，如果你不是出于这个目的来使用 getDerivedStateFromProps，原则上来说都是不符合规范的。

值得一提的是，getDerivedStateFromProps 在更新和挂载两个阶段都会“出镜”（这点不同于仅在更新阶段出现的 componentWillReceiveProps）。**这是因为“派生 state”这种诉求不仅在 props 更新时存在，在 props 初始化的时候也是存在的**。React 16 以提供特定生命周期的形式，对这类诉求提供了更直接的支持。

由此看来，挂载阶段的生命周期改变，并不是一个简单的“替换”逻辑，而是一个雄心勃勃的“进化”逻辑。

##### getDerivedStateFromProps

这个新生命周期方法的调用规则如下：

```js
static getDerivedStateFromProps(props, state)
```

在使用层面，你需要把握三个重点。

<u>第一个重点</u>是最特别的一点：**getDerivedStateFromProps 是一个静态方法**。静态方法不依赖组件实例而存在，因此你在这个方法内部是**访问不到 this** 的。若你偏要尝试这样做，必定报错，报错形式如下图所示：

![Drawing 3.png](/assets/images/react/CgqCHl-FVZSAX16PAAK3atPnbSg411.png)

<u>第二个重点</u>，**该方法可以接收两个参数**：props 和 state，它们分别<u>代表当前组件接收到的来自父组件的 props 和当前组件自身的 state</u>。我们可以尝试在 Demo 中输出这两个参数看一看，输出效果如下图所示：

![Drawing 4.png](/assets/images/react/CgqCHl-FVZqAJnD-AAQlZUXOgq0760.png)

可以看出，挂载阶段输出的 props 正是初始化阶段父组件传进来的 this.props 对象；而 state 是 LifeCycle 组件自身的 state 对象。

<u>第三个重点</u>，**getDerivedStateFromProps 需要一个对象格式的返回值**。如果你没有指定这个返回值，那么大概率会被 React 警告一番，警告内容如下图所示：

![Drawing 5.png](/assets/images/react/Ciqc1F-FVaCAOOnzAALVyD02cdg817.png)

**getDerivedStateFromProps 的返回值之所以不可或缺，是因为 React 需要用这个返回值来更新（派生）组件的 state**。因此当你确实不存在“使用 props 派生 state ”这个需求的时候，最好是直接省略掉这个生命周期方法的编写，*否则一定记得给它 return 一个 null*。

注意，**getDerivedStateFromProps 方法对 state 的更新动作并非“覆盖”式的更新**，**而是针对某个属性的定向更新**。比如这里我们在 getDerivedStateFromProps 里返回的是这样一个对象，对象里面有一个 fatherText 属性用于表示“父组件赋予的文本”：

```js
{
  fatherText: props.text
}
```

该对象并不会替换掉组件原始的这个 state：

```jsx
this.state = { text: "子组件的文本" };
```

而是仅仅针对 fatherText 这个属性作更新（这里原有的 state 里没有 fatherText，因此直接新增）。更新后，原有属性与新属性是共存的，如下图所示：

![Drawing 6.png](/assets/images/react/CgqCHl-FVbiAR1FtAABja-0bwL0578.png)

#### Updating 阶段：组件的更新

React 15 与 React 16.3 的更新流程对比如下图所示：

![图片2.png](/assets/images/react/CgqCHl-KlxyAB5MpAAFaH-Kgggo887.png)

注意，咱们前面提到 React 16.4 对生命周期流程进行了“微调”，其实就调在了更新过程的 getDerivedStateFromProps 这个生命周期上。先来看一张 React 16.4+ 的生命周期大图（出处仍然是[Wojciech Maj 的 react-lifecycle-methods-diagram](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)）：

![Drawing 8.png](/assets/images/react/Ciqc1F-FVcSALRwNAAIomWwVcQU231.png)

React 16.4 的挂载和卸载流程都是与 React 16.3 保持一致的，差异在于更新流程上：

- 在 React 16.4 中，**任何因素触发的组件更新流程**（包括由 this.setState 和 forceUpdate 触发的更新流程）都会触发 getDerivedStateFromProps；
- 而在 v 16.3 版本时，**只有父组件的更新**会触发该生命周期。

到这里，你已经对 getDerivedStateFromProps 相关的改变有了充分的了解。接下来，我们就基于这层了解，问出生命周期改变背后的第一个“Why”。

##### 改变背后的第一个“Why”：为什么要用 getDerivedStateFromProps 代替 componentWillReceiveProps？

对于 getDerivedStateFromProps 这个 API，React 官方曾经给出过这样的描述：

> 与 componentDidUpdate 一起，这个新的生命周期涵盖过时 componentWillReceiveProps 的所有用例。

在这里，请你细细品味这句话，这句话里蕴含了下面两个关键信息：

- getDerivedStateFromProps 是作为一个**试图代替 componentWillReceiveProps** 的 API 而出现的；
- getDerivedStateFromProps**不能完全和 componentWillReceiveProps 画等号**，其特性决定了我们曾经在 componentWillReceiveProps 里面做的事情，不能够百分百迁移到 getDerivedStateFromProps 里。

接下来我们就展开说说这两点。

- 关于 getDerivedStateFromProps 是如何代替 componentWillReceiveProps 的，在“挂载”环节已经讨论过：getDerivedStateFromProps 可以代替 componentWillReceiveProps 是**基于 props 派生 state**。
- 至于它为何不能完全和 componentWillReceiveProps 画等号，则是因为它过于“专注”了。这一点，单单从 getDerivedStateFromProps 这个 API 名字上也能够略窥一二。原则上来说，它能做且只能做这一件事。

乍一看，原来的 API 能做的事情更多，现在的 API 却限制重重，难道是 React 16 的生命周期方法“退化”了？当然不是。如果你对设计模式有所了解的话，就会知道，*一个 API 并非越庞大越复杂才越优秀，庞大和复杂的 API 往往会带来维护层面的“灾难”*。

说回 getDerivedStateFromProps 这个 API，它相对于早期的 componentWillReceiveProps 来说，正是做了“**合理的减法**”。而做这个减法的决心之强烈，从 getDerivedStateFromProps 直接被定义为 static 方法这件事上就可见一斑—— *static 方法内部拿不到组件实例的 this，这就导致你无法在 getDerivedStateFromProps 里面做任何类似于 this.fetch()、不合理的 this.setState（会导致死循环的那种）这类可能会产生副作用的操作*。

因此，getDerivedStateFromProps 生命周期替代 componentWillReceiveProps 的背后，**是 React 16 在强制推行“只用 getDerivedStateFromProps 来完成 props 到 state 的映射”这一最佳实践**。<u>意在确保生命周期函数的行为更加可控可预测，从根源上帮开发者避免不合理的编程方式，避免生命周期的滥用；同时，也是在为新的 Fiber 架构铺路</u>。

到这里，相信你已经对 getDerivedStateFromProps 吃得透透的了。至于什么是 Fiber 架构，这条路该怎么铺，你将在本课时后续的内容中找到答案。现在，我们得回到“更新”这条工作流里来，一起去看看 getSnapshotBeforeUpdate 是怎么一回事儿。

##### 消失的 componentWillUpdate 与新增的 getSnapshotBeforeUpdate

咱们先来看看 getSnapshotBeforeUpdate 是什么：

```jsx
getSnapshotBeforeUpdate(prevProps, prevState) {
  // ...
}
```

这个方法和 getDerivedStateFromProps 颇有几分神似，它们都强调了“我需要一个返回值”这回事。区别在于 **getSnapshotBeforeUpdate 的返回值会作为第三个参数给到 componentDidUpdate**。**它的执行时机是在 render 方法之后，真实 DOM 更新之前**。在这个阶段里，我们可以**同时获取到更新前的真实 DOM 和更新前后的 state&props 信息**。

尽管在实际工作中，需要用到这么多信息的场景并不多，但在对于实现一些特殊的需求来说，没它还真的挺难办。这里我举**一个非常有代表性的例子**：实现一个内容会发生变化的滚动列表，要求根据滚动列表的内容是否发生变化，来决定是否要记录滚动条的当前位置。这个需求的前半截要求我们对比更新前后的数据（感知变化），后半截则需要获取真实的 DOM 信息（获取位置），这时用 getSnapshotBeforeUpdate 来解决就再合适不过了。

对于这个生命周期，需要重点把握的是**它与 componentDidUpdate 间的通信过程**。在 Demo 中我给出了一个使用示例，它将帮助你更加具体地认知这个过程。代码如下：

```jsx
// 组件更新时调用
getSnapshotBeforeUpdate(prevProps, prevState) {
  console.log("getSnapshotBeforeUpdate方法执行");
  return "haha";
}

// 组件更新后调用
componentDidUpdate(prevProps, prevState, valueFromSnapshot) {
  console.log("componentDidUpdate方法执行");
  console.log("从 getSnapshotBeforeUpdate 获取到的值是", valueFromSnapshot);
}
```

现在我们点击 Demo 界面上“修改子组件文本内容”按钮，就可以看到这两个生命周期的通信效果，如下图所示：

![Drawing 9.png](/assets/images/react/Ciqc1F-FVlOAX7VMAAE_3SdYf2M700.png)

值得一提的是，这个生命周期的设计初衷，是为了“与 componentDidUpdate 一起，涵盖过时的 componentWillUpdate 的所有用例”（引用自 React 官网）。**getSnapshotBeforeUpdate 要想发挥作用，离不开 componentDidUpdate 的配合**。

那么换个角度想想，**为什么 componentWillUpdate 就非死不可呢**？说到底，还是因为它“挡了 Fiber 的路”。各位莫慌，咱们离真相越来越近了~

#### Unmounting 阶段：组件的卸载

我们先继续把完整的生命周期流程走完，以下是组件卸载阶段的示意图：

![图片3.png](/assets/images/react/Ciqc1F-KlzqACUOPAABE6JqN9E0200.png)

卸载阶段的生命周期与 React 15 完全一致，只涉及 componentWillUnmount 这一个生命周期，此处不再重复讲解。

接下来，就让一切变化背后的”始作俑者“ Fiber 架构来和大家打个招呼吧！

### 透过现象看本质：React 16 缘何两次求变？

##### Fiber 架构简析

Fiber 是 React 16 对 <u>React 核心算法</u>的一次重写。关于 Fiber，我将在“模块二：核心原理”花大量的篇幅来介绍它的原理和细节。在本课时，你只需要 get 到这一个点：**Fiber 会使原本同步的渲染过程变成异步的**。**在 React 16 之前，每当我们触发一次组件的更新，React 都会构建一棵新的虚拟 DOM 树，通过与上一次的虚拟 DOM 树进行 diff，实现对 DOM 的定向更新**。这个过程，是一个递归的过程。下面这张图形象地展示了这个过程的特征：

![图片4.png](/assets/images/react/Ciqc1F-Kl0WAO2mzAABxddWHnXI121.png)

###### Fiber要解决的问题

如图所示，**同步渲染的递归调用栈是非常深的**，只有最底层的调用返回了，整个渲染过程才会开始逐层返回。<u>这个漫长且不可打断的更新过程，将会带来用户体验层面的巨大风险：</u>**同步渲染一旦开始，便会牢牢抓住主线程不放，直到递归彻底完成。在这个过程中，浏览器没有办法处理任何渲染之外的事情，会进入一种无法处理用户交互**的状态。因此若渲染时间稍微长一点，页面就会面临卡顿甚至卡死的风险。

###### 异步渲染

而 ***React 16 引入的 Fiber 架构，恰好能够解决掉这个风险***：**Fiber 会将一个大的更新任务拆解为许多个小任务。每当执行完一个小任务时，渲染线程都会把主线程交回去，看看有没有优先级更高的工作要处理，确保不会出现其他任务被“饿死”的情况，进而避免同步渲染带来的卡顿。在这个过程中，渲染线程不再“一去不回头”，而是可以被打断的**，这就是 ***“异步渲染”***。它的执行过程如下图所示：

![图片5.png](/assets/images/react/Ciqc1F-Kl1CAA6pwAADpyi-xSnM494.png)

如果你初学 Fiber，对上面的两段描述感到陌生或者说“吃不透”，这都是正常的。在本课时，你大可不必如此苛求自己，**只需对“同步渲染”和“异步渲染”这两个概念有一个大致的印象，同时把握住 Fiber 架构下“任务拆解”和“可打断”这两个特性即可**。

接下来，看看“同步”变“异步”这个过程，是如何对生命周期构成影响的。

##### Fiber角度的生命周期工作流

**Fiber 架构的重要特征**就是*可以被打断的异步渲染模式*。但这个“打断”是有原则的，根据“**能否被打断**”这一标准，*React 16 的生命周期*被划分为了 **render 和 commit 两个阶段**，而 commit 阶段又被细分为了 pre-commit 和 commit。每个阶段所涵盖的生命周期如下图所示：

![Drawing 13.png](/assets/images/react/Ciqc1F-FVn6AEtlxAAIomWwVcQU485.png)

**三个阶段各自有哪些特征：**
- **render 阶段**：纯净且没有副作用，可能会被 React 暂停、终止或重新启动。
- **pre-commit 阶段**：可以读取 DOM。
- **commit 阶段**：可以使用 DOM，运行副作用，安排更新。

**总的来说，render 阶段在执行过程中允许被打断，而 commit 阶段则总是同步执行的。**
**为什么这样设计呢？** 简单来说，由于 render 阶段的操作对用户来说其实是“不可见”的，所以就算打断再重启，对用户来说也是零感知。而 commit 阶段的操作则涉及真实 DOM 的渲染，再狂的框架也不敢在用户眼皮子底下胡乱更改视图，所以这个过程必须用同步渲染来求稳。

##### 细说生命周期“废旧立新”背后的思考

**在 Fiber 机制下，render 阶段是允许暂停、终止和重启的**。当一个任务执行到一半被打断后，下一次渲染线程抢回主动权时，这个任务被**重启**的形式是<u>“重复执行一遍整个任务”而非“接着上次执行到的那行代码往下走”</u>。**这就导致 render 阶段的生命周期都是有可能被重复执行的**。

带着这个结论，我们再来看看 React 16 打算废弃的是哪些生命周期：
- componentWillMount；
- componentWillUpdate；
- componentWillReceiveProps。
这些生命周期的共性，**就是它们都处于 render 阶段，都可能重复被执行**，而且<u>由于这些 API 常年被滥用，它们在重复执行的过程中都存在着不可小觑的风险</u>。别的不说，说说我自己在团队 code review 中见过的“骚操作”吧。在“componentWill”开头的生命周期里，你习惯于做的事情可能包括但不限于:
- setState()；
- fetch 发起异步请求；
- 操作真实 DOM。

这些操作的问题（或不必要性）包括但不限于以下 3 点：

**（1）完全可以转移到其他生命周期（尤其是 componentDidxxx）里去做**。

比如在 componentWillMount 里发起异步请求。很多同学因为太年轻，<u>以为这样做就可以让异步请求回来得“早一点”，从而避免首次渲染白屏</u>。可惜你忘了，异步请求再怎么快也快不过（React 15 下）同步的生命周期。componentWillMount 结束后，render 会迅速地被触发，所以说首次渲染依然会在数据返回之前执行。**这样做不仅没有达到你预想的目的，还会导致服务端渲染场景下的冗余请求等额外问题，得不偿失**。

**（2）在 Fiber 带来的异步渲染机制下，可能会导致非常严重的 Bug**。

试想，<u>假如你在 componentWillxxx 里发起了一个付款请求</u>。由于 render 阶段里的生命周期都可以重复执行，在 componentWillxxx 被<u>打断 + 重启多次</u>后，<u>就会发出多个付款请求</u>。比如说，这件商品单价只要 10 块钱，用户也只点击了一次付款。但实际却可能因为 componentWillxxx 被**打断 + 重启多次**而多次调用付款接口，最终付了 50 块钱；又或者<u>你可能会习惯在 componentWillReceiveProps 里操作 DOM</u>（比如说删除符合某个特征的元素），那么 componentWillReceiveProps 若是执行了两次，你可能就会一口气删掉两个符合该特征的元素。

结合上面的分析，我们*再去思考 getDerivedStateFromProps 为何会在设计层面直接被约束为一个触碰不到 this 的静态方法*，其背后的原因也就更加充分了——<u>避免开发者触碰 this，就是在避免各种危险的骚操作</u>。

**（3）即使你没有开启异步，React 15 下也有不少人能把自己“玩死”。**

比如在 componentWillReceiveProps   和 componentWillUpdate 里<u>滥用 setState 导致重复渲染死循环的</u>，大家都懂哈（邪魅一笑）。

总的来说，**React 16 改造生命周期的主要动机是为了配合 Fiber 架构带来的异步渲染机制**。在这个改造的过程中，React 团队精益求精，<u>针对生命周期中长期被滥用的部分推行了具有强制性的最佳实践</u>。这一系列的工作做下来，首先是<u>确保了 Fiber 机制下数据和视图的安全性</u>，同时也<u>确保了生命周期方法的行为更加纯粹、可控、可预测</u>。

### 总结

通过 02 和 03 两个课时的学习，大家已经对 React 15、16 两个版本的生命周期有了深入的掌握，同时对 React 生命周期的一系列的变化以及其背后的原因都有了深刻而健全的理解。

生命周期看似简单，但要想真正吃透，竟然需要挑战这么长的一个知识链路，实在不简单！在使用 React 进行项目开发的 5 年里，我曾不止一次地为各路合作伙伴在生命周期里“为所欲为”而感到痛苦，也曾不止一次地为 React 基础知识结构摇摇欲坠的候选人感到可惜。若你能够耐下心来彻底消化掉这两个课时，相信这世上定能多出一个靠谱的前端！

话说回来，现有的生命周期，虽然已经对方法的最佳实践做了强约束，但是仍然无法覆盖<u>所有的“误操作”，其中最为典型的，就是对 getDerivedStateFromProps 的滥用</u>。关于这点，社区的讨论不是很多，但是 [React 团队给出的这篇文章](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)就帮助大家规避“误操作”来说是绰绰有余的。

经过了漫长的两个课时的学习，我们终于征服了生命周期这座小山包。<u>一个组件的一生如何度过，我们已经领教过了</u>。那么，多个组件之间如何“心意相通”呢？在下个课时，将围绕“数据在组件间的流动”展开讲解，探索“心意相通”的艺术。
