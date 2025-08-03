在 Webpack 配置中，`infrastructureLogging` 选项用于*配置基础设施日志记录*，这些日志记录提供了*有关 Webpack 内部操作的详细信息*，**有助于调试和优化构建过程**。

以下是如何在 Webpack 配置中使用 `infrastructureLogging` 选项的详细说明和示例：

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
  infrastructureLogging: {
    // 配置基础设施日志记录
  },
  module: {
    rules: [
      // ...其他规则
    ],
  },
};
```

### 常见的 `infrastructureLogging` 配置选项

#### 1. `level`

- **类型**：`string`
- **描述**：设置日志记录级别，可以是 `'trace'`、`'debug'`、`'info'`、`'warn'`、`'error'` 或 `'silent'`。
- **示例**：
  ```javascript
  level: 'info', // 记录信息级别的日志
  level: 'debug', // 记录调试级别的日志
  level: 'silent', // 关闭所有日志
  ```

#### 2. `debug`

- **类型**：`string[]`
- **描述**：指定要记录调试信息的模块或插件。
- **示例**：
  ```javascript
  debug: ['webpack'], // 记录 webpack 的调试信息
  debug: ['webpack', 'webpack-dev-server'], // 记录 webpack 和 webpack-dev-server 的调试信息
  ```

#### 3. `quiet`

- **类型**：`boolean`
- **描述**：是否抑制所有日志输出。
- **示例**：
  ```javascript
  quiet: true, // 抑制所有日志输出
  ```

### 常见配置示例

#### 1. 基本配置

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  infrastructureLogging: {
    level: 'info', // 记录信息级别的日志
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: 'babel-loader',
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
};
```

#### 2. 高级配置

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  infrastructureLogging: {
    level: 'debug', // 记录调试级别的日志
    debug: ['webpack', 'webpack-dev-server'], // 记录特定模块的调试信息
    quiet: false, // 不抑制所有日志输出
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: 'babel-loader',
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
};
```

### 动态配置 `infrastructureLogging`

你可以根据环境变量或其他条件动态配置 `infrastructureLogging`。

```javascript
// webpack.config.js
const path = require('path');
const isDevelopment = process.env.NODE_ENV !== 'production';

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  infrastructureLogging: isDevelopment ? {
    level: 'debug', // 记录调试级别的日志
    debug: ['webpack', 'webpack-dev-server'], // 记录特定模块的调试信息
    quiet: false, // 不抑制所有日志输出
  } : {
    level: 'info', // 记录信息级别的日志
    quiet: true, // 抑制所有日志输出
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: 'babel-loader',
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
};
```

### 总结

通过合理配置 `infrastructureLogging` 选项，可以获取有关 Webpack 内部操作的详细日志信息，帮助你更好地调试和优化构建过程。