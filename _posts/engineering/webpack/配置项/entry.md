
`入口（Entry）`是 Webpack 开始**构建的起点**。*Webpack 会从入口文件开始，递归地分析入口文件及其依赖的所有模块，然后将这些模块打包成一个或多个文件*。简单来说，入口就像是 Webpack 进入项目代码世界的 *“大门”*，**它告诉 Webpack 从哪个文件开始着手处理整个项目的资源**。
`entry` 选项可以是一个字符串、数组或对象，具体取决于你的项目需求。

### 入口的作用

- **确定打包范围**：入口文件决定了 Webpack 要处理哪些模块。Webpack 会从入口文件开始，递归地查找所有依赖的模块，将它们纳入打包范围。
- **代码分割**：在多入口配置中，可以将不同的功能模块或页面拆分成独立的入口，实现代码分割。这样可以避免将所有代码打包成一个大文件，提高页面的加载性能。例如，在一个多页面应用中，不同页面的代码可以分别打包，用户访问某个页面时只需要加载该页面所需的代码。
- **按需加载**：结合动态入口和代码分割技术，可以实现按需加载功能。例如，在单页面应用中，当用户访问某个特定路由时，再动态加载该路由对应的模块，而不是在页面加载时就加载所有模块。

### 基本用法

#### 1. 单个入口点

最简单的用法是*使用一个字符串来指定单个入口点*。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js', // 单个入口点
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，Webpack 从 `./src/index.js` 开始解析模块依赖关系，并生成一个名为 `bundle.js` 的输出文件。

#### 2. 多个入口点
多入口适用于复杂的项目，有多个独立的起点文件，例如*一个项目包含多个页面*，每个页面都有自己独立的 JavaScript 逻辑。
如果你的应用有多个入口点，可以使用*数组或对象*来指定。

##### 使用数组

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: ['./src/index.js', './src/another-entry.js'], // 多个入口点
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，Webpack 会将 `./src/index.js` 和 `./src/another-entry.js` 合并成一个输出文件 `bundle.js`。

##### 使用对象

使用对象可以*为每个入口点指定一个唯一的名称，生成多个输出文件*。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: {
    main: './src/index.js',
    another: './src/another-entry.js',
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js', // 使用 [name] 占位符生成多个文件
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，Webpack 会生成两个输出文件：`main.js` 和 `another.js`。

### 高级用法

#### 1. 动态入口点
动态入口允许你*根据某些条件或逻辑*来动态确定入口文件。
你可以*使用函数来动态生成入口点，这对于复杂的构建流程非常有用*。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: (webpackConfigEnv) => {
    const entries = {
      main: './src/index.js',
    };

    if (webpackConfigEnv && webpackConfigEnv.production) {
      entries.another = './src/another-entry.js';
    }

    return entries;
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，`entry` 是一个函数，根据 `webpackConfigEnv` 的值动态生成入口点。

#### 2. 多页应用（Multi-Page Application）

对于多页应用，每个页面可能有自己的入口点和输出文件。可以使用对象形式的 `entry` 来管理这些入口点。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: {
    page1: './src/page1/index.js',
    page2: './src/page2/index.js',
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，`page1` 和 `page2` 分别对应两个页面的入口点，生成 `page1.js` 和 `page2.js` 两个输出文件。

### 总结

通过合理配置 `entry` 选项，可以灵活地管理应用的入口点，无论是单个入口点、多个入口点还是动态生成的入口点。