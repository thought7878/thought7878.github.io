该项目的 ESLint 和 Prettier 是通过以下方式搭建和配置的：

---

## 1. 安装依赖

在 package.json 的 `devDependencies` 中定义了 ESLint、Prettier 及其插件的版本：

### ESLint 相关依赖
```json
"eslint": "^8.57.1",
"@typescript-eslint/eslint-plugin": "^8.29.1",
"@typescript-eslint/parser": "^8.29.1",
"eslint-config-prettier": "^10.1.2",
"eslint-import-resolver-typescript": "^3.10.0",
"eslint-plugin-import": "^2.31.0",
"eslint-plugin-jest": "^28.11.0",
"eslint-plugin-prettier": "^5.2.6",
"eslint-plugin-react": "^7.37.5",
"eslint-plugin-react-hooks": "^5.2.0",
"eslint-plugin-require-extensions": "^0.1.3",
"eslint-plugin-testing-library": "^7.1.1"
```

### Prettier 相关依赖
```json
"prettier": "^3.5.3",
"@trivago/prettier-plugin-sort-imports": "^5.2.2",
"prettier-plugin-jsdoc": "^1.3.2"
```

---

## 2. 配置 ESLint（`.eslintrc.js` 或 `.eslintrc.json`）

虽然未提供具体文件，但根据依赖可以推测典型的 ESLint 配置如下：

```js
module.exports = {
  root: true,
  env: {
    browser: true,
    es2021: true,
    jest: true
  },
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:jest/recommended",
    "plugin:testing-library/react",
    "prettier"
  ],
  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: "latest",
    sourceType: "module"
  },
  plugins: ["@typescript-eslint", "react", "jest", "testing-library"],
  rules: {
    // 自定义规则覆盖默认配置
  },
  settings: {
    react: {
      version: "detect"
    }
  }
};
```

---

## 3. 配置 Prettier（`.prettierrc`）

项目中提供了完整的 `.prettierrc` 配置文件，内容如下：

```json
{
  "$schema": "https://json.schemastore.org/prettierrc",
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": false,
  "trailingComma": "none",
  "plugins": ["@trivago/prettier-plugin-sort-imports", "prettier-plugin-jsdoc"],
  "importOrder": [
    "^react$",
    "<THIRD_PARTY_MODULES>",
    "^@/(.*)$",
    "^../",
    "^[./]"
  ],
  "importOrderSeparation": true
}
```

说明：
- 使用 **双空格缩进**。
- 不使用 Tab 缩进。
- 每行末尾添加分号。
- 使用双引号。
- 不添加尾随逗号。
- 使用 `@trivago/prettier-plugin-sort-imports` 插件自动排序导入语句，并按指定顺序排列。
- 使用 `prettier-plugin-jsdoc` 支持 JSDoc 格式化。

---

## 4. 脚本命令（`package.json`）

在 `scripts` 字段中定义了 ESLint 执行命令：

```json
"lint": "eslint ."
```

运行方式为：
```bash
pnpm run lint
```

或带 watch 模式的：
```bash
pnpm run lint --watch
```

> 注：部分子项目如 `examples-app` 有更详细的 ESLint 命令，例如：
```json
"lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0"
```

---

## 5. 与构建工具集成

### TypeScript + ESLint
- 使用 `@typescript-eslint/parser` 作为解析器。
- 使用 `@typescript-eslint/eslint-plugin` 提供 TypeScript 特定的 ESLint 规则。

### React 支持
- 使用 `eslint-plugin-react` 来支持 JSX 和 React 相关规则。
- 使用 `eslint-plugin-react-hooks` 来检查 React Hook 使用规范。

### 测试库支持
- 使用 `eslint-plugin-testing-library` 对测试代码进行风格规范。

---

## 6. 与 Prettier 集成

通过以下方式确保 ESLint 与 Prettier 协作：
- 安装 `eslint-config-prettier` 禁用与 Prettier 冲突的规则。
- 安装 `eslint-plugin-prettier` 将 Prettier 作为 ESLint 的规则执行。

---

## 7. IDE 支持建议

建议在 VSCode 中安装以下扩展以获得最佳开发体验：
- **ESLint**
- **Prettier - Code formatter**

并在设置中启用保存时格式化：
```json
{
  "editor.formatOnSave": true,
  "eslint.format.enable": true,
  "prettier.requireConfig": true
}
```

---

## ✅ 总结

| 工具 | 配置方式 |
|------|-----------|
| **ESLint** | 使用 `.eslintrc.js/json` 配置，结合多个插件（React、TS、Jest、Testing Library） |
| **Prettier** | 使用 `.prettierrc` 配置，结合插件自动排序导入语句和格式化 JS Doc |
| **脚本命令** | `npm run lint` 或 `pnpm run lint` |
| **IDE 集成** | 推荐配合 VSCode 插件实现保存自动格式化 |

整个 ESLint + Prettier 的搭建是现代前端项目标准实践，能够保证代码质量和一致性。