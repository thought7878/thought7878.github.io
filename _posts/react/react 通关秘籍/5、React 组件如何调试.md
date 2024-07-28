## Why 断点调试

大家都是怎么调试 React 组件的呢？很多同学都会说，console.log 啊。这样可以，但效率太低了，因为你只能看到你打印的一些零散值，*看不到代码的具体执行路线，比如走了哪些分支、调用某个函数的时候参数是什么*。

而如果你使用 **debugger 断点调试**的方式，就可以看到代码执行的细节，可以在关心的地方断住，单步执行来观察是否符合预期，*可以帮你更好的理清代码逻辑*。所以说，学会断点调试 React 组件的代码，是*提升你写代码和排查错误的水平*的很重要的一步。

用 cra 创建个项目：

```
npx create-react-app --template typescript debug-test
```

![[react/react 通关秘籍/media/1444dce7789d299015ea5264f3dc20d8_MD5.png]]

改下 index.tsx

```javascript
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(<App />);
```

然后在 App.tsx 写个组件：

```javascript
import { useEffect, useLayoutEffect, useState } from "react";

async function queryData() {
  const data = await new Promise<number>((resolve) => {
    setTimeout(() => {
      resolve(666);
    }, 2000);
  })
  return data;
}

function App() {
  const [num, setNum] = useState(0);

  useLayoutEffect(() => {
    queryData().then(data => {
      setNum(data);
    })
  }, []);

  return (
    <div onClick={(e) => {
      setNum((prevNum) => prevNum + 1)
    }}>{num}</div>
  );
}

export default App;

```
把开发服务跑起来：

```
npm run start
```

![[react/react 通关秘籍/media/26d82d7e0aff8634417b974ae31f0025_MD5.gif]]

## How 断点调试

点击 debug 面板的 `create a launch.json file`

![[react/react 通关秘籍/media/1c00a171881394bd9527b894e11f49fc_MD5.png]]

选择 chrome 类型的调试配置：

![[react/react 通关秘籍/media/d931f57a05468357ffcc964345f7d2c7_MD5.png]]

它会创建 .vscode/launch.json 文件：

![[react/react 通关秘籍/media/383c2c9dac040f967a6918a6b083d2b1_MD5.png]]

把端口改为 3000

然后点击调试按钮，会跑一个浏览器：

![[react/react 通关秘籍/media/956336b01e6ecc474926e351ca97660f_MD5.gif]]

打几个断点，然后点击*刷新*：

![[react/react 通关秘籍/media/455b26b5ed6bfda7cdbfb8eb6a76b0c6_MD5.png]]

代码会在断点处断住，左边可以看到作用域、调用栈，鼠标 hover 到变量上可以看到变量值：

![[react/react 通关秘籍/media/e0918400cae180e6099afc1ebdb6a2ec_MD5.png]]

### 几个按钮

这几个按钮分别是*跳断点执行、单步执行、进入函数、跳出函数、刷新、停止*：

![[react/react 通关秘籍/media/fec4e6a36118d90d9330e29dba676d8a_MD5.png]]

可以点点试试看。

在页面点击 div，会触发 click 事件，在断点处断住，

![[react/react 通关秘籍/media/609d9513cb4aaa4ea6fa0e7aa648f75c_MD5.png]]

可以在下面的 `debug console` 输入变量或者表达式，会输出执行结果：

![[react/react 通关秘籍/media/1e359ad74ff3c48759d554b502335eb9_MD5.png]]

这样调试不比 console.log 方便多了？

### 3种断点类型
#### 条件断点

而且还有其他3种断点类型，右键可以选择添加一个条件断点:

![[react/react 通关秘籍/media/5c2f64cb84fb396f5e312e84aeba0f3b_MD5.png]]



**输入表达式，代码会在满足条件的时候断住：**

![[react/react 通关秘籍/media/188b3e9654c052b1d38bd10f1fc58f6d_MD5.png]]

