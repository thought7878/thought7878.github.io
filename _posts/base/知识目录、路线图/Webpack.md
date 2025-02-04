> 总结Webpack的全部的知识点，不要介绍，只要知识点

### DS的思考
好的，我需要总结Webpack的所有知识点，但用户要求不要介绍，只要知识点。首先，我需要回忆Webpack的核心概念、配置项、常用loader、插件、优化手段、开发环境配置、与其他工具的集成等等。

首先，Webpack的`核心概念`有入口(entry)、输出(output)、loader、插件(plugins)、模式(mode)、模块(module)、依赖图(dependency graph)。这些是基础，必须列出来。

然后是`配置项`，比如entry可以配置为字符串、数组、对象，output中的path、filename、publicPath，还有模块的rules配置loader，plugins数组配置插件，还有resolve、devServer、optimization等配置。

接下来是`loader`，常用的有babel-loader处理ES6+，css-loader和style-loader处理CSS，sass-loader处理Sass，file-loader和url-loader处理图片和字体，还有ts-loader、vue-loader等等。每个loader的作用需要简要说明。

`插件方面`，HtmlWebpackPlugin生成HTML，CleanWebpackPlugin清理dist，MiniCssExtractPlugin提取CSS，OptimizeCSSAssetsPlugin优化CSS，TerserWebpackPlugin压缩JS，还有HotModuleReplacementPlugin热更新，DefinePlugin定义环境变量，CopyWebpackPlugin复制文件，BundleAnalyzerPlugin分析包大小等。

`优化部分`，代码分割（Code Splitting）、Tree Shaking、Scope Hoisting、缓存（chunkhash、contenthash）、懒加载（动态import）、压缩代码、使用CDN、DLLPlugin预编译库等。

`开发环境配置`，如devServer的配置：contentBase、port、hot、proxy等，source map的选择，HMR的使用。

`与其他工具的集成`，比如Babel、TypeScript、ESLint、Prettier、React/Vue等框架的配置。

其他知识点，比如`Webpack的工作流程`（初始化、编译、输出）、Tapable插件系统、loader和插件的开发、自定义配置、多页面应用配置、环境变量的使用、性能分析工具等。

需要确保涵盖所有主要方面，但不要展开详细说明，只列知识点。可能会有遗漏，需要再仔细检查一遍常见的Webpack功能和配置项。比如，是否提到了模块热替换、devtool的不同模式、externals配置排除某些库、shimming处理全局变量、不同模式的配置差异（development/production）等。

另外，可能还需要包括Webpack 5的新特性，比如模块联邦（Module Federation）、持久化缓存、资源模块（asset modules）、Top Level Await支持等。

最后，整理这些知识点，按逻辑分类，确保结构清晰，每个大类下的小点明确。检查是否有重复或遗漏的重要点，比如是否提到了Webpack的CLI命令，如webpack、webpack-dev-server，或者配置文件的不同写法（导出对象、函数、数组）。

可能需要分几个主要部分，比如核心概念、配置项、Loader、插件、优化、开发环境、生产环境、高级特性、工具集成等。确保每个部分下的知识点全面且准确，不涉及具体使用方法，只列出名称或功能关键词。


### Webpack 知识点清单

---

#### **一、核心概念**
1. **入口 (Entry)**  
   - 单入口、多入口、动态入口  
2. **输出 (Output)**  
   - `path`, `filename`, `publicPath`, `chunkFilename`, `library`  
3. **模块 (Module)**  
   - 支持的模块类型（JS、CSS、图片等）  
   - `rules` 配置 (`test`, `use`, `exclude`, `include`)  
4. **Loader**  
   - 转换非 JS 资源（如 CSS、图片、字体）  
5. **插件 (Plugins)**  
   - 扩展功能（打包优化、资源管理、环境注入等）  
6. **模式 (Mode)**  
   - `development`、`production`、`none`  
7. **依赖图 (Dependency Graph)**  
   - 模块间的依赖关系解析  

---

#### **二、配置项**
8. **基础配置**  
   - `entry`, `output`, `module`, `plugins`, `mode`, `devtool`  
9. **解析配置 (Resolve)**  
   - `alias`, `extensions`, `modules`  
10. **开发服务器 (DevServer)**  
    - `contentBase`, `port`, `hot`, `proxy`, `historyApiFallback`  
11. **优化配置 (Optimization)**  
    - `splitChunks`, `minimizer`, `runtimeChunk`, `concatenateModules`  
