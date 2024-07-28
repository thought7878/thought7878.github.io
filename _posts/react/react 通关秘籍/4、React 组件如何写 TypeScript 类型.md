现在写 React 组件都是基于 TypeScript，所以如何给组件写类型也是很重要的。

这节我们就来学下 React 组件如何写 TypeScript 类型。

用 cra 创建个项目：

```
npx create-react-app --template typescript react-ts
```

![[react/react 通关秘籍/media/495122fea13e07147c7161929dcefdf8_MD5.png]]

我们平时用的类型在 `@types/react` 这个包里，cra 已经帮我们引入了。

![[react/react 通关秘籍/media/f662d31d35ec3c7aa7cddb494c58e85f_MD5.png]]

## JSX 的类型

在 App.tsx 里开始练习 TypeScript 类型：

```javascript
interface AaaProps {
  name: string;
}

function Aaa(props: AaaProps) {
  return <div>aaa, {props.name}</div>
}

function App() {
  return <div>
    <Aaa name="guang"></Aaa>
  </div>
}

export default App;
```

其实组件我们一般不写返回值类型，就*用默认推导出来的*。

![[react/react 通关秘籍/media/f2debff3047d853f6fd7bf69de3ac8b5_MD5.png]]

**React 函数组件默认返回值就是 `JSX.Element`。**

我们看下 `JSX.Element` 的类型定义：

```javascript
const content: JSX.Element = <div>aaa</div>
```
![[react/react 通关秘籍/media/4e047a80aed01d892f9c9ee2a93b2154_MD5.png]]

可以看到它就是 `React.ReactElement`。也就是说，如果你想描述一个 jsx 类型，就用 React.ReactElement 就好了。比如 Aaa 组件有一个 content 的 props，类型为 ReactElement：

![[react/react 通关秘籍/media/c20e6e902d09c00c1b9779fa8d964537_MD5.png]]

这样就只能传入 JSX。

跑一下：
```
npm run start
```

![[react/react 通关秘籍/media/3be9c88ac6a92ffb69695aa7cbf5c66f_MD5.png]]

ReactEl*ement 就是 jsx 类型，但如果你传入 null、number 等就报错了*：

![[react/react 通关秘籍/media/0e388de6c7c4081acaa4c4e6707c3c76_MD5.png]]

![[react/react 通关秘籍/media/b7bc058e63729fa2e53fde72473f5e53_MD5.png]]

**如果有的时候就是 number、null 呢？换成 ReactNode 就好了**：

![[react/react 通关秘籍/media/2bb7d954a903d046f0ae6b1c319db0f7_MD5.png]]

看下它的类型定义：

![[react/react 通关秘籍/media/02d1c13b4f3c9fcc4b95987bfca4bf19_MD5.png]]

*ReactNode 包含 ReactElement、或者 number、string、null、boolean 等可以写在 JSX 里的类型。* **这三个类型的关系 ReactNode > ReactElement >= JSX.Element。** 所以，一般情况下，如果你想描述一个参数接收 JSX 类型，就用 ReactNode 就行。

## 函数组件的类型

前面的函数组件，我们都没明确定义类型：

![[react/react 通关秘籍/media/5a0d1f1fc457ddd1f4b6b2d1f8e91088_MD5.png]]

**其实它的类型是 FunctionComponent：**

![[react/react 通关秘籍/media/913982682713b880b68b56b05f12f8df_MD5.png]]

```javascript
const Aaa: React.FunctionComponent<AaaProps> = (props) => {
  return <div>aaa, {props.name}{props.content}</div>
}
```

看下它的类型定义：

![[react/react 通关秘籍/media/f956982c523bac21979def818fab0942_MD5.png]]

可以看到，*FC 和 FunctionComponent 等价，参数是 Props，返回值是 ReactNode*。
而且函数组件还可以写几个可选属性，这些用到了再说。

## hook 的类型

接下来过一下 hook 的类型：

### useState

先从 useState 开始：

一般用推导出的类型就行：

![[react/react 通关秘籍/media/6fa2e7d62ed304dd645cce097e357a2d_MD5.png]]

也可以手动声明类型：

![[react/react 通关秘籍/media/719484428f298c15aa62f5fcbb13cb33_MD5.png]]

useEffect 和 useLayoutEffect 这种没有类型参数的就不说了。

### useRef

useRef 我们知道，可以保存 dom 引用或者其他内容。

所以它的类型也有两种。

保存 dom 引用的时候，参数需要传个 null：

