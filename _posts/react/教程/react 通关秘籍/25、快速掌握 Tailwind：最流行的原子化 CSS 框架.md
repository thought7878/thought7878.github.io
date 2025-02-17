Tailwind 是流行的原子化 css 框架。

有多流行呢？

![[b65fb0930bf6b1c03bd10ea20994d074_MD5.png]]

它现在有 76k star 了，npm 包的周下载量也很高：

![[1d6f59dcf90b0877e2139a1687b014b4_MD5.png]]

那什么是原子化 css？

我们平时写 css 是这样的：

```html
<div class="aaa"></div>
```
```css
.aaa {
    font-size: 16px;
    border: 1px solid #000;
    padding: 4px;
}
```

在 html 里指定 class，然后在 css 里定义这个 class 的样式。

也就是 class 里包含多个样式：

![[b73533a071d00568af869e357847a096_MD5.png]]

而原子化 css 是这样的写法：

```html
<div class="text-base p-1 border border-black border-solid"></div>
```

```css
.text-base {
    font-size: 16px;
}
.p-1 {
    padding: 4px;
}
.border {
    border-width: 1px;
}
.border-black {
    border-color: black;
}
.border-solid {
    border-style: solid;
}
```

![[1bda23c86c570c35edf79ab8ce4e5b75_MD5.png]]

定义一些细粒度的 class，叫做原子 class，然后在 html 里直接引入这些原子化的 class。

这个原子化 css 的概念还是很好理解的，但它到底有啥好处呢? 它解决了什么问题？

口说无凭，我们试下 tailwind 就知道了，它就是一个提供了很多原子 class 的 css 框架。

我们通过 crerate-react-app 创建一个 react 项目：

```
npx create-react-app tailwind-test
```
然后进入 tailwind-test 目录，执行 

```
npm install -D tailwindcss

npx tailwindcss init
```
安装 tailwindcss 依赖，创建 tailwindcss 配置文件。

![[e8d664967b4ee1d4044157325b9c2d22_MD5.png]]

tailwind 实际上是一个 postcss 插件，因为 cra 内部已经做了 postcss 集成 tailwind 插件的配置，这一步就不用做了：

![[4b89f2c40abd80f863f01fb074f5af88_MD5.png]]

然后在入口 css 里加上这三行代码：

![[b51b4a25732662005de82b3e2b60e547_MD5.png]]

这三行分别是引入 tailwind 的基础样式、组件样式、工具样式的。

之后就可以在组件里用 tailwind 提供的 class 了：

```javascript
import './App.css';

function App() {
  return (
    <div className='text-base p-1 border border-black border-solid'>guang</div>
  );
}

export default App;
```
我们执行把开发服务跑起来:

```
npm run start 
```

可以看到，它正确的加上了样式：

![[3c187adb920afb8a6a22faee313d29a6_MD5.png]]

用到的这些原子 class 就是 tailwind 提供的：

![[c939064511d0fd0bb660f9251329bf7d_MD5.png]]

这里的 p-1 是 padding:0.25rem，你也可以在配置文件里修改它的值：

![[a84de5424c93d6cf5be3a2304908cf1e_MD5.png]]

在 tailwind.config.js 的 theme.extend 修改 p-1 的值，设置为 30px。

刷新页面，就可以看到 p-1 的样式变了：

![[8700d7c5bb52f62a4037cc6955e36ab6_MD5.png]]

.text-base 是 font-size、line-height 两个样式，这种通过数组配置：

![[0d3a3e62448b63a448cb1c661889919c_MD5.png]]

![[c4adf90c80332dde3da4933cd23ad0e5_MD5.png]]

也就是说所有 tailwind 提供的所有内置原子 class 都可以配置。

但这些都是全局的更改，有的时候你想临时设置一些值，可以用 [] 语法。

比如 text-[14px]，它就会生成 font-size:14px 的样式：

![[211c717f95128f674aba4008c7464c52_MD5.png]]

比如 aspect-[4/3]，就是这样的样式：

![[2dbc06d432233b76449a8219fe4346ea_MD5.png]]

我们平时经常指定 hover 时的样式，在 tailwind 里怎么指定呢？

很简单，这样写：

![[2cd8781642bee546d40a0d5e283bc867_MD5.png]]

生成的就是带状态的 class：

![[78003ae7156f5929d33094f79cbb50a8_MD5.png]]

![[914c1c3922c80603734803257ca738ae_MD5.gif]]

此外，写响应式的页面的时候，我们要指定什么宽度的时候用什么样式，这个用 tailwind 怎么写呢？

