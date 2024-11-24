在 Webpack 配置中，`module` 和 `rules` 选项用于*定义如何处理不同类型的模块*（如 JavaScript、CSS、图片等）。通过配置 `module` 和 `rules`，你可以指定不同的加载器（loaders）来处理特定类型的文件，从而*实现文件的转换、压缩等操作*。

### 基本配置

#### 1. 基本结构

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      // 配置规则
    ],
  },
};
```

### 详细配置选项

#### 1. `test`

- **类型**：`string` | `RegExp`
- **描述**：用于匹配文件路径的正则表达式或字符串。
- **示例**：
  ```javascript
  test: /\.js$/, // 匹配 .js 文件
  test: /\.css$/, // 匹配 .css 文件
  ```

#### 2. `include` 和 `exclude`

- **类型**：`string` | `RegExp` | `array`
- **描述**：用于指定哪些文件路径需要或不需要被处理。
- **示例**：
  ```javascript
  include: path.resolve(__dirname, 'src'), // 只处理 src 目录下的文件
  exclude: /node_modules/, // 排除 node_modules 目录下的文件
  ```

#### 3. `use`

- **类型**：`string` | `array` | `object`
- **描述**：指定一个或多个加载器来处理匹配的文件。
- **示例**：
  ```javascript
  use: 'babel-loader', // 使用单个加载器
  use: ['style-loader', 'css-loader'], // 使用多个加载器
  use: {
    loader: 'babel-loader',
    options: {
      presets: ['@babel/preset-env'],
    },
  }, // 使用带有选项的加载器
  ```

#### 4. `oneOf`

- **类型**：`array`
- **描述**：指定一组规则，Webpack 会按照顺序尝试匹配这些规则，一旦匹配成功就不再继续匹配。
- **示例**：
  ```javascript
  oneOf: [
    {
      test: /\.js$/,
      use: 'babel-loader',
    },
    {
      test: /\.css$/,
      use: ['style-loader', 'css-loader'],
    },
  ],
  ```

### 常见配置示例

#### 1. 处理 JavaScript 文件

使用 `babel-loader` *将 ES6+ 代码转换为兼容旧浏览器的代码*。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
    ],
  },
};
```

#### 2. 处理 CSS 文件

使用 `style-loader` 和 `css-loader` 将 CSS 文件嵌入到 JavaScript 中。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
};
```

#### 3. 处理图片文件

使用 `file-loader` 或 `url-loader` 处理图片文件，可以选择将图片内联为 Data URL 或者输出为单独的文件。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif|svg)$/,
        use: {
          loader: 'url-loader',
          options: {
            limit: 8192, // 小于 8KB 的图片将被内联为 Data URL
            name: '[name].[ext]', // 输出文件名
            outputPath: 'images/', // 输出目录
          },
        },
      },
    ],
  },
};
```

#### 4. 处理 TypeScript 文件

使用 `ts-loader` 或 `awesome-typescript-loader` 处理 TypeScript 文件。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js'],
  },
};
```

### 高级用法

#### 1. 使用 `oneOf` 优化性能

`oneOf` 可以*提高 Webpack 的性能，因为它会停止匹配第一个成功的规则，而不是继续尝试所有规则*。

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        oneOf: [
          {
            test: /\.js$/,
            exclude: /node_modules/,
            use: 'babel-loader',
          },
          {
            test: /\.css$/,
            use: ['style-loader', 'css-loader'],
          },
          {
            test: /\.(png|jpe?g|gif|svg)$/,
            use: {
              loader: 'url-loader',
              options: {
                limit: 8192,
                name: '[name].[ext]',
                outputPath: 'images/',
              },
            },
          },
          {
            test: /\.tsx?$/,
            use: 'ts-loader',
            exclude: /node_modules/,
          },
        ],
      },
    ],
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js'],
  },
};
```

### 总结

通过合理配置 `module` 和 `rules`，可以灵活地处理各种类型的文件，实现文件的转换、压缩等操作。