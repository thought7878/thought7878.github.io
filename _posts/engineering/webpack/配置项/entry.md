在 Webpack 配置中，`entry` 选项用于指定应用的入口点。Webpack *从这些入口点开始解析模块依赖关系*，然后将所有依赖的模块打包成一个或多个输出文件。`entry` 选项可以是一个字符串、数组或对象，具体取决于你的项目需求。

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

如果你的应用有多个入口点，可以使用数组或对象来指定。

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