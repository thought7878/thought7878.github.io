> 总结 Webpack 的全部的知识点，不要介绍，只要知识点

# DS 的思考

好的，我需要总结 Webpack 的所有知识点，但用户要求不要介绍，只要知识点。首先，我需要回忆 Webpack 的核心概念、配置项、常用 loader、插件、优化手段、开发环境配置、与其他工具的集成等等。

首先，Webpack 的`核心概念`有入口(entry)、输出(output)、loader、插件(plugins)、模式(mode)、模块(module)、依赖图(dependency graph)。这些是基础，必须列出来。

然后是`配置项`，比如 entry 可以配置为字符串、数组、对象，output 中的 path、filename、publicPath，还有模块的 rules 配置 loader，plugins 数组配置插件，还有 resolve、devServer、optimization 等配置。

接下来是`loader`，常用的有 babel-loader 处理 ES6+，css-loader 和 style-loader 处理 CSS，sass-loader 处理 Sass，file-loader 和 url-loader 处理图片和字体，还有 ts-loader、vue-loader 等等。每个 loader 的作用需要简要说明。

`插件方面`，HtmlWebpackPlugin 生成 HTML，CleanWebpackPlugin 清理 dist，MiniCssExtractPlugin 提取 CSS，OptimizeCSSAssetsPlugin 优化 CSS，TerserWebpackPlugin 压缩 JS，还有 HotModuleReplacementPlugin 热更新，DefinePlugin 定义环境变量，CopyWebpackPlugin 复制文件，BundleAnalyzerPlugin 分析包大小等。

`优化部分`，代码分割（Code Splitting）、Tree Shaking、Scope Hoisting、缓存（chunkhash、contenthash）、懒加载（动态 import）、压缩代码、使用 CDN、DLLPlugin 预编译库等。

`开发环境配置`，如 devServer 的配置：contentBase、port、hot、proxy 等，source map 的选择，HMR 的使用。

`与其他工具的集成`，比如 Babel、TypeScript、ESLint、Prettier、React/Vue 等框架的配置。

其他知识点，比如`Webpack的工作流程`（初始化、编译、输出）、Tapable 插件系统、loader 和插件的开发、自定义配置、多页面应用配置、环境变量的使用、性能分析工具等。

需要确保涵盖所有主要方面，但不要展开详细说明，只列知识点。可能会有遗漏，需要再仔细检查一遍常见的 Webpack 功能和配置项。比如，是否提到了模块热替换、devtool 的不同模式、externals 配置排除某些库、shimming 处理全局变量、不同模式的配置差异（development/production）等。

另外，可能还需要包括 Webpack 5 的新特性，比如模块联邦（Module Federation）、持久化缓存、资源模块（asset modules）、Top Level Await 支持等。

最后，整理这些知识点，按逻辑分类，确保结构清晰，每个大类下的小点明确。检查是否有重复或遗漏的重要点，比如是否提到了 Webpack 的 CLI 命令，如 webpack、webpack-dev-server，或者配置文件的不同写法（导出对象、函数、数组）。

可能需要分几个主要部分，比如核心概念、配置项、Loader、插件、优化、开发环境、生产环境、高级特性、工具集成等。确保每个部分下的知识点全面且准确，不涉及具体使用方法，只列出名称或功能关键词。

# Webpack 知识点清单

参考：[[Webpack 目录]]、[[Webpack 学习计划-DS]]

---

## 前置知识

### Why、What

参考：[[Why、What]]

### 打包、编译、压缩

参考：[[打包]]、[[编译]]

## 一、核心概念

### 核心作用

参考：[[Why、What]]

#### 模块打包

将分散的模块合并为少数文件

#### 资源处理

- 将任意资源转换为 JS 可识别的模块。
- 编译（TypeScript/Sass）、压缩、代码分割。

### 概念和术语
#### 基本概念
**入口 (Entry)**
- 构建的入口文件。
- 单入口、多入口、动态入口  
  参考：[[entry]]

**输出 (Output)**
- 构建的输出配置，包括输出路径、文件名等。
- `path`, `filename`, `publicPath`, `chunkFilename`, `library`  
  参考：[[output]]

**模块 (Module)**
- Webpack 中的模块，可以是 JavaScript 文件、CSS 文件、图片等。
- `rules` 配置 (`test`, `use`, `exclude`, `include`)  
  参考：[[module]]