也是一样的写法：

![[b461a2b23ca894f01f875b42998d4056_MD5.png]]

![[590ebf6e9a027d4523abf6c88cf50844_MD5.gif]]

生成的是这样的代码：

![[bcf917f658ae0cb9ac45a1b7e58470fa_MD5.png]]

这个断点位置自然也是可以配置的：

![[967d630406c61e95256af68bc27d145e_MD5.png]]

可以看到 md 断点对应的宽度变了：

![[b5b3d51a27d83fa539df86619aac7600_MD5.png]]

![[37c34cfe6cbf0911dc0618be6bf6a13c_MD5.gif]]

光这些就很方便了。

之前要这么写：

```html
<div class="aaa"></div>
```
```css
.aaa {
    background: red;
    font-size: 16px;
}

.aaa:hover {
    font-size: 30px;
}

@media(min-width:768px) {
    .aaa {
        background: blue;
    }
}
```

现在只需要这样：

```html
<div class="text-[14px] bg-red-500 hover:text-[30px] md:bg-blue-500"></div>
```

省去了很多样板代码，还省掉了 class 的命名。

并且这些 class 都可以通过配置来统一修改。

感受到原子化 css 的好处了么？

tailwind 文档提到了 3 个好处：

![[b11039b516fdba021fd6efb397b2bcb1_MD5.png]]

不用起 class 名字，这点简直太爽了，我就经常被起 class 名字折磨。

css 不会一直增长，因为如果你用之前的写法可能是这样的：

![[8be32a9b91425c718c8531fe924216ba_MD5.png]]

多个 class 里都包含了类似的样式，但你需要写多次，而如果用了原子 class，就只需要定义一次就好了。

css 没有模块作用域，所以可能你在这里加了一个样式，结果别的地方样式错乱了。

而用原子 class 就没这种问题，因为样式是只是作用在某个 html 标签的。

我觉得光这三点好处就能够说服我用它了，特别是不用起 class 名字这点。

当然，社区也有一些反对的声音，我们来看看他们是怎么说的：

**一堆 class，可读性、可维护性太差了**

真的么？

这种把 css 写在 html 里的方式应该是更高效才对。

想想为啥 vue 要创造个单文件组件的语法，把 js、css、template 放在一个文件里写，不就是为了紧凑么？

之前你要在 css、js 文件里反复跳来跳去的，查找某个 class 的样式是啥，现在不用这么跳了，直接在 html 里写原子样式，它不香么？

而且 tailwindcss 就前面提到的那么几个语法，没啥学习成本，很容易看懂才对。

**但是还要每次去查文档哪些 class 对应什么样式呀**

这个可以用 tailwind css 提供的 vscode 插件来解决：

![[506737f5f263bd20e7ff360081e4070e_MD5.png]]

安装这个 Tailwind CSS IntelliSense 之后的体验是这样的：

![[c8693c90b60e7943c85cde8c1a48d4fd_MD5.gif]]

有智能提示，可以查看它对应的样式。

不需要记。

**难以调试**

在 chrome devtools 里可以直接看到有啥样式，而且样式之间基本没有交叉，很容易调试：

![[835d0645f89868a0ad2fd757e6f3a71e_MD5.png]]

相反，我倒是觉得之前那种写法容易多个 class 的样式相互覆盖，还要确定优先级和顺序，那个更难调试才对：

![[20b396f6c734d0c6d0d25d11ff612907_MD5.png]]

**类型太长了而且重复多次**

![[78cc5597e8a18436d58824f4dbbb7227_MD5.png]]

这种问题可以用 @layer @apply 指令来扩展：

![[c123836476ba6ccc0c64d4a48cc0e204_MD5.png]]

前面讲过 @tailwind 是引入不同的样式的，而 @layer 就是在某一层样式做修改和扩充，里面可以用 @apply 应用其他样式。

效果是这样的：

![[1a1c9528814a82d7b1d54ad3014da8f3_MD5.png]]

![[0342b133696f0b022ecad55b42a5fa1d_MD5.png]]

**内置 class 不能满足我的需求**

其实上面那个 @layer 和 @apply 就能扩展内置原子 class。

但如果你想跨项目复用，那可以开发个 tailwind 插件

```javascript
const plugin = require('tailwindcss/plugin');

module.exports = plugin(function({ addUtilities }) {
    addUtilities({
        '.guang': {
            background: 'blue',
            color: 'yellow'
        },
        '.guangguang': {
            'font-size': '70px'
        }
    })
})
```

在 tailwind.config.js 里引入：