![[react/react 通关秘籍/media/ac65d378b44762a4d81a6d453f1705b7_MD5.png]]

不然会报错：

![[react/react 通关秘籍/media/7f469db30e1c8bb226035de6ef18c969_MD5.png]]

而保存别的内容的时候，不能传 null，不然也会报错，说是 current 只读：

![[react/react 通关秘籍/media/2d2e9a6d9d77451a1728d11f57278978_MD5.png]]

![[react/react 通关秘籍/media/f559cfdd0d6cd5b1226093e1e2eb50de_MD5.png]]

为什么呢？

看下类型就知道了：

当你传入 null 的时候，返回的是  RefObject，它的 current 是只读的：

![[react/react 通关秘籍/media/046f1a614f05ef9c60b486c86a9d1020_MD5.png]]

![[react/react 通关秘籍/media/09654bcb6f3f00cf3090c8db109b09d1_MD5.png]]

这很合理，因为保存的 dom 引用肯定不能改呀。

而不传 null 的时候，返回的 MutableRefObject，它的 current 就可以改了：

![[react/react 通关秘籍/media/fa8850c336a32aaf98b3b68f1f948f6b_MD5.png]]

![[react/react 通关秘籍/media/35fcf952b7476eb5518e3b819165f98e_MD5.png]]

因为 ref 既可以保存 dom 引用，又可以保存其他数据，而保存 dom 引用又要加上 readonly，所以才用 null 做了个区分。

传 null 就是 dom 引用，返回 RefObject，不传就是其他数据，返回 MutableRefObject。

所以，这就是一种约定，知道传 null 和不传 null 的区别就行了。

### useImperativeHandle

我们前面写过 forwardRef + useImperativeHandle 的例子，是这样的：
```javascript
import { useRef } from 'react';
import { useEffect } from 'react';
import React from 'react';
import { useImperativeHandle } from 'react';

interface GuangProps {
  name: string;
}

interface GuangRef {
  aaa: () => void;
}

const Guang: React.ForwardRefRenderFunction<GuangRef, GuangProps> = (props, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => {
    return {
      aaa() {
        inputRef.current?.focus();
      }
    }
  }, [inputRef]);

  return <div>
    <input ref={inputRef}></input>
    <div>{props.name}</div>
  </div>
}

const WrapedGuang = React.forwardRef(Guang);

function App() {
  const ref = useRef<GuangRef>(null);
 
  useEffect(()=> {
    console.log('ref', ref.current)
    ref.current?.aaa();
  }, []);

  return (
    <div className="App">
      <WrapedGuang name="guang" ref={ref}/>
    </div>
  );
}

export default App;
```

forwardRef 包裹的组件会额外传入 ref 参数，所以它不是 FunctionComponent 类型，而是专门的 ForwardRefRenderFunction 类型。

它有两个类型参数，第一个是 ref 内容的类型，第二个是 props 的类型：

![[react/react 通关秘籍/media/ce89830149e4cc1b354b4da5f0b6fc09_MD5.png]]

其实 forwardRef 也是这两个类型参数，所以写在 forwardRef 上也行：

![[react/react 通关秘籍/media/fa000c57872b2e12f1e8c2e7b9080ca1_MD5.png]]

```javascript
import { useRef } from 'react';
import { useEffect } from 'react';
import React from 'react';
import { useImperativeHandle } from 'react';

interface GuangProps {
  name: string;
}

interface GuangRef {
  aaa: () => void;
}

const WrapedGuang = React.forwardRef<GuangRef, GuangProps>((props, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => {
    return {
      aaa() {
        inputRef.current?.focus();
      }
    }
  }, [inputRef]);

  return <div>
    <input ref={inputRef}></input>
    <div>{props.name}</div>
  </div>
});

```

useImperativeHanlde 可以有两个类型参数，一个是 ref 内容的类型，一个是 ref 内容扩展后的类型。

![[react/react 通关秘籍/media/ce6427be1b6c45907d20435f57f15442_MD5.png]]

useImperativeHanlde 传入的函数的返回值就要求满足第二个类型参数的类型

不过一般没必要写，因为传进来的 ref 就已经是有类型的了，直接用默认推导的就行。

![[react/react 通关秘籍/media/20bedc2e5394e0fb731f2da3e946de02_MD5.png]]

### useReducer

useReducer 可以传一个类型参数也可以传两个：

![[react/react 通关秘籍/media/58e1752140528accf8582dc345c00c2d_MD5.png]]

