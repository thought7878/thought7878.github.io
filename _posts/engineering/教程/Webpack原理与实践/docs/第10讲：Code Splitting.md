玩转 Webpack 高级特性应对项目优化需求（下）

今天我将继续和你分享 Webpack 另外的一个高级特性，Code Splitting（分块打包）。

### All in One 的弊端

通过 Webpack 实现前端项目整体模块化的优势固然明显，但是它也会存在**一些弊端：** 它最终会将我们所有的代码打包到一起。试想一下，如果我们的应用非常复杂，模块非常多，那么这种 All in One 的方式就会*导致打包的结果过大，甚至超过 4～5M*。
在绝大多数的情况下，应用刚开始工作时，*并不是所有的模块都是必需的*。如果这些模块全部被打包到一起，即便应用只需要一两个模块工作，也必须先*把 bundle.js 整体加载进来*，而且前端应用一般都是运行在浏览器端，这也就意味着*应用的响应速度会受到影响，也会浪费大量的流量和带宽*。
所以这种 All in One 的方式并不合理，**更为合理的方案**是*把打包的结果按照一定的规则分离到多个 bundle 中，然后根据应用的运行需要按需加载*。这样就可以降低启动成本，提高响应速度。

可能你会联想到我们在开篇词中讲过，Webpack 就是通过把项目中散落的模块打包到一起，从而提高加载效率，那么为什么这里又要分离？这不是自相矛盾吗？
其实这并不矛盾，*只是物极必反罢了。Web 应用中的资源受环境所限，太大不行，太碎更不行*。因为我们开发过程中划分模块的颗粒度一般都会非常的细，很多时候一个模块只是提供了一个小工具函数，并不能形成一个完整的功能单元。
如果我们不将这些资源模块打包，直接按照开发过程中划分的模块颗粒度进行加载，那么运行一个小小的功能，就需要加载非常多的资源模块。
再者，*目前主流的 HTTP 1.1 本身就存在一些缺陷*，例如：
- 同一个域名下的并行请求是有限制的；
- 每次请求本身都会有一定的延迟；
- 每次请求除了传输内容，还有额外的请求头，大量请求的情况下，这些请求头加在一起也会浪费流量和带宽。
*综上所述，模块打包肯定是必要的，但当应用体积越来越大时，我们也要学会变通*。

### Code Splitting

**为了解决打包结果过大导致的问题**，Webpack 设计了一种分包功能：Code Splitting（代码分割）。

Code Splitting 通过*把项目中的资源模块按照我们设计的规则打包到不同的 bundle 中*，从而降低应用的启动成本，提高响应速度。

Webpack 实现**分包的方式主要有两种：**
- 根据业务不同配置多个打包入口，输出多个打包结果；
- 结合 ES Modules 的`动态导入`（Dynamic Imports）特性，*按需加载模块*。

#### 多入口打包

**多入口打包**一般适用于传统的多页应用程序，*最常见的划分规则*就是一个页面对应一个打包入口，对于不同页面间公用的部分，再提取到公共的结果中。