![[704d85f0a7965bbfb534be2497475ba4_MD5.png]]

这样就可以用这个新加的原子 class 了：

![[e1b2aa0feb5388903f195abeda696f8b_MD5.png]]

![[bee7be2382a781274a0e6fa2ec15d083_MD5.png]]

插件的方式或者 @layer 的方式都可以扩展。

**tailwind 的 class 名和我已有的 class 冲突了咋办？**

比如我本来有个 border 的 class：

![[d5db6e412ecd3706cbc32ecda69599a2_MD5.png]]

而 tailwind 也有，不就冲突了么？

这个可以通过加 prefix 解决：

![[6402f444691323ba68fd159203f744b8_MD5.png]]

不过这样所有的原子 class 都得加 prefix 了：

![[8c93f9a7be0886e1054c1e56fa159ef3_MD5.png]]

![[b85b7693db90eb538cfcd46f702fa569_MD5.png]]

知道了什么是原子 css 以及 tailwind 的用法之后，我们再来看看它的实现原理。

tailwind 可以单独跑，也可以作为 postcss 插件来跑。这是因为如果单独跑的话，它也会跑起 postcss，然后应用 tailwind 的插件：

![[b7ddf6e3c7938fc216c921b60aa75886_MD5.png]]

所以说，**tailwind 本质上就是个 postcss 插件**。

postcss 是一个 css 编译器，它是 parse、transform、generate 的流程。

在 [astexplorer.net](https://astexplorer.net/#/gist/6fe6d6027cbfdd64359fb203d9df378b/68583ac053782c87e3b85c1c56553985c410b02e) 可以看到 postcss 的 AST：

![[2289e44d20afb89380b9714ae9294e7b_MD5.png]]

而 postcss 就是通过 AST 来拿到 @tailwind、@layer、@apply 这些它扩展的指令，分别作相应的处理，也就是对 AST 的增删改查。

那它是怎么扫描到 js、html 中的 className 的呢？

这是因为它有个 extractor （提取器）的东西，用来通过正则匹配文本中的 class，之后添加到 AST 中，最终生成代码。

extractor 的功能看下测试用例就明白了：

![[981892155c4e4a8d44dffa8c65d471a5_MD5.png]]

所以说，**tailwind 就是基于 postcss 的 AST 实现的 css 代码生成工具，并且做了通过 extractor 提取 js、html 中 class 的功能。**

tailwind 还有种叫 JIT 的编译方式，这个原理也容易理解，本来是全部引入原子 css 然后过滤掉没有用到的，而 JIT 的话就是根据提取到的 class 来动态引入原子 css，更高效一点。

最后，为啥这个 css 框架叫 tailwind 呢？

因为作者喜欢叫做 kiteboarding 风筝冲浪的运动。

就是这样的，一个风筝，一个冲浪板：

![[9e7531e1f67bbeb187fe0490e28f5366_MD5.png]]

这种运动在顺风 tailwind 和逆风 headwind 下有不同的技巧。而 tailwind 的时候明显更加省力。

所以就给这个 css 框架起名叫 tailwind 了。

确实，我也觉得用这种方式来写 css 更加省力、高效，不用写 class 名字了，代码更简洁了，还不容易样式冲突了。

案例代码上传了[小册仓库](https://github.com/QuarkGluonPlasma/react-course-code/tree/main/tailwind-test)

## 总结

tailwind 是一个流行的原子化 css 框架。

传统 css 写法是定义 class，然后在 class 内部写样式，而原子化 css 是预定义一些细粒度 class，通过组合 class 的方式完成样式编写。

tailwind 用起来很简单：

所有预定义的 class 都可以通过配置文件修改值，也可以通过 aaa-[14px] 的方式定义任意值的 class。

所有 class 都可以通过 hover:xxx、md:xxx 的方式来添加某个状态下的样式，响应式的样式，相比传统的写法简洁太多了。

它的优点有很多，我个人最喜欢的就是不用起 class 的名字了，而且避免了同样的样式在多个 class 里定义多次导致代码重复，并且局部作用于某个标签，避免了全局污染。

它可以通过 @layer、@apply 或者插件的方式扩展原子 class，支持 prefix 来避免 class 名字冲突。

tailwind 本质上就是一个 postcss 插件，通过 AST 来分析 css 代码，对 css 做增删改，并且可以通过 extractor 提取 js、html 中的 class，之后基于这些来生成最终的 css 代码。

是否感受到了 tailwind 的简洁高效，易于扩展？就是这些原因让它成为了最流行的原子化 css 框架。