![[react/react 通关秘籍/media/9ada50d616d39415eb6b8d9a2fadff36_MD5.png]]

当传一个的时候，是 Reducer<xx,yy> 类型，xx 是 state 的类型，yy 是 action 的类型。

![[react/react 通关秘籍/media/3286b7acd3ec74b25a9970c4a71e6287_MD5.png]]

当传了第二个的时候，就是传入的初始化函数参数的类型。

![[react/react 通关秘籍/media/a1e7bb74e9b6cb8aaf2f54eff4d75dea_MD5.png]]

### 其余 hook

剩下的 hook 的类型比较简单，我们快速过一遍：

useCallback 的类型参数是传入的函数的类型：

![[react/react 通关秘籍/media/d7a75b4df1c773121d8235aaa5331b3a_MD5.png]]

useMemo 的类型参数是传入的函数的返回值类型：

![[react/react 通关秘籍/media/d7aeebf36f29fda82e32b9d80b4f1444_MD5.png]]

useContext 的类型参数是 Context 内容的类型：

![[react/react 通关秘籍/media/b6017ca44ec3b81504f3584f23863dbf_MD5.png]]

当然，这些都没必要手动声明，用默认推导的就行。

再就是 memo：

它可以直接用包裹的函数组件的参数类型：

![[react/react 通关秘籍/media/17981347861943c47376f89f20e06d66_MD5.png]]

也可以在类型参数里声明：

![[react/react 通关秘籍/media/1294e29291e9863a2d0d27ec40e0292e_MD5.png]]

## 参数类型

回过头来，我们再来看传入组件的 props 的类型。

### PropsWithChildren

前面讲过，jsx 类型用 ReactNode，比如这里的 content 参数：

![[react/react 通关秘籍/media/97e9ecf48f739d0a225eb8189e16de77_MD5.png]]


![[react/react 通关秘籍/media/998332b14cdf5114d4d51a09832ce5e0_MD5.png]]

如果你不想通过参数传入内容，可以在 children 里：

![[react/react 通关秘籍/media/80af23ea980cc5631e8c1d67a96f96f6_MD5.png]]

这时候就要声明 children 的类型为 ReactNode：

![[react/react 通关秘籍/media/fb8f4eeea0539ba8663bd8b07fbfe417_MD5.png]]

![[react/react 通关秘籍/media/e7b19d40a844d9f5b9fc917deb0102eb_MD5.png]]

```javascript
import React, { ReactNode } from "react";

interface CccProps {
  content: ReactNode,
  children: ReactNode
}

function Ccc(props: CccProps) {
  return <div>ccc,{props.content}{props.children}</div>
}

function App() {

  return <div>
    <Ccc content={<div>666</div>}>
      <button>7777</button>
    </Ccc>
  </div>
}

export default App;
```

![[react/react 通关秘籍/media/06abacccc05e5070db2c2ba6c59d8f81_MD5.png]]

但其实没有必要自己写，传 children 这种情况太常见了，React 提供了相关类型：

![[react/react 通关秘籍/media/f2a2d34e4c073e3cca839380a413e88d_MD5.png]]

```javascript
type CccProps = PropsWithChildren<{
  content: ReactNode,
}>
```
看下它的类型定义：

![[react/react 通关秘籍/media/4f03ae63dbf82305f1ad230932aacda6_MD5.png]]

就是给 Props 加了一个 children 属性。

### CSSProperties

有时候组件可以通过 props 传入一些 css 的值，这时候怎么写类型呢？

用 CSSProperties。

比如加一个 color 参数：

![[react/react 通关秘籍/media/efc965aea2c037f16fd8d10cf0c6d418_MD5.png]]

或者加一个 styles 参数：

![[react/react 通关秘籍/media/43ea0910897fd52e2fdd871f7ae8ee91_MD5.png]]

可以看到，提示出了 css 的样式名，以及可用的值：

![[react/react 通关秘籍/media/f43a7871c5f7dc3ea608decb8aff7f5e_MD5.png]]

```javascript
import React, { CSSProperties, PropsWithChildren, ReactNode } from "react";

type CccProps = PropsWithChildren<{
  content: ReactNode,
  color: CSSProperties['color'],
  styles: CSSProperties
}>


function Ccc(props: CccProps) {
  return <div>ccc,{props.content}{props.children}</div>
}

function App() {

  return <div>
    <Ccc content={<div>666</div>} color="yellow" styles={{
      backgroundColor: 'blue'
    }}>
      <button>7777</button>
    </Ccc>
  </div>
}

export default App;
```

