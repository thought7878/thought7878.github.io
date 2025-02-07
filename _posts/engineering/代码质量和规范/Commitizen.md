### 什么是 Commitizen

**Commitizen** 是一个用于**生成符合 Conventional Commits 规范的 Git 提交信息**的工具。在前端开发中，遵循 Conventional Commits 规范可以使提交信息更加清晰、统一，便于团队协作、代码审查和版本管理。然而，*手动编写*符合规范的提交信息可能*比较繁琐且容易出错*，Commitizen 则*提供了一种交互式的方式来帮助开发者生成规范的提交信息*。

### 为什么使用 Commitizen

- **提高提交信息质量**：*通过交互式的提示和引导*，确保提交信息符合 Conventional Commits 规范，避免因手动编写导致的格式错误或信息不完整。
- **提升开发效率**：*减少开发者思考提交信息格式的时间*，按照工具的提示逐步填写，快速生成规范的提交信息。
- **促进团队协作**：统一团队成员的提交信息风格，使项目的提交历史更加清晰易读，方便团队成员理解和追踪代码变更。

### 如何使用 Commitizen

#### 1. 安装

在项目中全局或局部安装 Commitizen：

```bash
# 全局安装
npm install -g commitizen
# 局部安装到项目中
npm install --save-dev commitizen
```

#### 2. 初始化适配器

Commitizen 需要一个适配器来定义提交信息的格式和交互方式，常用的适配器是 `cz - conventional - changelog`，它遵循 Conventional Commits 规范。在项目中安装该适配器：

```bash
npm install --save-dev cz-conventional-changelog
```

然后在 `package.json` 中添加以下配置：

```json
{
  "config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  }
}
```

上述配置指定了使用 `cz - conventional - changelog` 作为 Commitizen 的适配器。

#### 3. 使用 Commitizen 进行提交

安装和配置完成后，**不再使用传统的 `git commit` 命令，而是使用 `git cz` 命令来进行提交**：

```bash
git cz
```

*执行该命令后，Commitizen 会以交互式的方式引导你填写提交信息*，具体步骤如下：

- **选择提交类型**：从一系列预设的提交类型（如 `feat`、`fix`、`docs` 等）中选择本次提交的类型。
- **输入作用域（可选）**：指定提交影响的范围，如某个模块、功能或文件的名称。
- **输入简短描述**：用简短的语句描述本次提交的主要内容，使用祈使句、现在时，首字母小写，结尾不要加标点。
- **输入详细描述（可选）**：对提交内容进行更详细的说明，解释提交的动机、与之前实现的对比等。
- **关联相关的 issue（可选）**：如果本次提交与某个 issue 相关，可以输入 issue 编号。

按照提示依次完成输入后，Commitizen 会自动生成符合 Conventional Commits 规范的提交信息并完成提交操作。

### 自定义适配器

除了使用 `cz - conventional - changelog` 适配器，还可以根据项目的具体需求自定义适配器。自定义适配器可以定义独特的提交类型、交互流程和提交信息格式。要创建自定义适配器，可以参考 Commitizen 的官方文档，按照其提供的规范进行开发。

### 与其他工具集成

- **与 Husky 和 Commitlint 集成**：可以将 Commitizen 与 Husky 和 Commitlint 结合使用，进一步确保提交信息的规范性。在 Husky 的 `commit - msg` 钩子中使用 Commitlint 检查提交信息是否符合规范，如果不符合则阻止提交。这样可以在提交代码时进行双重验证，提高提交信息的质量。