**Loader**
- *编译JS、CSS；转换非 JS 资源*（如 CSS、图片、字体），将不同类型的模块转换为 Webpack 可以处理的 JavaScript 可以使用的模块

**插件 (Plugins)**
- 用于*扩展 Webpack 的功能*（打包优化、资源管理、环境注入等），可以在构建过程的不同阶段执行自定义的任务。

**模式 (Mode)**
- `development`、`production`、`none`  
  参考：[[mode]]

#### 高级概念
**依赖图 (Dependency Graph)**
- 模块和模块间的依赖关系 
  参考：[[依赖图 (Dependency Graph)-DB]]

**Compiler：** 
- 代表整个 Webpack 编译过程，负责整个构建流程的控制。

**Compilation：** 
- 代表一次特定的构建，包含了当前的模块资源、编译生成资源、变化的文件等信息。

**Chunk：** 
- Webpack 打包生成的代码块，*一个 chunk 可以包含多个 module*。
  参考：[[Chunk-DS]]
  
**Bundle:** 
- 最终输出的打包文件，*一个 bundle 可以包含一个或多个 chunk*。

#### 代码分割（Code Splitting）
参考：[[代码分割（Code Splitting）-DS]]、[[SplitChunks-DS]]


---

## 二、配置项

参考：[[配置项、完整配置文件]]

### 1. 基础配置

| 配置项    | 说明                                     |
| --------- | ---------------------------------------- |
| `entry`   | 入口文件（单入口、多入口、动态入口）     |
| `output`  | 输出文件配置（路径、文件名、公共路径等） |
| `context` | 基础上下文路径（默认 `process.cwd()`）   |

参考：[[entry]]、[[output]]、[[_posts/engineering/webpack/配置项/context|context]]

### 2. 模块处理

| 配置项         | 说明                                   |
| -------------- | -------------------------------------- |
| `module.rules` | 定义 Loader 规则（处理 JS/CSS/图片等） |
| `resolve`      | 模块解析规则（别名、扩展名、主文件等） |
| `externals`    | 排除外部依赖（如 CDN 引入的库）        |

参考：[[module]]、[[resolve]]、[[externals]]

### 3. 插件

| 配置项    | 说明                                        |
| --------- | ------------------------------------------- |
| `plugins` | 插件列表（HTML 生成、环境变量注入、压缩等） |

参考：[[plugins]]

### 4. 开发工具

| 配置项      | 说明                                                     |
| ----------- | -------------------------------------------------------- |
| `devServer` | 开发服务器配置（端口、代理、热更新等）                   |
| `devtool`   | 源码映射模式（`source-map`、`eval-cheap-source-map` 等） |

参考：[[devServer]]、[[devTool]]

### 5. 优化与模式

| 配置项         | 说明                                      |
| -------------- | ----------------------------------------- |
| `optimization` | 代码分割、压缩、Tree Shaking 等优化配置   |
| `mode`         | 构建模式（`development` 或 `production`） |

参考：[[optimization]]、[[mode]]

### 6. 高级配置

| 配置项        | 说明                                     |
| ------------- | ---------------------------------------- |
| `performance` | 性能提示（文件大小限制等）               |
| `stats`       | 控制构建输出信息（日志级别）             |
| `experiments` | 实验性功能（如 WebAssembly、异步模块等） |

参考：[[_posts/engineering/webpack/配置项/performance|performance]]、[[stats]]

---

## 三、常见的 Loader

参考：[[常见的Loader]]

**JavaScript 处理**

- `babel-loader`（转译 ES6+、JSX） 参考：[[babel-loader]]
- `ts-loader`（编译 TypeScript）

**样式处理**

- `css-loader`（解析 CSS 依赖）
- `style-loader`（注入 CSS 到 DOM）
- `sass-loader`/`less-loader`（编译 Sass/Less）
- `postcss-loader`（Autoprefixer 等）

**资源处理**

- `file-loader`（处理文件路径）
- `url-loader`（转 Base64 小文件）
- `raw-loader`（导入文件为字符串）

**框架支持**

- `vue-loader`（Vue 单文件组件）
- `svg-react-loader`（SVG 转 React 组件）

---

## 四、常见的插件

参考：[[常见的Plugin]]

