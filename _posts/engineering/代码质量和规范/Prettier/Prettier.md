Prettier 是一个流行的**代码格式化工具**，专注于通过统一的代码风格（如缩进、引号、分号等）提升代码可读性。它与 ESLint 的定位不同：**ESLint 用于检测代码错误和规范**，而 **Prettier 专注于代码格式化**。两者结合使用可以实现**代码质量和风格的双重统一**。

---

### **1. 核心特性**
- **支持语言**：JavaScript、TypeScript、CSS、HTML、Vue、React、JSON、Markdown 等。
- **零配置**：开箱即用，默认风格已标准化。
- **高度可配置**：通过配置文件自定义代码风格。
- **插件生态**：支持扩展（如 Tailwind CSS、GraphQL 等）。
- **编辑器集成**：VSCode、WebStorm、Sublime 等主流编辑器支持。

---

### **2. 安装与配置**
#### **基础安装**
```bash
# 本地安装（推荐）
npm install --save-dev prettier

# 全局安装（不推荐）
npm install -g prettier
```

#### **创建配置文件**
在项目根目录创建 `.prettierrc` 文件（JSON 格式）：
```json
{
  "printWidth": 80,        // 每行最大字符数
  "tabWidth": 2,           // 缩进空格数
  "useTabs": false,        // 使用空格代替 Tab
  "semi": true,            // 末尾加分号
  "singleQuote": true,     // 使用单引号
  "trailingComma": "es5",  // 末尾逗号（es5: 仅对象/数组）
  "bracketSpacing": true,  // 对象字面量括号空格 { foo: bar }
  "arrowParens": "always", // 箭头函数参数始终带括号
  "endOfLine": "auto"      // 自动换行符（Windows/Linux/Mac 兼容）
}
```

#### **忽略文件**
创建 `.prettierignore` 文件：
```
/dist/
/node_modules/
*.min.js
```

---

### **3. 与 ESLint 协作**
#### **安装依赖**
```bash
npm install --save-dev eslint-config-prettier eslint-plugin-prettier prettier
```

#### **修改 ESLint 配置**
在 `.eslintrc.js` 中添加 Prettier 支持（确保 `prettier` 放在 `extends` 最后）：
```js
module.exports = {
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier" // 关闭与 Prettier 冲突的 ESLint 规则
  ],
  rules: {
    // 可选：覆盖 Prettier 的默认规则
  }
};
```

---

### **4. 编辑器集成**
#### **VSCode 配置**
1. 安装插件：[Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=Prettier.prettier-vscode)
2. 设置保存时自动格式化：
   ```json
   // VSCode settings.json
   "editor.defaultFormatter": "esbenp.prettier-vscode",
   "editor.formatOnSave": true,
   "[javascript]": {
     "editor.formatOnSave": true
   },
   "[typescript]": {
     "editor.formatOnSave": true
   },
   "[vue]": {
     "editor.formatOnSave": true
   }
   ```

#### **WebStorm 配置**
1. 安装插件：`Settings > Plugins > Prettier`
2. 设置默认格式化工具：`Settings > Languages & Frameworks > JavaScript > Prettier`

---

### **5. 命令行使用**
#### **格式化所有文件**
```bash
npx prettier --write .
```

#### **检查格式问题**
```bash
npx prettier --check .
```

#### **格式化指定文件**
```bash
npx prettier --write src/**/*.js
```

---

### **6. 常见问题解决**
#### **问题 1：保存时未自动格式化**
- **原因**：编辑器未正确设置默认格式化工具。
- **解决**：检查 VSCode 的 `settings.json` 或 WebStorm 的格式化配置。

#### **问题 2：Prettier 与 ESLint 规则冲突**
- **原因**：未正确禁用冲突规则。
- **解决**：确保 `eslint-config-prettier` 已安装且 `extends` 中包含 `"prettier"`。

#### **问题 3：格式化后代码逻辑错误**
- **原因**：Prettier 仅处理格式，不保证逻辑安全。
- **解决**：格式化前确保代码逻辑正确，或使用 `--no-semi` 等参数调整规则。

#### **问题 4：Vue/JSX 文件格式化异常**
- **解决**：安装额外插件：
  ```bash
  npm install --save-dev @prettier/plugin-xml prettier-plugin-tailwindcss
  ```

---

### **7. 最佳实践**
- **团队协作**：将 `.prettierrc` 和 `.prettierignore` 提交到 Git，确保团队风格统一。
- **CI 检查**：在 CI 流程中添加格式化检查：
  ```bash
  npx prettier --check .
  ```
- **Git Hook**：使用 `husky` 在提交前自动格式化：
  ```bash
  npx husky add .husky/pre-commit "npx prettier --write ."
  ```

---

### **8. 参考文档**
- [Prettier 官方文档](https://prettier.io/docs/en/)
- [与 ESLint 集成指南](https://prettier.io/docs/en/integrating-with-linters.html)
- [Tailwind CSS 插件](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)

通过合理配置 Prettier，可以大幅减少代码风格争议，提升开发效率。如果遇到具体问题，建议结合官方文档和社区资源进一步排查。