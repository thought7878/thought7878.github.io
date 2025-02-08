在 Webpack 配置中使用 `externals` 可以让你*排除某些模块的打包*，从而在运行时从外部环境中获取这些模块。这在以下几个场景中特别有用：

1. **避免重复打包**：如果你的应用已经通过 CDN 加载了一些库（如 jQuery、React 等），可以使用 `externals` *避免在打包时再次包含这些库*。
2. **减小打包体积**：通过*排除一些大型库的打包*，可以显著减小最终生成的 bundle 大小。
3. **共享模块**：在多个应用之间共享某些模块，避免每个应用都单独打包这些模块。

### 基本用法

`externals` 配置项可以是一个对象、数组或函数。以下是一些常见的用法示例：

#### 1. 对象形式

```javascript
// webpack.config.js
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  externals: {
    react: 'React',
    'react-dom': 'ReactDOM',
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，`react` 和 `react-dom` *不会被打包进最终的 bundle 中*，而是期望在运行时从全局环境中获取这些模块。例如，你*可以在 HTML 文件中通过 CDN 引入这些库：*

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>App</title>
  // CDN 引入这些库
  <script src="https://unpkg.com/react@17/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
</head>
<body>
  <div id="root"></div>
  <script src="./dist/bundle.js"></script>
</body>
</html>
```

#### 2. 数组形式

如果你想在一个模块中排除多个依赖，可以使用数组形式：

```javascript
// webpack.config.js
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  // 
  externals: [
    {
      lodash: {
        commonjs: 'lodash',
        commonjs2: 'lodash',
        amd: 'lodash',
        root: '_' // 表示全局变量 _
      }
    },
    {
      moment: 'moment'
    }
  ],
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，`lodash` 和 `moment` *都不会被打包进最终的 bundle 中*。

#### 3. 函数形式

如果你需要更复杂的逻辑来决定哪些模块应该被排除，可以使用函数形式：

```javascript
// webpack.config.js
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  externals: (context, request, callback) => {
    if (/^my-library-/.test(request)) {
      return callback(null, 'commonjs ' + request);
    }
    callback();
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，*所有以 `my-library-` 开头的模块都会被排除*，并且期望在运行时从 CommonJS 环境中获取这些模块。

### 高级用法

#### 1. 多环境配置

如果你的应用需要在不同的环境中使用不同的外部库，可以使用环境变量来动态配置 `externals`：

```javascript
// webpack.config.js
const isProduction = process.env.NODE_ENV === 'production';

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  // 只有在生产环境下才会排除
  externals: isProduction ? {
    react: 'React',
    'react-dom': 'ReactDOM',
  } : {},
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

在这个例子中，*只有在生产环境下才会排除 `react` 和 `react-dom`。*

#### 2. 使用 `webpack-externals-plugin`

*如果你需要更复杂的外部库管理*，可以考虑使用 `webpack-externals-plugin`。这个插件提供了更多的灵活性和功能：

```bash
npm install --save-dev webpack-externals-plugin
```

```javascript
// webpack.config.js
const path = require('path');
constExternalsPlugin = require('webpack-externals-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  plugins: [
    newExternalsPlugin('amd', [
      {
        name: 'react',
        var: 'React',
        amd: 'react',
        commonjs: 'react',
        commonjs2: 'react',
      },
      {
        name: 'react-dom',
        var: 'ReactDOM',
        amd: 'react-dom',
        commonjs: 'react-dom',
        commonjs2: 'react-dom',
      },
    ]),
  ],
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

### 总结

通过合理使用 `externals` 配置，可以显著减少打包体积，提高应用的加载性能。以上示例展示了 `externals` 的基本用法和一些高级用法，希望对你有所帮助。