前面我们学了用 react-spring 做属性变化的动画，以及和 use-gesture 手势库结合做一些交互触发的动画。

其实还有一种动画类型没有涉及，就是过渡动画。

过渡动画是当元素进入、离开的时候（也就是添加到 dom 和从 dom 移除的时候）触发的动画。

比如这个：

![[react/react 通关秘籍/media/8f97497a4b0aa151fe7a63003b487ee8_MD5.gif]]

有同学说，上节不是做过这个么？

不一样，上节我们是一个列表里多个元素根据 index 计算 x，多个元素同时存在。

而这个是只存在一个，切换的时候其余元素会添加到 dom 和从 dom 中移除，从而触发进入、离开的过渡动画。

我们来写一下就知道了。

创建个项目：

![[react/react 通关秘籍/media/01a7a516699071dac4a1b7fa6f2a30e2_MD5.png]]

安装 react-spring 的包：

```shell
npm install --save @react-spring/web
```
改下 App.tsx：

```javascript
import React, { useState, CSSProperties } from 'react'
import { useTransition, animated, AnimatedProps } from '@react-spring/web'

import './App.css';

interface PageItem {
  (props: AnimatedProps<{ style: CSSProperties }>): React.ReactElement
}

const pages: Array<PageItem> = [
  ({ style }) => <animated.div style={{ ...style, background: 'lightpink' }}>A</animated.div>,
  ({ style }) => <animated.div style={{ ...style, background: 'lightblue' }}>B</animated.div>,
  ({ style }) => <animated.div style={{ ...style, background: 'lightgreen' }}>C</animated.div>,
]

export default function App() {
  const [index, set] = useState(0);

  const onClick = () => set(state => (state + 1) % 3);

  const transitions = useTransition(index, {
    from: { transform: 'translate3d(100%,0,0)' },
    enter: { transform: 'translate3d(0%,0,0)' },
    leave: { transform: 'translate3d(-100%,0,0)' },
  })


  return (
    <div className='container' onClick={onClick}>
      {transitions((style, i) => {
        const Page = pages[i]
        return <Page style={style} />
      })}
    </div>
  )
}
```
从上往下来看。

pages 是一个数组，接收 style 作为参数，返回 ReactElement。

这个 style 是被 react-spring 处理过后的 style，所以是 AnimatedProps 类型：

![[react/react 通关秘籍/media/9c9f3430fb63952eaf003435610b8514_MD5.png]]

不是都用 ReactNode 来表示 JSX 么，为什么这里用 ReactElement？

讲 react 的 ts 类型的时候，讲过 ReactNode 包括 ReactElement 类型还包括 string、number 等类型：

![[react/react 通关秘籍/media/9b3ec837e60f88b3fe4849730dab0118_MD5.png]]

但是这里要把 pages 的元素作为 ReactElement 来用，而 string、number 等都是不可以的，所以不能写 ReactNode：

![[react/react 通关秘籍/media/04742dcf8feb1e3947712f3f2b2a71ea_MD5.png]]

然后我们声明了一个 index 的 state，在点击的时候修改了它：

![[react/react 通关秘籍/media/f18fc6cc5ef514bf8aec5133f6df1af7_MD5.png]]

而渲染的时候只渲染这一个，之前渲染的组件就会被销毁，从而触发过渡动画：

![[react/react 通关秘籍/media/c83ac44ee48318cfac59a1eb4e7f7ccb_MD5.png]]

这是和上节那个类似效果的区别，上节那个并不是同时只保留一个，所以不是过渡动画。

然后这里用到了 useTransition，它的第一个参数就是会变化的状态，当状态变化的时候就会触发进入、离开动画：

![[react/react 通关秘籍/media/ae9d04003fef9e13e429dba3b8f1226d_MD5.png]]

分别指定了初始状态（from），进入的时候（enter），离开的时候（leave）会变化的 style。

这样，当 index 变化的时候，这些 style 就会变，从而触发动画：

