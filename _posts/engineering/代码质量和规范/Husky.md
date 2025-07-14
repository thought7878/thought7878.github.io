**Husky** 是一个用于*在 Git 提交流程中运行自定义脚本*的工具，它可以帮助你*在代码提交前或推送前自动执行一些任务*，如：

- ✅ 代码格式化（Prettier）
- ✅ 代码检查（ESLint）
- ✅ 单元测试（Jest）
- ✅ 提交信息校验（Commitlint）
- ✅ 构建/编译验证
- ✅ 防止敏感文件提交

---

## 一、为什么使用 Husky？

### 核心目的：**提升代码质量 & 统一团队协作规范**

| 优点         | 描述                            |
| ---------- | ----------------------------- |
| 自动化校验      | 每次提交都经过 Lint 或测试，防止低级错误合并到主分支 |
| 统一风格       | 避免因个人编码风格不同造成代码混乱             |
| 防止非法提交     | 如提交了 `.env` 文件、未通过测试就提交等      |
| 支持 CI 更好集成 | 减少 CI 因风格问题失败的概率              |

---

## 二、Husky 的核心功能：Git Hooks 管理

*Git 原生支持 `hooks`（钩子）机制*，可以监听 Git 操作事件（如 commit、push），并触发脚本。

*但原生 Git Hooks 不会随项目一起提交到仓库，也不方便管理*。**Husky 就是用来简化这个过程的工具**。

### 支持的 Git Hooks（常用）

| Hook 名称 | 触发时机 | 推荐用途 |
|-----------|----------|----------|
| `pre-commit` | 提交前 | 运行 Linter / 测试 |
| `commit-msg` | 提交信息时 | 校验 Commit Message 是否符合规范 |
| `prepare-commit-msg` | 准备提交信息时 | 自动修改默认提交信息 |
| `pre-push` | 推送前 | 运行完整测试套件 |
| `post-checkout` | 切换分支后 | 自动安装依赖或清理缓存 |
| `post-merge` | 合并后 | 重新构建或安装依赖 |

---

## 三、快速上手 Husky

### 1. 安装 Husky

```bash
npm install husky --save-dev
```

或使用 `npx` 快速初始化：

```bash
npx husky-init && npm install
```

> ⚠️ 注意：如果你用的是 Yarn，请使用：
```bash
yarn add husky --dev
npx husky-init
```

这会自动创建 `.husky` 目录，并在 `package.json` 中添加 `"prepare"` 脚本（*确保每次安装依赖后 hooks 生效*）。

---

### 2. 添加 Git Hook 示例

#### 示例 1：添加 `pre-commit` hook（执行 ESLint）

```bash
npx husky add .husky/pre-commit "npm run lint"
```

此时 `.husky/pre-commit` 文件内容为：

```sh
#!/bin/sh
. "$(dirname "$0")/_/husky-init.sh"
npm run lint
```

如果 `npm run lint` 返回非 0 状态码（表示出错），提交将被中断。

---

#### 示例 2：添加 `commit-msg` hook（校验提交信息）

先安装 [commitlint](https://commitlint.js.org/)：

```bash
npm install @commitlint/config-conventional @commitlint/cli --save-dev
```

然后创建配置文件 `.commitlintrc.cjs`：

```js
module.exports = {
  extends: ['@commitlint/config-conventional'],
};
```

接着添加 hook：

```bash
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

这样每次提交都会检查是否符合 [Conventional Commits](https://www.conventionalcommits.org/) 规范。

---

#### 示例 3：添加 `pre-push` hook（运行测试）

```bash
npx husky add .husky/pre-push "npm test"
```

这样在推送前会运行测试，避免未通过测试的代码被推送到远程仓库。

---

## 四、推荐搭配工具（与 Husky 结合使用）

| 工具 | 作用 | 推荐场景 |
|------|------|----------|
| **ESLint** | 代码规范 | `pre-commit` |
| **Prettier** | 代码格式化 | `pre-commit` |
| **Stylelint** | CSS/Sass/Less 样式检查 | `pre-commit` |
| **Jest / Vitest** | 单元测试 | `pre-commit` / `pre-push` |
| **commitlint** | 提交信息规范 | `commit-msg` |
| **lint-staged** | 只对暂存区文件进行 lint | 性能优化 + 更细粒度控制 |

---

## 五、进阶：配合 `lint-staged` 使用（推荐）

`lint-staged` 可以只对 Git 暂存区的文件进行 lint 或格式化，而不是整个项目。

### 安装：

```bash
npm install lint-staged --save-dev
```

### 配置 `package.json`：

```json
{
  "lint-staged": {
    "*.js": ["eslint --fix", "prettier --write"],
    "*.css": ["stylelint --fix"]
  }
}
```

### 修改 `pre-commit` hook：

```bash
npx husky add .husky/pre-commit "npx lint-staged"
```

这样只会对即将提交的文件进行处理，速度快，体验好。

---

## 六、常见问题与注意事项

| 问题 | 解决方法 |
|------|----------|
| 新人 clone 项目后 hooks 不生效 | 执行 `npm install` 自动触发 `prepare` 脚本 |
| 想跳过某个 hook | 使用 `git commit --no-verify`（慎用） |
| Husky 报错：`cannot find module husky/init` | 更新 husky 版本或重装依赖 |
| 在 CI 环境中不需要执行某些 hook | 可以加环境判断逻辑，例如：`process.env.CI !== 'true'` |

---

## 七、总结：推荐实践

| 场景 | 推荐 hook |
|------|------------|
| 代码格式化 & Lint | `pre-commit` + `lint-staged` |
| 提交信息规范 | `commit-msg` + `commitlint` |
| 提交前跑单元测试 | `pre-commit` |
| 推送前跑完整测试套件 | `pre-push` |
| 切换分支后自动安装依赖 | `post-checkout` |
| 防止敏感文件提交 | `pre-commit` + 自定义脚本 |

---

如果你正在使用 Vue、React、Vite、Next.js、Monorepo（如 Nx、Lerna、pnpm）、TypeScript 等技术栈，我也可以为你提供一套完整的 Husky + Linting + Testing 的自动化开发规范方案。欢迎继续提问！