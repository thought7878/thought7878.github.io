Webpack 的依赖图（Dependency Graph）是其核心概念之一，在整个打包过程中起着至关重要的作用。以下从定义、构建过程、作用和表示形式几个方面详细介绍。

### 定义

**依赖图**是一个`有向无环图`（DAG，Directed Acyclic Graph），*它描述了项目中各个模块之间的依赖关系*。在这个图里，*每个节点*代表一个模块，*节点之间的有向边*代表模块之间的依赖关系，即一个模块引用了另一个模块。

### 构建过程

1. **确定入口点**：Webpack 从配置文件（通常是 `webpack.config.js`）中指定的入口文件开始。例如：

```javascript
// webpack.config.js
module.exports = {
  entry: "./src/index.js",
};
```

这里的 `./src/index.js` 就是入口文件，Webpack 会从这个文件开始构建依赖图。 

2. **解析模块**：Webpack 分析入口文件中的模块导入语句，像 ES6 的 `import` 或者 CommonJS 的 `require`。比如在 `index.js` 里有如下代码：

```javascript
// src/index.js
import utils from "./utils.js";
```

Webpack 会根据这个导入语句去查找 `./utils.js` 文件。 

3. **递归查找依赖**：找到 `./utils.js` 后，Webpack 会继续分析 `utils.js` 中的导入语句，重复上述解析模块的过程，递归地查找该模块依赖的其他模块，直到所有模块都被分析完毕。 

4. **构建图结构**：在分析模块的过程中，Webpack 会将每个模块作为一个节点，模块之间的依赖关系作为边，构建出整个依赖图。

### 作用

1. **打包依据**：依赖图是 Webpack 进行打包的基础。Webpack 根据依赖图确定哪些模块需要被打包，以及打包的顺序，确保所有模块及其依赖都被正确地包含在最终的打包文件中。
2. **代码分割**：依赖图有助于 Webpack 实现代码分割。通过分析模块之间的依赖关系，Webpack 可以找出哪些模块可以被分割成单独的文件，实现按需加载，提高应用的性能。例如，对于通过动态导入（`import()`）引入的模块，Webpack 会将其作为一个独立的代码块进行打包。
3. **模块管理**：依赖图让 Webpack 能够有效地管理模块。它可以识别重复的模块，避免重复打包，还可以对模块进行优化和处理，如使用 Loader 对不同类型的模块进行转换。

### 表示形式

虽然 **Webpack 内部构建的依赖图**是一个*复杂的数据结构*，但通常不会直接展示给开发者。不过，开发者可以*使用一些工具来可视化依赖图*，比如 `webpack-bundle-analyzer` 插件。安装并配置该插件后，运行打包命令，它会生成一个可视化的报告，展示各个模块在依赖图中的位置和所占的体积等信息，帮助开发者分析和优化项目。例如：

```javascript
// webpack.config.js
const BundleAnalyzerPlugin =
  require("webpack-bundle-analyzer").BundleAnalyzerPlugin;

module.exports = {
  // 其他配置...
  plugins: [new BundleAnalyzerPlugin()],
};
```

运行 `webpack` 命令后，会在浏览器中打开一个页面，显示项目的依赖图可视化结果。
