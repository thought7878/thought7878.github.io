写 react 项目的小伙伴应该都用过 antd 组件库，但绝大多数同学并没有看过它的源码。而想深入掌握 antd 组件库，只熟悉参数是不行的，必须要深入到源码层面。所以今天就来分享下如何调试 antd 的源码。而且我敢说这种调试源码的方式 90% 的前端都不会。为什么呢？看到后面你就知道了。

## 创建一个 react 项目

首先，我们用 create-react-app 创建一个 react 项目：

```
yarn create react-app antd-react-test
```
创建成功后，进入到项目里，把 dev server 跑起来。

![[debug/前端调试通关秘籍/media/9983d05d40f70f46e3baecb5778a1638_MD5.png]]

浏览器访问可以看到渲染出的页面：

![[debug/前端调试通关秘籍/media/d8cfa6d89b3aafa7545747ca414d8029_MD5.png]]

然后我们安装 antd，在入口组件里引入样式和 Button 组件：

![[debug/前端调试通关秘籍/media/b8e0141e011db7b6e5483f528e5ef5af_MD5.png]]

页面会显示这个 Button：

![[debug/前端调试通关秘籍/media/cceef9e97542a328e9ff16f303a25782_MD5.png]]

## 创建 VSCode 调试配置

那怎么调试这个 Button 组件的源码呢？首先，创建一个 VSCode 调试配置：

![[debug/前端调试通关秘籍/media/253266a02288c4546ee06e98b7a2dcc3_MD5.png]]

指定调试的 URL，然后启动调试。

在组件里打个断点，代码会在这里断住：

![[debug/前端调试通关秘籍/media/7aeed945a5007f5d491c6bacadf004b7_MD5.png]]

可以看到调用栈中上一帧是 renderWithHooks，这就是 react 源码里调用函数组件的地方。

点击那个调用栈，你就会看到：

![[debug/前端调试通关秘籍/media/917ded5010ad2fd7864c59c398dcba59_MD5.png]]

它调用了 App 的函数组件，传入了参数，拿到渲染后的 children 做后续处理。

所有函数组件都是在这里被调用的，而 antd 的组件也全部是函数组件，那么我们在这里加个断点，打名字为 Button 的函数组件被调用的时候断住不就行了？

这种在某种条件下才断住的情况可以用条件断点：

右键选择添加条件断点：

![[debug/前端调试通关秘籍/media/5b6d463297716298306be8142c7e45e9_MD5.png]]

输入断住的条件：

![[debug/前端调试通关秘籍/media/e24e3c37e64387fb5001e869b01cd34b_MD5.png]]

当组件名字包含 Button 的时候才断住。

然后刷新：

![[debug/前端调试通关秘籍/media/f7281bc139e0bf36e96a2b04024ee74d_MD5.gif]]

你会看到 App 组件明明也是函数组件，却没有在这里断住，而 InternalButton 在这里断住了。这就是条件断点的作用。这个 InternalButton 就是  antd 里的 Button 组件。

step into 进入函数内部：

![[debug/前端调试通关秘籍/media/6d3d94125b4c347aba1f4e17b0852654_MD5.gif]]

*你会发现这确实是 Button 组件的源码，但却是被编译后的，比如 jsx 都被编译成了 React.createElement：*

![[debug/前端调试通关秘籍/media/44dca3fcc2489d3b576e6b8112a8f02a_MD5.png]]

这样是可以调试 Button 组件源码的，但是比较别扭。

*那能不能直接调试 Button 组件对应的 tsx 源码呢？可以的，这就要用到 sourcemap 了。*

## 下载源码