12. **外部扩展 (Externals)**  
    - 排除第三方库（如 CDN 引入的 jQuery）  

---

#### **三、常用 Loader**
13. **JavaScript 处理**  
    - `babel-loader`（转译 ES6+）  
    - `ts-loader`（编译 TypeScript）  
14. **样式处理**  
    - `css-loader`（解析 CSS 依赖）  
    - `style-loader`（注入 CSS 到 DOM）  
    - `sass-loader`/`less-loader`（编译 Sass/Less）  
    - `postcss-loader`（Autoprefixer 等）  
15. **资源处理**  
    - `file-loader`（处理文件路径）  
    - `url-loader`（转 Base64 小文件）  
    - `raw-loader`（导入文件为字符串）  
16. **框架支持**  
    - `vue-loader`（Vue 单文件组件）  
    - `svg-react-loader`（SVG 转 React 组件）  

---

#### **四、常用插件**
17. **HTML 管理**  
    - `HtmlWebpackPlugin`（生成 HTML 文件）  
18. **资源清理**  
    - `CleanWebpackPlugin`（清理构建目录）  
19. **CSS 优化**  
    - `MiniCssExtractPlugin`（提取 CSS 为独立文件）  
    - `OptimizeCSSAssetsPlugin`（压缩 CSS）  
20. **JS 压缩**  
    - `TerserWebpackPlugin`（压缩 JS）  
21. **热更新**  
    - `HotModuleReplacementPlugin`（模块热替换）  
22. **环境变量**  
    - `DefinePlugin`（注入全局常量）  
23. **文件复制**  
    - `CopyWebpackPlugin`（复制静态文件）  
24. **包分析**  
    - `BundleAnalyzerPlugin`（可视化分析包体积）  

---

#### **五、优化策略**
25. **代码分割 (Code Splitting)**  
    - 入口分割、动态导入 (`import()`)、`splitChunks`  
26. **Tree Shaking**  
    - 移除未引用代码（需 ES Module 语法）  
27. **Scope Hoisting**  
    - 合并模块作用域，减少闭包  
28. **缓存优化**  
    - `[hash]`/`[chunkhash]`/`[contenthash]`（文件名哈希）  
    - `cache-loader`（缓存 Loader 结果）  
29. **懒加载 (Lazy Loading)**  
    - 动态 `import()` 按需加载模块  
30. **DLL 预编译**  
    - `DllPlugin` + `DllReferencePlugin` 提升构建速度  
31. **CDN 加速**  
    - 配置 `publicPath` 指向 CDN 地址  

---

#### **六、开发环境**
32. **Source Map**  
    - `devtool` 配置（`eval`、`source-map`、`cheap-module-source-map` 等）  
33. **模块热替换 (HMR)**  
    - 实时更新模块不刷新页面  
34. **代理配置 (Proxy)**  
    - 解决跨域问题  
35. **ESLint 集成**  
    - `eslint-webpack-plugin` 实时校验代码  

---

#### **七、生产环境**
36. **代码压缩**  
    - JS（Terser）、CSS（CSSNano）、HTML（HtmlWebpackPlugin 压缩）  
37. **资源指纹**  
    - 文件名添加哈希（缓存控制）  
38. **环境变量注入**  
    - 区分开发/生产环境配置  
39. **Bundle 分析**  
    - 使用 `webpack-bundle-analyzer` 优化体积  

---

#### **八、高级特性**
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

#### **九、工具集成**
45. **Babel**  
    - `@babel/core`、`@babel/preset-env`、`babel.config.js`  
46. **TypeScript**  
    - `tsconfig.json`、`ts-loader`/`babel-loader`  
47. **ESLint/Prettier**  
    - 代码规范与格式化  
48. **框架集成**  
    - React（`@babel/preset-react`）、Vue（`vue-loader`）  

---

#### **十、其他**
49. **Webpack CLI**  
    - `webpack`、`webpack-dev-server`、`--config` 指定配置文件  
50. **多配置导出**  
    - 配置文件导出数组或函数（多环境配置）  
51. **Shimming**  
    - `ProvidePlugin` 自动加载模块（如全局 jQuery）  
52. **性能分析**  
    - `speed-measure-webpack-plugin` 测量构建耗时  

---

### **总结**  
此清单涵盖 Webpack 核心配置、Loader/插件、优化策略、开发/生产环境实践及高级特性，适用于快速查阅和系统性学习。