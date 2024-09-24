
# 视频总结
https://www.bilibili.com/video/BV1Wz4y1B7fn/?spm_id_from=333.788&vd_source=22af953ea4c09540ad1966711a2d53f0

 
- [00:17](https://www.bilibili.com/video/BV1Wz4y1B7fn/?t=17.825957#t=17.83) 问题：有哪些常见的Loader？他们是解决什么问题的？
- 
- [00:43](https://www.bilibili.com/video/BV1Wz4y1B7fn/?t=43.326077#t=43.33) Loader是什么？为什么使用Loader，解决什么问题？
	- 问题：[webpack only understands JavaScript and JSON files. ](https://webpack.js.org/concepts/#loaders)
	- **Loaders** allow webpack to process other types of files and convert them into valid modules that can be consumed by your application and added to the dependency graph.
- 
- [04:20](https://www.bilibili.com/video/BV1Wz4y1B7fn/?t=260.495631#t=04:20.50) [常见的Loader](https://webpack.js.org/loaders) [Link2](https://webpack.js.org/awesome-webpack/#loaders)
	- 
- [05:48](https://www.bilibili.com/video/BV1Wz4y1B7fn/?t=348.325321#t=05:48.33) 答案
	- 回答提示：按照一定的维度进行描述，会更有系统性
	- 他们是解决什么问题的？
		- Webpack默认只能处理JS、JSON，不能处理其他类型的资源。Loader让Webpack能处理其他类型的资源，把他们转换成应用可以消费的有效模块
	- 有哪些常见的Loader？
		- JS
			- babel-loader
			- ts-loader
			- eslint-loader
		- CSS
			- sass-loader，预处理器
			- less-loader
			- style-loader
			- css-loader
			- postcss-loader，后处理器
		- image
			- file-loader
			- url-loader
			- image-loader
- 
- [08:35](https://www.bilibili.com/video/BV1Wz4y1B7fn/?t=515.29527#t=08:35.30) 考察点

# Webpack 中的 Loader：让 Webpack 理解各种文件

**Loader** 是 Webpack 中一个非常核心的概念，它就像一个*翻译官*，*帮助 Webpack 理解和处理各种不同类型的文件*，比如 CSS、图片、字体等。**Webpack 本身只能处理 JavaScript 模块，而 Loader 可以将这些其他类型的文件转换成 JavaScript 模块，从而让 Webpack 可以打包和处理它们**。

## Loader 的作用

- **转换文件类型：** *将非 JavaScript 文件（如 CSS、Less、Sass、TypeScript 等）转换成在 JavaScript 模块可使用的模块，以便 Webpack 打包处理*。
- **提取样式、图片等资源：** 将样式、图片等资源从 JavaScript 代码中提取出来，生成独立的文件，从而优化页面加载速度。
- **处理其他任务：** Loader 可以执行一些其他的任务，比如编译、压缩、转换等。

## Loader 的工作原理

1. **Webpack 从入口文件开始，根据模块的依赖关系，递归找到需要处理的文件。**
2. **Webpack 找到对应的 Loader，将文件交给 Loader 处理。**
3. **Loader 对文件进行转换，生成 JavaScript 模块。**
4. **生成的 JavaScript 模块被添加到依赖图中，Webpack 继续处理下一个模块。**

## Loader 的配置

在 Webpack 配置文件中，通过 `module.rules` 属性来配置 Loader。每个规则包含一个正则表达式，用于匹配要处理的文件类型，以及对应的 Loader。


```JavaScript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      // ...其他规则
    ]
  }
};
```

## 常见的 Loader

### JS
#### **babel-loader**
- **作用：** 将 ES6+ 语法转换为 ES5，以便在老旧浏览器上运行；使用最新的 JavaScript 语法特性。
- **使用场景：** 现代 JavaScript 项目中，为了使用最新的 JavaScript 语法特性。除了转换 JavaScript，babel-loader 还可以与其他 loader 配合，比如 `babel-loader` + `jsx-loader` 处理 JSX 语法。

#### **ts-loader**
- **作用：** 将 `.ts` 和 `.tsx` 文件编译为 JavaScript 文件。
- 

### CSS
#### **css-loader**
- **作用：** 将 CSS 模块加载到 JavaScript 中，作为模块的依赖。
- **使用场景：** 在 JavaScript 中引入 CSS 文件，实现模块化样式管理。

#### **style-loader**
- **作用：** 将 css-loader 加载的 CSS 插入到 HTML 的 `<style>` 标签中，让样式生效。
- **使用场景：** 与 css-loader 配合使用，将 CSS 模块注入到页面中。

#### **less-loader**
- **作用：** 将 Less 预处理器文件编译为 CSS。
- **使用场景：** 使用 Less 编写样式，提高 CSS 代码的可维护性。

#### **sass-loader**
- **作用：** 将 Sass/Scss 预处理器文件编译为 CSS。
- **使用场景：** 使用 Sass/Scss 编写样式，支持嵌套、变量、函数等高级特性。

#### **stylus-loader**
- **作用：** 将 Stylus 预处理器文件编译为 CSS。
- **使用场景：** 使用 Stylus 编写样式，支持动态属性、混合等特性。


### 其他文件（如图片、字体等）
#### **file-loader**
- **作用：** 处理静态文件（如图片、字体等），将其复制到输出目录，并返回一个指向这些资源的 URL。
- **使用场景：** 处理图片、字体等静态资源。

#### **url-loader**
- **作用：** 与 file-loader 类似，但可以将较小的文件直接嵌入到 CSS 或 JavaScript 中，减少 HTTP 请求。
- **使用场景：** 处理小尺寸的图片、字体等静态资源。

## 总结

Loader 是 Webpack 的核心功能之一，它使得 Webpack 可以处理各种类型的文件，从而构建现代化的 Web 应用。通过合理配置 Loader，我们可以实现模块化、代码分割、样式处理、图片优化等功能，提高开发效率和应用性能。

**想了解更多关于 Loader 的信息，可以查阅 Webpack 的官方文档：** [https://webpack.js.org/loaders/](https://webpack.js.org/loaders/)