![[react/react 通关秘籍/media/1c15a2fa94a6a4352d5532454a41bf78_MD5.png]]

在 App.css 写下样式：

```javascript
.container > div {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  font-weight: 800;
  font-size: 300px;
}

```
跑一下：

```
npm run start
```

![[react/react 通关秘籍/media/8f97497a4b0aa151fe7a63003b487ee8_MD5.gif]]

没啥问题。

但这只是一个元素的过渡动画，如果多个元素呢？

新建 App2.tsx

```javascript
import React, { useState } from "react";
import "./App2.css";
import { useTransition, animated } from '@react-spring/web'

export default function App() {
  const [items, setItems] = useState([
    { id: 1, text: "guang" },
    { id: 2, text: "guang" },
  ]);

  const transitions = useTransition(items, {
    from: { transform: 'translate3d(100%,0,0)', opacity: 0 },
    enter: { transform: 'translate3d(0%,0,0)', opacity: 1 },
    leave: { transform: 'translate3d(-100%,0,0)', opacity: 0 },
  });

  return (
    <div>
      <div className="item-box">
        {transitions((style, i) => {
          return <animated.div className="item" style={style}>
            <span
              className="del-btn"
              onClick={() => {
                setItems(items.filter((item) => item.id !== i.id));
              }}
            >
              x
            </span>
            {i.text}
          </animated.div>
        })}
      </div>

      <div
        className="btn"
        onClick={() => {
          setItems([...items, { id: Date.now(), text:  'guang' }]);
        }}
      >
        Add
      </div>
    </div>
  );
}
```
还有 App2.css

```css
.item-box {
    width: 300px;
    margin: 20px auto;
}

.item {
    margin: 4px 0;
    padding: 10px 0;
    border-radius: 4px;
    background: lightblue;
}

.del-btn {
    padding: 0 10px;
    cursor: pointer;
    user-select: none;
}

.btn {
    color: #fff;
    background-color: #0069d9;
    border-color: #0062cc;
    padding: 10px 20px;
    border-radius: 4px;
    width: fit-content;
    cursor: pointer;
    margin: 20px auto;
}
```
引入这个组件跑一下：

![[react/react 通关秘籍/media/2b81bebf233027f6b9b53a1b889961fe_MD5.png]]

![[react/react 通关秘籍/media/65cd992995e9d2fc992f6702d04362e9_MD5.gif]]
可以看到，每个元素都加上了过渡动画。

回过头来看下代码：

![[react/react 通关秘籍/media/e664b7e0bdac31f4cfe88a130e75d685_MD5.png]]

![[react/react 通关秘籍/media/c450480fc577f7570a17982091ff7f02_MD5.png]]

![[react/react 通关秘籍/media/519a812e78035535f47ab21891e838c4_MD5.png]]

列表项的增删就不详述了。

useTransition 多个元素的时候，你会发现和之前一模一样：

![[react/react 通关秘籍/media/e2f45d85858dd23ff3142bce68e8302e_MD5.png]]

![[react/react 通关秘籍/media/68f6da7781aa68ac188d278f10f39e2b_MD5.png]]

useTransition 传单个数据就是单个元素的过渡动画、传数组就是多个元素的过渡动画，写法一样。

此外，现在是刚开始所有元素会做一次动画：

![[react/react 通关秘籍/media/deb307178e406fdd3310f7e3db191e9f_MD5.gif]]

如果我只是想在增删元素的时候才有动画呢？

设置下 initial 时的样式就可以了：

![[react/react 通关秘籍/media/41179e4acf1bce4010360871b6686f8f_MD5.png]]

```javascript
const transitions = useTransition(items, {
    initial: { transform: 'translate3d(0%,0,0)', opacity: 1 },
    from: { transform: 'translate3d(100%,0,0)', opacity: 0 },
    enter: { transform: 'translate3d(0%,0,0)', opacity: 1 },
    leave: { transform: 'translate3d(-100%,0,0)', opacity: 0 },
});
```
这样最开始就不会做一次动画，只有在增删元素的时候会触发过渡动画：