我们得把 antd 的源码下载下来(我下载的时候是 4.23）：

```
git clone --depth=1 --single-branch git@github.com:ant-design/ant-design.git
```
下载的时候加个 `--single-branch` 是下载单个分支， `--depth=1` 是下载单个 commit， 这样速度会快几十倍，是个有用的加速小技巧。

## build

antd 下载下来，安装完依赖之后，我们开始 build。但你会发现 package.json 中有 build 命令，有 dist 命令，该执行哪个呢？这个就需要了解下 antd 的几种入口了。去 react 项目的 node_modules 下，找到 antd 的 package.json 看一下，你会发现它有三种入口：

![[debug/前端调试通关秘籍/media/c988eca36a5cc1693b35c3cdb429aa1c_MD5.png]]

- main 是 commonjs 的入口，也就是 require('antd') 的时候会走这个。

- module 是 esm 的入口，也就是 import xx from 'antd' 的时候会走这个。

- unpkg 是 UMD 的入口，也就是通过 script 标签引入的时候或者 commonjs 的方式等都可以用。

分别对应了 lib、es、dist 的目录。

所以 antd 项目里的 dist 命令就是单独生成 UMD 代码的，而 build 命令是生成这三种代码。

这三种形式的代码都是可用的，这里我们选择构建 UMD 形式的代码，因为它会用 webpack 打包，而另外两种是通过 gulp 构建的。我对 webpack 更熟悉一些。

执行 npm run dist，就会构建出 dist 目录，下面是 UMD 的代码：

![[debug/前端调试通关秘籍/media/9be0b2e7bafd06b4cabc9cc042fa1a7b_MD5.png]]

![[debug/前端调试通关秘籍/media/d08b82c10cd1f69625e97b6f45e744f1_MD5.png]]

你会发现默认的构建就是会生成 sourcemap 的，其实你去那个 react 测试项目里看下，从 npm 下载的 antd 包也带了 sourcemap：

![[debug/前端调试通关秘籍/media/df4d4f9263e7303074d8295b974366f8_MD5.png]]

那直接用 dist 入口的代码就能调试源码了么？

我们试一下：

![[debug/前端调试通关秘籍/media/5e9f3a1dd71742705b7774341ebd71e1_MD5.png]]

把引入组件的地方换成 dist 目录下，也就是用 UMD 形式的入口。

重新跑调试：

你会发现代码确实比之前更像源码了。

之前前面是这样的：

![[debug/前端调试通关秘籍/media/7d1ab46441cfdf9beadc62825449632a_MD5.png]]

现在是这样：

![[debug/前端调试通关秘籍/media/0723e792f586f7d56ce51aa725bbf1fa_MD5.png]]

也就是没了 babel runtime 的代码，这明显是源码了。

但是你往后看：

之前是这样的：

![[debug/前端调试通关秘籍/media/5d276a74aff555702ab784a1952de5dd_MD5.png]]

现在是这样：
![[debug/前端调试通关秘籍/media/9d3a11a70659dc26a4092b10c17e73e1_MD5.png]]

依然还是 React.createElement，而不是 jsx，也没有 ts 的代码。说明它还不是最初的源码。

为什么会出现这种既是源码又不是源码的情况呢？因为它的编译流程是这样的：

![[debug/前端调试通关秘籍/media/cc3dc8369710eb19d92b021fded3dc77_MD5.png]]

代码经过了 tsc 的编译，然后又经过了 babel 的编译，最后再通过 webpack 打包成 bundle.js。

tsc 和 babel 的编译都会生成 sourcemap，而 webpack 也会生成一个 sourcemap。webpack 的 sourcemap 默认只会根据最后一个 loader 的 sourcemap 来生成。*所以说上面我们用了 sourcemap 之后只能关联到 babel 处理之前的代码，像 ts 语法、jsx 代码这些都没有了。因为没有关联更上一级的 ts-loader 的 sourcemap，自然是没法直接映射回源码的。*

*所以想映射回最初的 tsx 源码，只要关联了每一级 loader 的 sourcemap 就可以了*。而这个是可以配置的，就是 devtool。devtool 可以设置 soruce-map，就是生成 sourcemap，*但是这个不会关联 loader 的 sourcemap*。还可以设置 cheap-module-source-map，这个 module 就是关联 loader 的 soruce-map 的意思。（那个 cheap 是只保留行的 sourcemap，生成速度会更快）

*思路理清楚了，我们去改下编译配置：* antd 的编译工具链在 @ant-design/tools 这个包里，从 antd/node_modules/@antd-design/tools/lib/getWebpackConfig.js 就可以找到 webpack 的配置

![[debug/前端调试通关秘籍/media/669a0316193ab9db8089ab6d8ea51d70_MD5.png]]

搜一下 ts-loader，你就会看到这段配置：

![[debug/前端调试通关秘籍/media/3233397404dd9f934e470414b249f93a_MD5.png]]

确实就像我们分析的，tsx 会经过 ts-loader 和 babel-loader 的处理。

搜一下 devtool，你会发现它的配置是 source-map：

![[debug/前端调试通关秘籍/media/685d0893f037c35ed36fb9e8400aaf57_MD5.png]]

*这就是 antd 虽然有 sourcemap，但是关联不到 tsx 源码的原因。*

那我们给它改一下：把 devtool 改为 cheap-module-source-map。并且改一下 babel 配置，设置 sourceMap 为 true，让它生成 sourcemap。 

![[debug/前端调试通关秘籍/media/41061c99476e4b8786bfdf517a8c0465_MD5.png]]

ts也同样要生成 sourcemap，不过那个是在根目录的 tsconfig.json里改：

![[debug/前端调试通关秘籍/media/564e8e5a317385995b20192861434cea_MD5.png]]

改完这三点之后，再重新跑 npm run dist。dist 目录下会生成新的 antd.js 和 antd.js.map。

![[debug/前端调试通关秘籍/media/4166d4d706e3c4fb50fe32c18613096d_MD5.png]]

*把它复制到 react 项目的 node_modules/antd/dist 下，覆盖之前的。*

清一下 babel-loader 的缓存，删除整个 .cache 目录：

![[debug/前端调试通关秘籍/media/f9bcc11a0e8b4eac66e21dfeaf599df3_MD5.png]]

重新跑 dev server。注意，这里要用 dist 下的代码：

![[debug/前端调试通关秘籍/media/295e852c21ce33c5ad230f02fc4b4cdb_MD5.png]]

然后再跑到断点的位置，进入组件源码，你会进入一个新世界：

![[debug/前端调试通关秘籍/media/d630b19575f7ad26d7fc1f87020d36af_MD5.gif]]

ts 类型、jsx 的语法，熟悉的感觉又回来了，这不就是 antd 组件的源码么！

![[debug/前端调试通关秘籍/media/3c24027e01416b5c4e8d8823ae3ed66f_MD5.gif]]

你可以断点调试 antd 的参数是怎么处理的，什么参数会走什么逻辑等。

*这个完全不影响正常开发，也就是把 antd 换成了从 antd/dist/antd 引入而已，开发完了换回去就行。*

*有的同学可能会担心 node_modules 下的改动保存不下来*。这个也不是问题，可以执行下 npx patch-package  antd，会生成这样一个 patch 文件。patch 文件里记录了你对 antd 包的改动，这个可以上传到 git 仓库，其他小伙伴拉下来再执行 npx patch-package 就会自动应用这些改动。

![[debug/前端调试通关秘籍/media/d07fcec06c8702788e7a688dc6c4a703_MD5.png]]



至此，我们成功的调试了 antd 组件的 tsx 源码。

为什么说 90% 的前端不会调试它的源码呢？主要是涉及的技术比较多：

- VSCode Chrome Debugger 调试网页，这个知道的人就不多
- react 源码里 renderWithHooks 是调用函数组件的地方
- 条件断点可以在满足条件的时候断住
- antd 的 esm、commonjs、UMD 三种入口
- sourcemap 是干啥的，虽然经常接触，但还是有很多前端没用过
- webpack 的 cheap-module-source-map 的含义，为什么需要关联 loader 的 sourcemap

而调试 antd 的组件源码需要综合运用这些技术，难度还是比较高的。

*但这些都是我们前面学过的东西，在这里串起来了，把学过的东西串起来的时候是不是还挺爽的～* 能综合运用所学技术来解决具体遇到的问题，这就是我们学习多种技术的目的。

## 总结

antd 是 react 主流组件库，我们经常使用它但可能并没有调试过它的源码。

我们可以在 renderWithHooks 里调用函数组件的地方打个条件断点，在调用想调试的组件时断住，这样我们就可以 step into 到该组件定义的地方。

但是这样调试的并不是最初的源码，没有 jsx 和 ts 语法。

想调试最初的 tsx 源码需要用 sourcemap。

antd 有三种入口：es 目录对应 esm 入口，lib 目录对应 commonjs 入口，dist 目录对应 UMD 入口。

把 antd 代码下载下来，执行 npm run dist 就可以生成 UMD 形式的代码。

想要 sourcemap 映射到 tsx 源码，需要把 devtool 设置成 cheap-module-source-map，然后开启 babel-loader 和 ts-loader 的 sourcemap。

把产物覆盖 antd 的 dist 下的产物，再调试就可以直接调试 antd 组件的 tsx 源码了。

用 antd 组件写业务逻辑之余，对什么组件感兴趣，可以顺便去看看它的源码，它不香么？

（这节我们用到了条件断点，上节学的各种断点在特定场景下都很有用，大家要能灵活运用）
