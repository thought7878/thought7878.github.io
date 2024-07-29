上节我们知道了什么是调试、调试的原理，这节我们开始学习调试工具的使用。

首先从网页的 JS 调试开始。

我们以 React 项目为例，用 create-react-app 创建一个 react 项目：

```
yarn create react-app test-react-debug
```

进入项目目录，执行 `npm run start`。

它会启动一个开发服务，然后浏览器访问 localhost:3000：

![[debug/前端调试通关秘籍/media/1e050b00da3a6e62ddee74d9624425bc_MD5.png]]

# Chrome DevTools

在 Sources 面板找到 src/index.js，打上个断点：

![[debug/前端调试通关秘籍/media/e021dacaffcb7e54e725ea720d9cd356_MD5.png]]

然后刷新就可以开始调试了：

![[debug/前端调试通关秘籍/media/85ad1075a92e741dba98dd4efd80227f_MD5.png]]

代码会在断点处断住，右边会显示当前 local 作用域的变量，global 作用域的变量，还有调用栈 call stack。


![[debug/前端调试通关秘籍/media/b9542810cb0979c89b66f0bcb702bc55_MD5.png]]

上面有几个控制执行的按钮，分别是：

![[debug/前端调试通关秘籍/media/176623375bd8f242b6c4ee18b378c62c_MD5.png]]恢复执行

![[debug/前端调试通关秘籍/media/b576e1ad27307a7e59f53562ce50edc7_MD5.png]]单步执行

![[debug/前端调试通关秘籍/media/942e6080da600dc4315d409af94b236f_MD5.png]]进入函数调用

![[debug/前端调试通关秘籍/media/ee1b3b84ab3d50fa23135cb4d879c562_MD5.png]]跳出函数调用


![[debug/前端调试通关秘籍/media/0031d5df15d05c70c081e6b89d6a9ab2_MD5.png]]让断点失效

![[debug/前端调试通关秘籍/media/d4bb3e5cac2fa8823e487acab559487a_MD5.png]]在异常处断住

**可以控制代码的执行，可以看到每一步的调用栈和作用域的变量，那理清代码的逻辑，或者排查代码中的问题不就很容易了么？**

# VSCode Debugger

其实调试网页的 JS，除了 Chrome DevTools 外，还有一种更好用的调试方式： VSCode Debugger。

用 VSCode 打开项目目录，创建 .vscode/launch.json 文件：

![[debug/前端调试通关秘籍/media/270999d269a88f2819e75833a3a50602_MD5.png]]

点击右下角的 Add Configuration... 按钮，选择 Chrome: Launch

![[debug/前端调试通关秘籍/media/2e65095bb4f550564bbc122654bbcdd4_MD5.png]]

把访问的 url 改为**开发服务器启动的地址**：

![[debug/前端调试通关秘籍/media/48150e7d2d52770499d8ef767f39da21_MD5.png]]

然后进入 Debug 窗口，点击启动：

![[debug/前端调试通关秘籍/media/1a99ae4e7e58a562b248a48c2fd85665_MD5.png]]

你会发现它启动了浏览器，并打开了这个 url：

![[debug/前端调试通关秘籍/media/9b45d697ce3f9a2908d1883d7c3508b2_MD5.png]]

VSCode 里还会有一排控制执行的按钮：

![[debug/前端调试通关秘籍/media/86d8b409d6f27217dac5efdddab7d485_MD5.png]]

在代码打个断点，然后点击 ![[debug/前端调试通关秘籍/media/ab45088fb3459c63d1a3fe859b3064f3_MD5.png]]
 刷新：
 
![[debug/前端调试通关秘籍/media/de2f412ae92b294686d541384230ee43_MD5.png]]

代码会执行到断点处断住，本地和全局作用域的变量，调用栈等都会展示在左边：


![[debug/前端调试通关秘籍/media/d14bbd5ef93362daf55a1a88b57fcc0e_MD5.gif]]

![[debug/前端调试通关秘籍/media/3a5a41abe91fab0f07edefa5ac49be32_MD5.png]]

上面的控制按钮分别对应**恢复执行、单步执行、进入函数调用、跳出函数调用**，这个和 Chrome DevTools 一样，还多了**刷新和停止**的按钮。

![[debug/前端调试通关秘籍/media/bb2bb1a0e14348effbe000dafb4313a9_MD5.png]]



那**异常断点**的按钮呢？被移到了这里。可以在被 catch 的异常处断住，也可以在没有被 catch 的异常处断住。

![[debug/前端调试通关秘籍/media/b256b93d7c9030afa59fbe1920aa81f3_MD5.png]]


## VSCode Debugger的好处

看起来和 Chrome DevTools 里调试差不多呀，在 VSCode Debugger 里调试有啥好处么？
**好处**是不用切换工具呀，之前是调试在 Chrome DevTools，写代码在 VSCode，而现在写代码和调试都可以在 VSCode 里，可以边调试边写代码。
比如我想访问 this 的某个属性，可以在 Debug Console 里输入 this 看下它的值，然后再来写代码：

![[debug/前端调试通关秘籍/media/8ed73b2ab69b30a0f5261f5c5bc43455_MD5.gif]]

如果你用了 TypeScript 可能会有属性名的提示、属性值类型的提示，但并不知道属性的值是啥。而边调试边写代码，能直接知道属性值是什么，有哪些函数可以调用。**边调试边写代码是我推荐的写代码方式。**

知道了怎么用，我们再来思考下：为什么 Chrome DevTools 和 VSCode Debugger 都可以调试网页呢？这是因为调试协议是一样的，都是 CDP。Chrome DevTools 可以对接 CDP 来调试网页，VSCode Debugger 也可以。只不过 VSCode Debugger 会多一层 Debug Adapter Protocol 的转换。

![[debug/前端调试通关秘籍/media/afefdcfa308550b5f72c3b41c29e7293_MD5.png]]

这也是为什么两个调试工具的功能大同小异。

# 总结

Chrome DevTools 和 VSCode Debugger 都能调试网页的 JS，可以打断点，单步执行，可以看到本地和全局作用域的变量，还有函数调用栈。

但我更推荐使用 VSCode Debugger 来调试，这样写代码和调试都用同一个工具，不用切换，而且还可以边调试边写代码。

这俩原理都是对接了 Chrome DevTools Protocol，用自己的 UI 来做展示和交互。

如果你还没用过 VSCode Debugger，不妨就从今天开始用起来吧。