Webpack 配置多入口打包的方式非常简单，这里我准备了一个[相应的示例](https://github.com/zce/webpack-multi-entry)，具体结构如下：

> 示例源码
> 
> - GitHub：[https://github.com/zce/webpack-multi-entry](https://github.com/zce/webpack-multi-entry)
> - CodeSandbox：[https://codesandbox.io/s/github/zce/webpack-multi-entry](https://codesandbox.io/s/github/zce/webpack-multi-entry)

```text
.
├── dist
├── src
│   ├── common
│   │   ├── fetch.js
│   │   └── global.css
│   ├── album.css
│   ├── album.html
│   ├── album.js
│   ├── index.css
│   ├── index.html
│   └── index.js
├── package.json
└── webpack.config.js
```

这个示例中有*两个页面，分别是 index 和 album*。代码组织的逻辑也很简单：
- index.js 负责实现 index 页面功能逻辑；
- album.js 负责实现 album 页面功能逻辑；
- global.css 是公用的样式文件；
- fetch.js 是一个公用的模块，负责请求 API。

我们回到配置文件中，这里我们尝试为这个案例配置多入口打包，具体配置如下：

```javascript
// ./webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  entry: {
    index: './src/index.js',
    album: './src/album.js'
  },
  output: {
    filename: '[name].bundle.js' // [name] 是入口名称
  },
  // ... 其他配置
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/index.html',
      filename: 'index.html'
    }),
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/album.html',
      filename: 'album.html'
    })
  ]
}
```

一般 `entry 属性`中只会配置一个打包入口，如果我们需要*配置多个入口，可以把 entry 定义成一个对象*。
注意：这里 entry 是定义为对象而不是数组，如果是数组的话就是把多个文件打包到一起，还是一个入口。
*在这个对象中一个属性就是一个入口*，*属性名称*就是这个入口的名称，*值*就是这个入口对应的文件路径。那我们这里配置的就是 index 和 album 页面所对应的 JS 文件路径。
一旦我们的入口配置为多入口形式，那输出文件名也需要修改，因为两个入口就有两个打包结果，不能都叫 bundle.js。我们可以在这里*使用 [name] 这种占位符来输出动态的文件名，[name] 最终会被替换为入口的名称*。
除此之外，在配置中还通过 `html-webpack-plugin` 分别为 index 和 album 页面*生成了对应的 HTML 文件*。

完成配置之后，我们就可以打开命令行终端，运行 Webpack 打包，那此次打包会有两个入口。打包完成后，我们找到输出目录，这里就能看到两个入口文件各自的打包结果了，如下图所示：

![[7acae5d760e02395f4df420f1eadef98_MD5.png]]

但是这里还有*一个小问题*，我们打开任意一个输出的 HTML 文件，具体结果如下图：

![[2a0b729b4b25daef30bc55790cc3d3c4_MD5.png]]

你就会发现 *index 和 album 两个打包结果都被页面载入了，而我们希望的是每个页面只使用它对应的那个输出结果*。
所以这里还需要修改配置文件，我们回到配置文件中，找到输出 HTML 的插件，*默认这个插件会自动注入所有的打包结果*，**如果需要指定所使用的 bundle，通过 HtmlWebpackPlugin 的 `chunks 属性`来设置**。我们分别为两个页面配置使用不同的 chunk，具体配置如下：

> TIPS：每个打包入口都会形成一个独立的 chunk（块）。

```javascript
// ./webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  entry: {
    index: './src/index.js',
    album: './src/album.js'
  },
  output: {
    filename: '[name].bundle.js' // [name] 是入口名称
  },
  // ... 其他配置
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/index.html',
      filename: 'index.html',
      chunks: ['index'] // 指定使用 index.bundle.js
    }),
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/album.html',
      filename: 'album.html',
      chunks: ['album'] // 指定使用 album.bundle.js
    })
  ]
}
```

完成以后我们再次回到命令行终端，然后运行打包，打包结果如下图：

![[b5a27ff4abb981725f617176c352bad0_MD5.png]]

这一次打包的结果就完全正常了。

那这就是配置多入口打包的方法，以及如何指定在 HTML 中注入的 bundle。

**提取公共模块**

多入口打包本身非常容易理解和使用，但是它也存在**一个小问题**，就是*不同的入口中一定会存在一些公共使用的模块，如果按照目前这种多入口打包的方式，就会出现多个打包结果中有相同的模块的情况*。
例如，我们上述案例中，index 入口和 album 入口中就共同使用了 global.css 和 fetch.js 这两个公共的模块。这里是因为我们的示例比较简单，所以重复的影响没有那么大，但是如果我们公共使用的是 jQuery 或者 Vue.js 这些体积较大的模块，那影响就会比较大，不利于公共模块的缓存。

所以，我们还需要*把这些公共的模块提取到一个单独的 bundle 中*。Webpack 中实现公共模块提取非常简单，我们只需要**在优化配置中开启 `splitChunks` 功能就可以了**，具体配置如下：

```javascript
// ./webpack.config.js
module.exports = {
  entry: {
    index: './src/index.js',
    album: './src/album.js'
  },
  output: {
    filename: '[name].bundle.js' // [name] 是入口名称
  },
  optimization: {
    splitChunks: {
      // 自动提取所有公共模块到单独 bundle
      chunks: 'all'
    }
  }
  // ... 其他配置
}
```

我们回到配置文件中，这里在 optimization 属性中添加 splitChunks 属性，那这个属性的值是一个对象，这个对象需要*配置一个 `chunks` 属性，我们这里将它设置为 all，表示所有公共模块都可以被提取*。

完成以后我们打开命令行终端，再次运行 Webpack 打包，打包结果如下图：

![[22f1f49c6da6aa685ea9eec50f36387c_MD5.png]]

此时在我们的 dist 下就会额外生成一个 JS 文件，*在这个文件中就是 index 和 album 中公共的模块部分了*。

除此之外，splitChunks 还支持很多高级的用法，可以实现各种各样的分包策略，这些我们可以在[文档](https://webpack.js.org/plugins/split-chunks-plugin/)中找到对应的介绍。

#### 动态导入
##### What
除了多入口打包的方式，**Code Splitting 更常见的实现方式**还是结合 ES Modules 的动态导入特性，从而实现按需加载。
按需加载是开发浏览器应用中一个非常常见的需求。一般我们常说的按需加载指的是加载数据或者加载图片，但是我们**这里所说的按需加载**，指的是在应用运行过程中，需要某个资源模块时，才去加载这个模块。*这种方式极大地降低了应用启动时需要加载的资源体积，提高了应用的响应速度，同时也节省了带宽和流量*。

Webpack 中支持***使用 ES Modules 的动态导入的特性*实现模块的按需加载**，而且*所有动态导入的模块都会被自动提取到单独的 bundle 中，从而实现分包*。

##### 优点
**相比于多入口的方式，动态导入更为灵活**，*因为我们可以通过代码中的逻辑去控制需不需要加载某个模块，或者什么时候加载某个模块*。而且我们分包的目的中，很重要的一点就是让模块实现按需加载，从而提高应用的响应速度。

##### 案例
接下来，我们具体来看如何使用动态导入特性，这里我已经设计了*一个可以发挥按需加载作用的场景*，具体效果如下图所示：

![[d7cc92b489cc721d0e6981fe4ee43b51_MD5.gif]]

在这个应用的主体区域，如果我们访问的是首页，它显示的是一个文章列表，如果我们访问的是相册页，它显示的就是相册列表。

回到代码中，我们来看目前的实现方式，具体结构如下：

```text
.
├── src
│   ├── album
│   │   ├── album.css
│   │   └── album.js
│   ├── common
│   │   ├── fetch.js
│   │   └── global.css
│   ├── posts
│   │   ├── posts.css
│   │   └── posts.js
│   ├── index.html
│   └── index.js
├── package.json
└── webpack.config.js
```

文章列表对应的是这里的 posts 组件，而相册列表对应的是 album 组件。在打包入口（index.js）中同时导入了这两个模块，然后根据页面锚点的变化决定显示哪个组件，核心代码如下：

```javascript
// ./src/index.js
import posts from './posts/posts'
import album from './album/album'
const update = () => {
  const hash = window.location.hash || '#posts'
  const mainElement = document.querySelector('.main')
  mainElement.innerHTML = ''
  if (hash === '#posts') {
    mainElement.appendChild(posts())
  } else if (hash === '#album') {
    mainElement.appendChild(album())
  }
}
window.addEventListener('hashchange', update)
update()
```

在这种情况下，就可能产生资源浪费。试想一下：如果用户只需要访问其中一个页面，那么加载另外一个页面对应的组件就是浪费。

如果我们*采用动态导入的方式*，就不会产生浪费的问题了，因为所有的组件都是惰性加载，只有用到的时候才会去加载。具体实现代码如下：

```javascript
// ./src/index.js
// import posts from './posts/posts'
// import album from './album/album'
const update = () => {
  const hash = window.location.hash || '#posts'
  const mainElement = document.querySelector('.main')
  mainElement.innerHTML = ''
  if (hash === '#posts') {
    // mainElement.appendChild(posts())
    // 动态导入
    import('./posts/posts').then(({ default: posts }) => {
      mainElement.appendChild(posts())
    })
  } else if (hash === '#album') {
    // mainElement.appendChild(album())
    // 动态导入
    import('./album/album').then(({ default: album }) => {
      mainElement.appendChild(album())
    })
  }
}
window.addEventListener('hashchange', update)
update()
```

> P.S. 为了动态导入模块，可以将 import 关键字作为函数调用。当以这种方式使用时，*import 函数*返回一个 Promise 对象。这就是 ES Modules 标准中的 [Dynamic Imports](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import#Dynamic_Imports)。

这里我们先*移除 import 这种静态导入*，然后*在需要使用组件的地方通过 import 函数导入指定路径*，那这个方法返回的是一个 Promise。*在这个 Promise 的 then 方法中我们能够拿到模块对象*。由于我们这里的 posts 和 album 模块是*以默认成员导出，所以我们需要解构模块对象中的 default*，先拿到导出成员，然后再正常使用这个导出成员。

完成以后，Webpack Dev Server 自动重新打包，我们再次回到浏览器，此时应用仍然是可以正常工作的。
那我们再回到命令行终端，重新运行打包，然后看看此时的打包结果具体是怎样的。打包完成以后我们打开 dist 目录，具体结果如下图所示：

![[05caaad7fe1d87807e56b6b609f42403_MD5.png]]

此时 dist 目录下就会额外多出三个 JS 文件，*其中有两个文件是动态导入的模块，另外一个文件是动态导入模块中公共的模块*，*这三个文件就是由动态导入自动分包产生的*。

*以上就是动态导入在 Webpack 中的使用。整个过程我们无需额外配置任何地方*，只需要按照 ES Modules 动态导入的方式去导入模块就可以了，Webpack 内部会自动处理分包和按需加载。

如果你使用的是 Vue.js 之类的 SPA 开发框架的话，那你*项目中路由映射的组件就可以通过这种动态导入的方式实现按需加载，从而实现分包*。

#### 魔法注释
##### Why & What
*默认通过动态导入产生的 bundle 文件，它的 name 就是一个序号*，这并没有什么不好，因为大多数时候，在生产环境中我们根本不用关心资源文件的名称。
但是**如果你还是需要给这些 bundle 命名的话，就可以使用 Webpack 所特有的魔法注释去实现**。具体方式如下：

```javascript
// 魔法注释
import(/* webpackChunkName: 'posts' */'./posts/posts')
  .then(({ default: posts }) => {
    mainElement.appendChild(posts())
  })
```

所谓**魔法注释**，就是*在 import 函数的形式参数位置，添加一个行内注释*，这个注释有一个特定的格式：webpackChunkName: ''，这样就可以*给分包的 chunk 起名字了*。

完成过后，我们再次打开命令行终端，运行 Webpack 打包，那此时我们生成 bundle 的 name 就会使用刚刚注释中提供的名称了，具体结果如下：

![[27ae5b6da391c8a5b438102f047b21ee_MD5.png]]

**魔法注释还有个特殊用途：** *如果你的 chunkName 相同的话，那相同的 chunkName 最终就会被打包到一起*。例如，我们这里可以把这两个 chunkName 都设置为 components，然后再次运行打包，那此时这两个模块都会被打包到一个文件中，具体操作如下图所示：

![[2d797d460a83140c6a3581376bf093c8_MD5.png]]

*借助这个特点，就可以根据自己的实际情况，灵活组织动态加载的模块了。*

### 写在最后

最后我们来总结一下今天的核心内容，我们介绍了为什么要进行分包，以及 Webpack Code Splitting 的两种实现方式，分别是多入口打包和动态导入，其中动态导入会更常用到。

在这里，我想跟你再额外聊几句我的看法，*其实从事开发工作就是不断“制造”问题，再不断解决问题*。也正是在这样的一个制造问题解决问题的过程中，行业的技术、标准、工具不断迭代，不断完善，这是一个向好的过程。作为开发人员千万不要怕麻烦，应该多思考，多积累，才能更好地适应，甚至是引领行业的变化。