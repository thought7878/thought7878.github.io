ESLint 是一个用于 JavaScript/TypeScript 的静态代码检查工具，旨在帮助开发者遵循最佳实践并保持代码风格一致。以下是关于 ESLint 的详细指南：

---

### 1. 基本概念
- **作用**：检测代码中的潜在错误、强制代码规范（如缩进、变量命名）、提升代码质量。
- **优势**：高度可配置，支持插件扩展（如 React、Vue、TypeScript），可与 Prettier 协同工作。

---

### 2. 安装与配置
#### 基础安装
```bash
# 本地安装（推荐）
npm install eslint --save-dev

# 全局安装（不推荐，版本冲突风险高）
npm install -g eslint
```

#### 初始化配置
```bash
npx eslint --init
```
选择配置后会生成 `.eslintrc.js` 或 `.eslintrc.json` 文件，示例如下：
```js
// .eslintrc.js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended', // 基础推荐规则
    'plugin:react/recommended', // React 插件
    'plugin:@typescript-eslint/recommended', // TypeScript 支持
  ],
  parserOptions: {
    ecmaVersion: 2021,
    sourceType: 'module',
    ecmaFeatures: {
      jsx: true, // React JSX 支持
    },
  },
  plugins: ['react', '@typescript-eslint'],
  rules: {
    // 自定义规则覆盖
    'no-console': ['warn'], // 将 console.warn/error 标记为警告
    'indent': ['error', 2], // 缩进为 2 个空格
  },
};
```

---

### 3. 常用规则配置

| 规则名              | 作用示例                     | 可选值（error/warn/off） |
|---------------------|------------------------------|--------------------------|
| `no-console`        | 禁止使用 `console`           | `'warn'`                 |
| `no-debugger`       | 禁止使用 `debugger`          | `'error'`                |
| `prefer-const`      | 推荐使用 `const` 而非 `let`  | `'error'`                |
| `react/prop-types`  | 强制 React 组件使用 prop types | `'off'`（若使用 TypeScript 可关闭） |

---

### 4. 编辑器集成
#### VSCode 配置
1. 安装插件：[ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
2. 启用保存自动修复：
   ```json
   // VSCode settings.json
   "eslint.enable": true,
   "eslint.run": "onSave",
   "editor.codeActionsOnSave": {
     "source.fixAll.eslint": true
   }
   ```

---

### 5. 忽略检查
#### 忽略文件
创建 `.eslintignore` 文件：
```
/dist/
/node_modules/
*.min.js
```

配置文件
```json
// 忽略的文件模式（不检查这些文件）
ignorePatterns: ["**/*.css.d.ts", "dist", "build"],
```
#### 单行/代码块忽略
```js
// 单行忽略
// eslint-disable-next-line no-console
console.log('Hello');

/* eslint-disable no-console */
console.log('A');
console.log('B');
/* eslint-enable no-console */
```

---

### 6. 与 Prettier 协作
```bash
npm install eslint-config-prettier eslint-plugin-prettier prettier --save-dev
```
在 `.eslintrc.js` 中添加：
```js
extends: [
  'eslint:recommended',
  'plugin:react/recommended',
  'plugin:@typescript-eslint/recommended',
  'prettier' // 确保放在最后以覆盖冲突规则
]
```

---

### 7. 常见问题解决
#### 问题 1：规则冲突
- **现象**：ESLint 与 Prettier 的格式化规则不一致。
- **解决**：使用 `eslint-config-prettier` 禁用冲突规则。

#### 问题 2：React 报错 `'React' must be in scope when using JSX`
- **解决**：确保 `react` 插件已添加，且 `env` 中包含 `browser`：
  ```js
  env: {
    browser: true,
    es2021: true,
  },
  plugins: ['react']
  ```

#### 问题 3：TypeScript 类型错误未被检测
- **解决**：安装 `@typescript-eslint/eslint-plugin` 和 `@typescript-eslint/parser`，并在配置中启用：
  ```js
  parser: '@typescript-eslint/parser',
  extends: ['plugin:@typescript-eslint/recommended']
  ```

---

### 8. 最佳实践
- **团队协作**：统一配置文件并提交到 Git，避免本地差异。
- **CI 检查**：在 CI 流程中添加 `eslint` 命令，防止低质量代码合并。
- **自动修复**：运行 `npx eslint . --fix` 自动修正可修复的错误。

---

### 9. 参考文档
- [ESLint 官方文档](https://eslint.org/docs/latest/)
- [React 插件文档](https://github.com/jsx-eslint/eslint-plugin-react)
- [TypeScript 插件文档](https://github.com/typescript-eslint/typescript-eslint)

通过合理配置 ESLint，可以显著提升代码质量和团队协作效率。如果遇到具体问题，建议结合官方文档和社区资源进一步排查。