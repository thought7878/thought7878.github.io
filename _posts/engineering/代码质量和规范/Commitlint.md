### 1. 什么是 Commitlint

**Commitlint** 是一个用于**检查 Git 提交信息是否符合指定规范**的工具。在前端开发中，良好的提交信息有助于团队成员*更好地理解代码变更的意图、追踪问题和进行版本管理*。Commitlint 通过定义一系列规则，*对每次 Git 提交时输入的信息进行验证，确保提交信息遵循统一的规范*。

### 2. 为什么使用 Commitlint

- **统一提交风格**：在团队开发中，*不同成员的提交信息风格可能差异很大，这会导致提交历史混乱，难以阅读和理解*。使用 Commitlint 可以强制团队成员遵循统一的提交信息规范，*使提交历史更加清晰、规范*。
- **便于追踪和管理**：规范的提交信息能够准确描述代码变更的内容和目的，方便团队成员快速定位和理解每次提交所做的更改，提高代码审查、问题追踪和版本管理的效率。
- **自动化检查**：通过将 Commitlint 集成到开发流程中，可以在提交代码时自动检查提交信息的规范性，避免不规范的提交信息进入版本控制系统，减少人工审查的工作量。

### 3. 如何使用 Commitlint

#### 3.1 安装

在项目中安装 Commitlint 及其相关依赖。通常会使用 `@commitlint/cli` 作为命令行工具，同时还需要安装一个配置包，如 `@commitlint/config-conventional`，它提供了一套常用的提交信息规范。

```bash
npm install --save-dev @commitlint/{cli,config-conventional}
```

#### 3.2 配置

在项目根目录下创建 `commitlint.config.js` 文件，并配置使用的规则集。一般使用 `@commitlint/config-conventional` 作为基础规则集：

```javascript
module.exports = {
  extends: ["@commitlint/config-conventional"],
};
```

`@commitlint/config-conventional` 遵循的是 Conventional Commits 规范，该规范要求提交信息的格式为：参考 [[Conventional Commits 规范]]

```plaintext
<类型>[可选的作用域]: <描述>

[可选的正文]

[可选的脚注]
```

其中，“类型” 描述了提交的类型，常见的类型有 `feat`（新功能）、`fix`（修复 bug）、`docs`（文档更新）、`style`（代码样式调整）等；“作用域” 是可选的，用于指定提交影响的范围；“描述” 是对提交内容的简要说明。

#### 3.3 集成到 Husky

*为了在每次提交代码时自动检查提交信息*，需要将 Commitlint 集成到 Husky 的 `commit - msg` 钩子中。

首先，确保已经安装并配置了 Husky。

然后，使用以下命令添加 `commit - msg` 钩子：

```bash
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

此命令会在 `.husky` 文件夹下创建 `commit - msg` 文件，当执行 `git commit` 命令时，*Husky 会触发该钩子，调用 Commitlint 对提交信息进行检查*。如果提交信息不符合规范，提交操作将被阻止，并给出相应的错误提示。

### 4. 自定义规则

除了使用默认的规则集，还可以根据项目的具体需求自定义 Commitlint 规则。在 `commitlint.config.js` 文件中，可以通过 `rules` 字段来定义自定义规则。例如，限制提交信息的描述长度不超过 50 个字符：

```javascript
module.exports = {
  extends: ["@commitlint/config-conventional"],
  rules: {
    "subject-max-length": [2, "always", 50],
  },
};
```

上述规则中，`subject-max-length` 表示检查提交信息描述的最大长度，`[2, 'always', 50]` 表示该规则为错误级别（2），始终应用（`always`），最大长度为 50 个字符。

### 5. 注意事项

- **规则培训**：在团队中引入 Commitlint 后，需要对团队成员进行规则培训，确保大家理解并遵循提交信息规范。可以提供示例和文档，帮助成员熟悉规范的使用。
- **规则调整**：根据项目的发展和实际需求，可能需要对 Commitlint 的规则进行调整。在调整规则时，要充分考虑对团队开发流程的影响，并及时通知团队成员。