![[react/react 通关秘籍/media/88c61b8aa412fc57bf3fd7906af28ee0_MD5.gif]]

这就是用 react-spring 的 useTransition 做过渡动画的方式。

其实提到过渡动画，还有一个老牌的库 react-transition-group。

这个库是 react 官方出的，而且用的也很多。

看 npm 包的周下载量：

[react-transition-group](https://www.npmjs.com/package/react-transition-group) 的周下载量：

![[react/react 通关秘籍/media/6d9707c3f762127a216958a049ffe5c5_MD5.png]]

[@react-spring/web](https://www.npmjs.com/package/@react-spring/web) 的周下载量：

![[react/react 通关秘籍/media/5eeb950fd307ed88a728aa9f2a7c1f72_MD5.png]]

好家伙，10 倍的差距。

这并不是说 react-transition-group 做过渡动画更优秀，只是它出现的更早更加流行。

我们来试一下：

安装 react-transition-group：

```shell
npm install --save react-transition-group

npm install --save-dev @types/react-transition-group
```

不同于 react-spring 是在组件 enter、leave 的时候直接修改 style。

react-transition-group 是通过改变 className 来给组件加上的过渡效果的。

创建 App3.tsx：

```javascript
import { useEffect, useState } from 'react';
import { CSSTransition } from 'react-transition-group';
import './App3.css';
 
function App() {
  const [flag, setFlag] = useState(false);

  useEffect(() => {
    setTimeout(() => {
      setFlag(true);
    }, 3000);
  }, []);

  return <CSSTransition
    in={flag}
    timeout={1000}
  >
    <div id="box"></div>
  </CSSTransition>
}

export default App;
```
App3.css

```css
#box {
    width: 300px;
    height: 50px;
    background: lightblue; 
    margin: 200px auto;
}

.enter {
    transform: translateX(-100%);
    opacity: 0;
}

.enter-active {
    transform: translateX(0);
    opacity: 1;

    transition: all 1s ease;
}

.enter-done {
    border: 5px solid #000;
}
```

参数 in 设置为 true 就是触发进入的动画，设置为 false 就是触发离开的动画。

跑一下：

![[react/react 通关秘籍/media/13df77252dd65268236a39c506cb5c82_MD5.png]]

![[react/react 通关秘籍/media/38f8f9923d5aeecfea4089d69be27535_MD5.gif]]

可以看到，当 in 变为 true 的时候，会触发进入的动画，依次给元素加上 .enter、.enter-active、.enter-done 的 className。

这样，我们就把过渡的样式加到这三个 className 里就可以了。

![[react/react 通关秘籍/media/1eae74ad37bdfb507587f98d76962f19_MD5.png]]

而且不同于 react-spring 会自己控制动画效果，react-transition-group 只是加上 className 不负责动画效果，需要添加 transition 来实现动画。

CSSTransition 组件会先设置 enter，再设置 enter-active，这样就触发动画了。

然后到了 timeout 参数的时间，就会设置 enter-done 的 className。

反之，如果 in 的参数改为 false，就会触发离开动画：

![[react/react 通关秘籍/media/8fd83917a71b4192f31ce580445b815c_MD5.gif]]

className 会先设置 exit，再设置 exit-active 来触发动画，到了 timeout 的时间会设置为 exit-done。

连起来，就可以实现 enter 和 exit 的动画：

```javascript
import { useEffect, useState } from 'react';
import { CSSTransition } from 'react-transition-group';
import './App3.css';
 
function App() {
  const [flag, setFlag] = useState(false);

  return <div>
    <CSSTransition
      in={flag}
      timeout={1000}
    >
      <div id="box"></div>
    </CSSTransition>
    <button onClick={() => setFlag(!flag)}>{!flag ?  '进入' : '离开'}</button>
  </div>
}

export default App;
```
```css
#box {
  width: 300px;
  height: 50px;
  background: lightblue; 
  margin: 100px auto;
}

button {
  margin: 0 auto;
  display: block;
}

.enter {
  transform: translateX(-100%);
  opacity: 0;
}

.enter-active {
  transform: translateX(0);
  opacity: 1;

  transition: all 1s ease;
}

.enter-done {
  border: 5px solid #000;
}

.exit {
  transform: translateX(0%);
  opacity: 1;
}

.exit-active {
  transform: translateX(100%);
  opacity: 0;

  transition: all 1s ease;
}

.exit-done {
}
```

![[react/react 通关秘籍/media/c0feb08e62f0efa834739c5e520bd635_MD5.gif]]

可以看到，通过 className 从 enter 到 enter-active 到 enter-done 的变化，以及从 exit 到 exit-active 到 exit-done 的变化，就实现了进入和离开的动画。

不知道大家有没有发现，最开始出现的时候是没有动画的，之后后来切换 in 的 props 的时候，才有动画。

如果想最开始出现的时候就做一次动画呢？

这就需要设置 appear 的 props 了：

![[react/react 通关秘籍/media/19b2d26856746be603d142f55fa9bc64_MD5.png]]
```css
.appear {
  transform: scale(0);
}

.appear-active {
  transform: scale(1);
  transition: all 1s ease;
}

.appear-done {
  
}
```
![[react/react 通关秘籍/media/290d0174275b7e34f08d8db82380362a_MD5.gif]]

可以看到，最开始还有一个 appear、appear-active、appear-done 的 className 变化，并且还会添加 enter-done。

这个只会在刚出现的时候设置一次。

也就是一共可以有 appear、enter、exit 3 种过渡动画。

这个在 react-spring 里也是一样，它默认是有 appear 动画的：

![[react/react 通关秘籍/media/deb307178e406fdd3310f7e3db191e9f_MD5.gif]]

我们当时是设置了 initial 时的样式，然后就没有 apear 动画了：

![[react/react 通关秘籍/media/41179e4acf1bce4010360871b6686f8f_MD5.png]]

![[react/react 通关秘籍/media/88c61b8aa412fc57bf3fd7906af28ee0_MD5.gif]]

也就是说用 react-spring 和 react-transition-group 都能实现一样的过渡动画的功能。

而且 react-transition-group 只是添加 className 要自己用 transition 来做动画，而 react-spring 是自带动画效果。

继续看 react-transition-group，现在是我们自己设置 in 的 props 来触发进入和离开动画的，如果是列表的多个 child，都想加动画呢？

这时候就用 TransitionGrop 组件。

创建 App4.tsx

```javascript
import React, { useState } from "react";
import { CSSTransition, TransitionGroup } from "react-transition-group";
import "./App4.css";

export default function App() {
  const [items, setItems] = useState([
    { id: 1, text: "guang" },
    { id: 2, text: "guang" },
  ]);

  return (
    <div>
      <TransitionGroup className="item-box">
        {items.map(({ id, text }) => (
          <CSSTransition key={id} timeout={1000}>
            <div className="item">
              <span
                className="del-btn"
                onClick={() => {
                  setItems(items.filter((item) => item.id !== id));
                }}
              >
                x
              </span>
              {text}
            </div>
          </CSSTransition>
        ))}
      </TransitionGroup>

      <div
        className="btn"
        onClick={() => {
          setItems([...items, { id: Date.now(), text:  'guang' }]);
        }}
      >
        Add
      </div>
    </div>
  );
}
```
就是用 TransitionGroup 包裹下，
```css
.item-box {
  width: 300px;
  margin: 20px auto;
}

.item {
  margin: 4px 0;
  padding: 10px 0;
  border-radius: 4px;
  background: lightblue;
}

.del-btn {
  padding: 0 10px;
  cursor: pointer;
  user-select: none;
}

.enter {
  opacity: 0;
  transform: translateX(-100%);
  background: lightblue;
}
.enter-active {
  opacity: 1;
  transform: translateX(0%);
  background: lightblue;
  transition: all 1s ease;

}
.enter-done {
}
.exit {
  opacity: 1;
  transform: translateX(0%);
  background: red;
}
.exit-active {
  opacity: 0;
  transform: translateX(100%);
  background: red;
  transition: all 1s ease;
}

.btn {
  color: #fff;
  background-color: #0069d9;
  border-color: #0062cc;
  padding: 10px 20px;
  border-radius: 4px;
  width: fit-content;
  cursor: pointer;
  margin: 20px auto;
}
```
效果就是前面用 react-spring 实现过一遍的那个：

![[react/react 通关秘籍/media/950ce9df293bd6430c7b1f1209d63d42_MD5.gif]]

用 CSSTransition 的时候，我们需要自己设置 in 的 props 来触发进入和离开动画。

![[react/react 通关秘籍/media/748025142f7308e5b816d9908366d8ac_MD5.png]]

而现在只需要设置 key，TransitionGroup 会在 children 变化的时候对比新旧 item，来自动设置 in，触发动画。

![[react/react 通关秘籍/media/c68008c03d649688cc5055d8818aed5c_MD5.png]]

这就是 react-transition-group 的常用功能。

此外，它还有两个组件，Transition 和 SwitchTransition：

把 CSSTransition 换成 Transition，然后打印下 status：

![[react/react 通关秘籍/media/42c6a736f128a8f9d6417bd4ac5a7ee7_MD5.png]]

可以看到，status 最开始是从 entering 到 entered，从 exiting 到 exited 变化，但是不会设置 className：

![[react/react 通关秘籍/media/bc985b1d35d7e244862fbbd1c3780c3a_MD5.gif]]

我们可以根据 status 的变化自己设置 className。

其实，CSSTransition 就是基于 Transition 封装的。

一般我们用 CSSTransition 就好了。

再就是 SwithTransition，先看下效果：

![[react/react 通关秘籍/media/249a5f79c7800d883b0bbc48001a8883_MD5.png]]

包裹一层 SwitchTransition，然后设置下 key。

当 mode 为 in-out 时：

![[react/react 通关秘籍/media/24e16d3c29c46c4e1ca775510541b6ab_MD5.gif]]

当 mode 为 out-in 时：

![[react/react 通关秘籍/media/ecb45336cc2d98793d980e4ba24e89dd_MD5.gif]]

这个组件就是用来控制两个组件切换时的进入、离开动画的顺序的。

这样，react-transition-group 的 4 个组件： Transition、CSSTransition、TransitionGroup、SwitchTransition 我们就都过了一遍。

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/transition-test)

## 总结

当组件进入 dom 和从 dom 中移除的时候，发生的动画就叫做过渡动画。

react-spring 有 useTransition 这个 hook 来实现过渡动画，我们也可以用 react-trasition-group 这个包来实现。

这两个包能实现一样的功能，但是思路不同。

react-spring 有内置的动画效果，所以只要用 useTransition 设置 from、enter、leave 时的 style，它就会在数据变化的时候触发过渡动画。

而 react-transition-group 是通过 className 的修改来实现过渡动画，而且要自己用 transition 的 css 来控制动画效果：

- 进入的时候会触发 enter、enter-active、enter-done 的 className 切换

- 离开的时候是 exit、exit-active、exit-done 的切换

- 如果设置了 appear 参数，刚出现的时候，还会有 appear、appear-active、appear-done 的切换。

它有 Transition、CSSTransition、TransitionGroup、SwitchTransition 这 4 个组件。

常用的就是 CSSTransition 和 TransitionGroup，这俩是用来做单个元素的过渡动画和多个元素的过渡动画的。

而在 react-spring 里，单个元素的过渡动画和多个元素的过渡动画写法没区别。

具体用哪种方案来实现过渡动画都行，都是很流行的方案。
