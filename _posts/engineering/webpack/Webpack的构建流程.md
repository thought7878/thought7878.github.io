
# 构建流程
Webpack 的构建过程，大致上可划分为**三个阶段：**

![[engineering/教程/Webpack5 核心原理与应用实践/media/0007aa99d42380bfd9ce3abfce8fadf9_MD5.webp]]

- 初始化阶段：
	- 主要是根据配置信息设置/初始化内置的各类插件。
- 构建阶段/Make：从`entry`模块开始，执行
	- 读入文件内容；
	- 调用 Loader 转译文件内容；
	- 调用 [acorn](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Facorn) 生成 AST 结构；
	- 分析 AST，确定模块依赖列表；
	- 遍历模块依赖列表，对每一个依赖模块重新执行上述流程，直到生成完整的模块依赖图 —— ModuleGraph 对象。
- 生成阶段/Seal：
	- 遍历模块依赖图，对每一个模块执行：
		- 代码转译？？？，如 `import` 转换为 `require` 调用；
		- 分析运行时依赖。
	- 合并模块代码与运行时代码，生成 chunk；
	- 执行产物优化操作，如 Tree-shaking；
	- 将最终结果写出到产物文件。


## 初始化阶段
 
### 读取配置文件

Webpack 首先会读取项目根目录下的`webpack.config.js`（或其他指定的配置文件）。这个配置文件包含了一系列的配置选项，如`entry`（入口）、`output`（输出）、`module`（模块）、`plugins`（插件）等。*这些配置信息将指导 Webpack 整个构建过程*。例如，一个简单的`webpack.config.js`可能如下：

```js
const path = require('path');
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  },
  module: {
    rules: [
      // 模块处理规则，如加载CSS、图片等
    ]
  },
  plugins: [
    // 插件配置
  ]
};
```

### 创建 Compiler 对象
Webpack *根据读取的配置文件*创建一个`Compiler`对象。这个`Compiler`对象是 Webpack 的*核心引擎*，*它包含了整个构建过程的状态信息和方法*。它会在后续的构建过程中协调各个模块的处理、插件的调用等操作。例如，`Compiler`对象*可以触发构建过程中的不同阶段*，如`compilation`阶段。

### 加载插件（Plugin）

在初始化阶段，Webpack 会*遍历配置文件中的`plugins`数组，加载并实例化每个插件*。每个插件都可以通过定义的钩子（Hook）方法来介入 Webpack 的构建过程。例如，`html-webpack-plugin`插件会在构建过程中生成 HTML 文件，它在初始化阶段被加载后，会等待合适的构建阶段来执行生成 HTML 文件的操作。



## 编译（Compilation）阶段

### 解析入口文件

`Compiler`对象会从配置的`entry`（入口）点开始解析模块。例如，如果`entry`是`'./src/index.js'`，Webpack 会读取这个`index.js`文件，并*将其作为构建的起点*。在解析过程中，会识别文件中的`import`或`require`语句，从而确定需要加载的其他模块。

### 构建模块依赖图

当解析入口文件和其他被引用的模块时，Webpack 会*构建一个模块依赖图（Module Dependency Graph）*。这个图展示了各个模块之间的依赖关系，例如，`index.js`可能依赖于`utils.js`，那么在模块依赖图中就会有一条从`index.js`指向`utils.js`的边。*这种依赖关系的构建有助于 Webpack 正确地处理模块加载顺序等问题*。

### 编译、转换模块（通过 Loader）

对于每个需要处理的模块，Webpack 会根据配置文件中的`module.rules`来选择合适的 Loader 进行处理。*例如*，如果模块是一个`.css`文件，并且配置了`css-loader`和`style-loader`，那么会先使用`css-loader`来解析 CSS 模块，然后使用`style-loader`将解析后的 CSS 样式注入到 HTML 文档中。**这个过程会对各种类型的模块进行转换**，如将 ES6 + 代码转换为 ES5（通过`babel-loader`）、将 LESS/SASS 文件转换为 CSS 文件（通过`less-loader`或`sass-loader`）等。

## 输出（Output）阶段
### 生成资源文件

在模块转换完成后，Webpack 会*根据配置文件中的`output`选项来生成最终的资源文件*。例如，如果`output.filename`配置为`'main.js'`，`output.path`配置为`path.resolve(__dirname, 'dist')`，那么 Webpack 会将打包后的 JavaScript 文件（以及其他通过插件或 Loader 处理后的资源，如 CSS、图片等）输出到`dist`目录下的`main.js`文件中。对于 CSS 文件，如果使用了`extract-css-plugins`等插件，可能会将 CSS 文件单独输出到`dist`目录下的某个 CSS 文件中。

### 输出 HTML 文件（如果有相关插件）

如果配置了`html-webpack-plugin`等生成 HTML 的插件，在这个阶段会根据模板文件和打包后的资源信息生成 HTML 文件。插件会自动将打包后的 JavaScript 和 CSS 文件的引用插入到 HTML 文件的正确位置。例如，生成的 HTML 文件可能会在`<head>`部分插入`<link>`标签来引用 CSS 文件，在`<body>`部分插入`<script>`标签来引用 JavaScript 文件。

### 文件写入磁盘

最后，Webpack 会将生成的所有资源文件（JavaScript、CSS、HTML 以及其他文件）写入到磁盘的相应位置。这个过程完成后，构建过程结束，生成的文件可以用于部署到服务器或者在本地进行开发测试等操作。


# 参考
[[24 真正读懂 Webpack 核心流程]]
[[1.持久化缓存：大幅提升构建性能]]