![[react/react 通关秘籍/media/506b3eb78abcddac6ff73574a53fd859_MD5.png]]

也可以选择 hit count，*代码会在触发对应的次数的时候断住：*

![[react/react 通关秘籍/media/68156d151a4f4b8d550625b95a92c3c0_MD5.png]]

 #### Logpoint
 
 `logpoint`，*它不会断住，但会在代码执行到这里的时候打印表达式的值：*

![[react/react 通关秘籍/media/eedf4fe3e4dd040f947797fce0f34365_MD5.png]]

![[react/react 通关秘籍/media/e09612259cb9856142f954e76b0e138a_MD5.png]]

![[react/react 通关秘籍/media/9b32dfd6f6b5209d33d5e2123ab667cb_MD5.png]]

这些断点类型也都挺有用的。这样我们就可以在 VSCode 里边写 React 组件边断点调试了。

### userDataDir

### 为什么新启动的浏览器没有 React DevTools等插件

不过，**这个浏览器没有 React DevTools**。

![[react/react 通关秘籍/media/2b498d69b04f485f6cfe23d04b6c124c_MD5.png]]

确实，*因为这跑的是一个新的浏览器实例，没有之前的那些用户数据*。*用户数据是保存在 userDataDir 里的，一个 userDataDir 对应一个浏览器实例*。不信我们试试看：

![[react/react 通关秘籍/media/726de63e2dca4c6be25ad811730ab6bf_MD5.png]]

我指定一个 userDataDir，然后点击调试启动。

在启动的浏览器里把掘金收藏为书签：

![[react/react 通关秘籍/media/beb337176782fed9fd90a73907070851_MD5.png]]

然后进入刚才那个 userDataDir，进入 defaults 目录，看一下 Bookmarks 文件的内容： 

![[react/react 通关秘籍/media/a200e9cc7d7694ff2853765fe7fda9ce_MD5.png]]

就有刚才保存的书签了。

同理，各种 chrome 插件、浏览记录、cookies 等等，所有用户数据都是保存在 userDataDir 里。chrome 一个 userDataDir 只能跑一个实例。

**我们调试的时候，如果没有指定 userDataDir，默认是临时创建一个新的 userDataDir。** 所以这时候自然就没有 React DevTools 等你之前安装的插件了。

### 设置userDataDir，使用React DevTools等插件

**如果想调试的时候还用这些插件，把 userDataDir 设置为 false，就是用默认的 userDataDir** ：

![[react/react 通关秘籍/media/273badc0ef9c70c8b6ad2f46dae3ca59_MD5.png]]


*这时候需要把之前跑的 chrome 关掉才能跑，因为一个 userDataDir 只能跑一个实例。*

之后再点击调试，这次跑的浏览器就有你之前装的 React DevTools 了：

![[react/react 通关秘籍/media/84bc97c5de810b4fbd6d350360395378_MD5.png]]

这样，我们就可以在 VSCode 里断点调试，并且跑的调试浏览器还有 React DevTools 可用了。

## 总结

这节我们学了如何用 VSCode 调试 React 组件。

点击创建 launch.json，输入 chrome 类型的调试配置，点击调试，这时候代码就会在打的断点处断住。

断点类型有普通断点、条件断点、hit count、logpoint 等。

用 debugger 可以在想调试的代码处断住，单步调试，看一些变量的变化，看代码执行路线，这样高效很多。

此外，chrome 的各种用户数据是保存在 userDataDir 下，一个 userDataDir 只能跑一个实例。

默认跑的浏览器是会创建新的临时 userDataDir，所以没有之前的用户数据，也就没有之前安装的 React DevTools 等插件。

可以把它设置为 false，然后关掉别的浏览器再跑，这时候就是在默认 userDataDir 跑的，各种用户的数据都有。

会断点调试 React 组件，是提高开发和排查问题效率的很重要的技能。