**资源清理**

- `CleanWebpackPlugin`（清理构建目录）

**HTML 管理**

- `HtmlWebpackPlugin`（生成 HTML 文件）

**CSS 优化**

- `MiniCssExtractPlugin`（提取 CSS 为独立文件）
- `OptimizeCSSAssetsPlugin`（压缩 CSS）

**JS 压缩**

- `TerserWebpackPlugin`（压缩 JS）

**热更新**

- `HotModuleReplacementPlugin`（模块热替换）

**环境变量**

- `DefinePlugin`（注入全局常量）

**文件复制**

- `CopyWebpackPlugin`（复制静态文件）

**包分析**

- `BundleAnalyzerPlugin`（可视化分析包体积）

---

## 构建流程

参考：[[Webpack的构建流程-DB]]、[[Webpack的构建流程-DS]]、[[Webpack的构建流程-DS3]]

---

## 优化构建性能

参考：[[优化构建性能-DS]]、[[实际案例]]

---

## 优化应用性能

参考：[[使用Webpack优化应用的性能]]、[[SplitChunks-DS]]、[[代码分割（Code Splitting）-DS]]、[[作用域提升（Scope Hoisting）-DS]]、[[Tree-shaking-DS]]

**代码分割 (Code Splitting)**

- 入口分割、动态导入 (`import()`)、`splitChunks`

**Tree Shaking**

- 移除未引用代码（需 ES Module 语法）

**Scope Hoisting**

- 合并模块作用域，减少闭包

**缓存优化**

- `[hash]`/`[chunkhash]`/`[contenthash]`（文件名哈希）
- `cache-loader`（缓存 Loader 结果）

**懒加载 (Lazy Loading)**

- 动态 `import()` 按需加载模块

**DLL 预编译**

- `DllPlugin` + `DllReferencePlugin` 提升构建速度

**CDN 加速**

- 配置 `publicPath` 指向 CDN 地址

---

## 开发 Loader & Plugin

参考：[[自定义 Loader-DS]]、[[自定义 Plugin-DS]]

---

## 、配置开发环境

32. **Source Map**
    - `devtool` 配置（`eval`、`source-map`、`cheap-module-source-map` 等）
33. **模块热替换 (HMR)**
    - 实时更新模块不刷新页面
34. **代理配置 (Proxy)**
    - 解决跨域问题
35. **ESLint 集成**
    - `eslint-webpack-plugin` 实时校验代码

---

## 、配置生产环境

36. **代码压缩**
    - JS（Terser）、CSS（CSSNano）、HTML（HtmlWebpackPlugin 压缩）
37. **资源指纹**
    - 文件名添加哈希（缓存控制）
38. **环境变量注入**
    - 区分开发/生产环境配置
39. **Bundle 分析**
    - 使用 `webpack-bundle-analyzer` 优化体积

---

## 、高级特性

40. **模块联邦 (Module Federation)**
    - Webpack 5 支持微前端架构
41. **持久化缓存**
    - `cache: { type: 'filesystem' }` 加速二次构建
42. **资源模块 (Asset Modules)**
    - Webpack 5 内置 `asset/resource`、`asset/inline`
43. **Top Level Await**
    - 支持在顶层使用 `await`
44. **Loader/Plugin 开发**
    - 自定义 Loader（函数格式）、插件（Tapable 钩子）

---

## 、工具集成

45. **Babel**
    - `@babel/core`、`@babel/preset-env`、`babel.config.js`
46. **TypeScript**
    - `tsconfig.json`、`ts-loader`/`babel-loader`
47. **ESLint/Prettier**
    - 代码规范与格式化
48. **框架集成**
    - React（`@babel/preset-react`）、Vue（`vue-loader`）

---

## 、其他

49. **Webpack CLI**
    - `webpack`、`webpack-dev-server`、`--config` 指定配置文件
50. **多配置导出**
    - 配置文件导出数组或函数（多环境配置）
51. **Shimming**
    - `ProvidePlugin` 自动加载模块（如全局 jQuery）
52. **性能分析**
    - `speed-measure-webpack-plugin` 测量构建耗时

---

# 总结

此清单涵盖 Webpack 核心配置、Loader/插件、优化策略、开发/生产环境实践及高级特性，适用于快速查阅和系统性学习。
