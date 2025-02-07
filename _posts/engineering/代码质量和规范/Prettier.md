### Prettier 是什么

**Prettier** 是一个强大的*代码格式化工具*，秉持“固执己见”（Opinionated）的理念，旨在*为多种编程语言*提供统一、标准化的*代码格式化方案*。它可以自动分析代码的结构，*按照预设或自定义的规则对代码进行格式化*，调整代码的*缩进、换行、空格、引号*使用等方面，*让代码具有一致的外观和风格*。Prettier 支持众多前端和后端编程语言，如 JavaScript、TypeScript、CSS、SCSS、Less、HTML、JSON、Markdown 等。

### 为什么使用 Prettier

#### 提升代码可读性

- **统一风格**：在团队开发中，不同开发者可能有*不同的代码编写习惯*，这会导致*代码风格参差不齐*，*增加代码阅读和理解的难度*。Prettier 可以*强制执行统一的代码风格*，无论代码由谁编写，最终呈现出的格式都是一致的，使代码更易于阅读和维护。
- **优化布局**：它会对代码的布局进行优化，例如*合理的缩进、换行和空格*使用，使代码结构更加清晰。比如在处理嵌套的代码块时，能清晰地展示层级关系，让开发者更容易把握代码的逻辑结构。

#### 提高开发效率

- **自动化操作**：Prettier 能够*自动完成代码格式化*的工作，开发者无需手动调整代码的格式，*节省了大量时间和精力*，可以将更多的注意力放在代码的功能实现和逻辑设计上。
- **避免风格争议**：在代码审查过程中，常常会因为代码风格问题产生争论。使用 Prettier 可以消除这些不必要的争议，审查人员可以将更多的精力放在代码的逻辑正确性和功能完整性上，提高代码审查的效率。

#### 减少人为错误

- **一致性保障**：手动格式化代码容易出现疏漏，导致代码风格不一致。Prettier 可以确保每次格式化的结果都是一致的，避免因人为疏忽而引入的格式错误，保证代码质量的稳定性。

### 如何使用 Prettier

#### 安装

Prettier 基于 Node.js 和 npm，你可以将它*作为开发依赖*安装到项目中。在项目根目录下打开终端，执行以下命令：

```bash
npm install prettier --save-dev
```

#### 配置文件

虽然 Prettier 有一套默认的格式化规则，零配置即可使用，但你也可以*根据项目需求创建自定义的配置文件*。常见的配置文件格式有 `.prettierrc.js`、`.prettierrc.json` 等。以下是一个简单的 `.prettierrc.js` 配置文件示例：

```javascript
module.exports = {
  printWidth: 80, // 每行代码的最大字符数，超过则换行
  tabWidth: 4, // 缩进的空格数
  useTabs: false, // 是否使用制表符（Tab）进行缩进
  semi: true, // 是否在语句末尾添加分号
  singleQuote: true, // 是否使用单引号
};
```

#### 格式化代码

- **格式化单个文件**：在项目根目录下，使用以下命令对指定的文件进行格式化：

```bash
npx prettier --write your-file.js
```

`--write` 选项表示直接修改源文件。

- **格式化整个目录**：如果要格式化某个目录下的所有文件，可以使用通配符。例如，格式化 `src` 目录下的所有 JavaScript 文件：

```bash
npx prettier --write src/**/*.js
```

#### 与开发工具集成

- **VS Code 集成**：
  - 打开 VS Code，在扩展市场中搜索并安装 “Prettier - Code formatter” 插件。
  - 安装完成后，你可以在 VS Code 的设置中配置默认的代码格式化工具为 Prettier。在 `settings.json` 中添加以下配置：

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true // 保存文件时自动格式化
}
```

- **与 Webpack 集成**：如果项目使用 Webpack 作为构建工具，可以使用 `prettier-webpack-plugin` 插件将 Prettier 集成到构建流程中。首先安装插件：

```bash
npm install prettier-webpack-plugin --save-dev
```

然后在 Webpack 配置文件中添加以下配置：

```javascript
const PrettierPlugin = require("prettier-webpack-plugin");

module.exports = {
  // ...其他配置
  plugins: [new PrettierPlugin()],
};
```