## HTMLAttributes

如果你写的组件希望可以当成普通 html 标签一样用，也就是可以传很多 html 的属性作为参数呢？

那可以继承 HTMLAttributes：

![[react/react 通关秘籍/media/9f9cf47daca587d599f1d595a3ea3615_MD5.png]]

上图中可以看到，提示了很多 html 的属性。

```javascript
import React, { HTMLAttributes } from "react";

interface CccProps extends HTMLAttributes<HTMLDivElement>{

    } 

function Ccc(props: CccProps) {
  return <div>ccc</div>
}

function App() {

  return <div>
    <Ccc p>
      <button>7777</button>
    </Ccc>
  </div>
}

export default App;
```

那 HTMLAttributes 的类型参数是干嘛的呢？

是其中一些 onClick、onMouseMove 等事件处理函数的类型参数：

![[react/react 通关秘籍/media/1fe59733e3dff122aac0ba16bdee2c67_MD5.png]]

当然，继承 HTMLAttributes 只有 html 通用属性，有些属性是某个标签特有的，这时候可以指定 FormHTMLAttributes、AnchorHTMLAttributes 等：

![[react/react 通关秘籍/media/04951366f58f05d6fa6849350bdcc916_MD5.png]]

比如 a 标签的属性，会有 href：

![[react/react 通关秘籍/media/63aca190e7b10dcf8865829cd699a456_MD5.png]]

### ComponentProps

继承 html 标签的属性，前面用的是 HTMLAttributes：

![[react/react 通关秘籍/media/625bb380ece7899acfa066ad14433c92_MD5.png]]

其实也可以用 ComponentProps：

![[react/react 通关秘籍/media/cc4ecb7e740e27a3a98289f90f5e2e16_MD5.png]]

![[react/react 通关秘籍/media/1031e20c46bb7fdb779cf1286b088e4b_MD5.png]]

效果一样。

ComponentProps 的类型参数是标签名，比如 a、div、form 这些。

## EventHandler

很多时候，组件需要传入一些事件处理函数，比如 clickHandler：

![[react/react 通关秘籍/media/5fca117256c7f0b976ef95d1ac6b5753_MD5.png]]

```javascript
import React, { HTMLAttributes, MouseEventHandler } from "react";

interface CccProps {
  clickHandler: MouseEventHandler
} 

function Ccc(props: CccProps) {
  return <div onClick={props.clickHandler}>ccc</div>
}

function App() {

  return <div>
    <Ccc clickHandler={(e) => {
      console.log(e);
    }}></Ccc>
  </div>
}

export default App;
```
这种参数就要用 xxxEventHandler 的类型，比如 MouseEventHandler、ChangeEventHandler 等，它的类型参数是元素的类型。

或者不用 XxxEventHandler，自己声明一个函数类型也可以：

![[react/react 通关秘籍/media/9d1d110400461adeb33f5c28122a781e_MD5.png]]

```javascript
interface CccProps {
  clickHandler: (e: MouseEvent<HTMLDivElement>) => void
} 
```
案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/react-ts)。

## 总结

我们过了一遍写 React 组件会用到的类型：

- **ReactNode**：JSX 的类型，一般用 ReactNode，但要知道 ReactNode、ReactElement、JSX.Element 的关系

- **FunctionComonent**：也可以写 FC，第一个类型参数是 props 的类型

- **useRef 的类型**：传入 null 的时候返回的是 RefObj，current 属性只读，用来存 html 元素；不传 null 返回的是 MutableRefObj，current 属性可以修改，用来存普通对象。

- **ForwardRefRenderFunction**：第一个类型参数是 ref 的类型，第二个类型参数是 props 的类型。forwardRef 和它类型参数一样，也可以写在 forwardRef 上

- **useReducer**：第一个类型参数是 Reducer<data 类型, action 类型>，第二个类型参数是初始化函数的参数类型。

- **PropsWithChildren**：可以用来写有 children 的 props

- **CSSProperties**： css 样式对象的类型

- **HTMLAttributes**：组件可以传入 html 标签的属性，也可以指定具体的 ButtonHTMLAttributes、AnchorHTMLAttributes。

- **ComponentProps**：类型参数传入标签名，效果和 HTMLAttributes 一样

- **EventHanlder**：事件处理器的类型

后面写 React 组件的时候，会大量用到这些 typescript 的类型。