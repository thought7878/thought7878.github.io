### 1. 什么是 lint-staged

`lint-staged` 是一个*在 Git 暂存区文件上运行 linters（代码检查工具，如 ESLint、Stylelint）的工具*。在前端开发中，当我们修改了大量代码并准备提交时，通常*只希望对本次提交所涉及的文件进行代码检查，而不是对整个项目的所有文件进行检查，因为检查整个项目可能会非常耗时*。`lint-staged` 就可以帮助我们实现这一目标，*它会自动识别 Git 暂存区中的文件，并只对这些文件运行指定的 lint 命令*。

### 2. 为什么要使用 lint-staged

- **提高效率**：**只对暂存区的文件进行代码检查**，避免了对整个项目进行不必要的检查，节省了时间，尤其是在项目规模较大时，这种效率提升更为明显。
- **减少误报**：只检查本次提交相关的文件，能够更精准地发现和修复本次修改引入的问题，减少因检查范围过大而产生的误报。
- **确保提交质量**：在代码提交前对暂存区文件进行检查，可以保证每次提交的代码都符合代码规范和质量要求，避免有问题的代码进入版本控制系统。

### 3. 如何使用 lint-staged

#### 3.1 安装

在项目根目录下，使用 npm 或 yarn 安装 `lint-staged`：

```bash
npm install lint-staged --save-dev
# 或者使用 yarn
yarn add lint-staged --dev
```

#### 3.2 配置

`lint-staged` *通常与 Husky 配合使用，借助 Husky 的 Git 钩子在代码提交前触发 `lint-staged` 执行代码检查*。参考 [[Husky_2]]

首先，确保已经安装并配置了 Husky（具体配置方法可参考前文关于 Husky 的介绍）。

然后，*在 `package.json` 中添加 `lint-staged` 的配置*，指定对不同类型文件要执行的 lint 命令。以下是一个示例：

```json
{
  "lint-staged": {
    "*.js": ["eslint --fix", "prettier --write"],
    "*.css": ["stylelint --fix", "prettier --write"]
  }
}
```

上述配置表示：

- 对于暂存区中所有扩展名为 `.js` 的文件，先执行 `eslint --fix` 进行 ESLint 检查并尝试自动修复问题，然后执行 `prettier --write` 进行代码格式化。
- 对于暂存区中所有扩展名为 `.css` 的文件，先执行 `stylelint --fix` 进行 Stylelint 检查并尝试自动修复问题，再执行 `prettier --write` 进行代码格式化。

#### 3.3 集成到 Husky 钩子

在 Husky 的 `pre-commit` 钩子中添加 `lint-staged` 命令。可以使用以下命令添加钩子：

```bash
npx husky add .husky/pre-commit "npx lint-staged"
```

这会在 `.husky` 文件夹下的 `pre-commit` 文件中添加 `npx lint-staged` 命令，意味着在每次代码提交前，`lint-staged` 会自动对暂存区的文件执行配置好的 lint 命令。如果检查不通过，提交操作将被阻止。

### 4. 注意事项

- **命令执行顺序**：在 `lint-staged` 的配置中，命令是按顺序依次执行的。例如，对于 `.js` 文件，先执行 `eslint --fix` 再执行 `prettier --write`，要注意命令顺序可能会影响最终的代码检查和格式化结果。
- **错误处理**：当 `lint-staged` 中的某个命令执行失败时，整个检查流程会中断，提交操作也会被阻止。要确保配置的命令能够正确处理错误，并给出清晰的错误信息，方便开发者定位和解决问题。
- **配置灵活性**：`lint-staged` 的配置可以根据项目需求进行灵活调整，除了执行 lint 命令，还可以执行其他自定义脚本，如单元测试等。
