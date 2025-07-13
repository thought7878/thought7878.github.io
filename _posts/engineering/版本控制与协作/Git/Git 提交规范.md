Git 提交规范（Commit Convention）是一种标准化的提交信息格式，目的是让团队协作更清晰、代码审查更容易，并支持自动化工具（如生成 Changelog、语义化版本号等）。最常用的 Git 提交规范是 **[Conventional Commits](https://www.conventionalcommits.org/)**。

---

## 一、为什么要使用 Git 提交规范？

1. ✅ **提高可读性**：一眼看出每次提交的目的。
2. ✅ **便于审查与追踪**：方便 Code Review 和问题定位。
3. ✅ **自动生成 Changelog**：配合工具自动提取变更日志。
4. ✅ **支持语义化版本控制（SemVer）**：用于 CI/CD 自动发布版本。
5. ✅ **增强团队协作一致性**：统一风格，避免混乱的提交信息。

---

## 二、Conventional Commits 标准格式

```text
<type>[optional scope]: <subject>

[body]

[footer]
```

### 1. `type`（必填）：表示本次提交的类型

| 类型         | 含义                                  |
| ---------- | ----------------------------------- |
| `feat`     | 新功能（feature）                        |
| `fix`      | 修复 bug                              |
| `chore`    | 构建流程或辅助工具的变动（不修改源码）                 |
| `docs`     | 文档更新                                |
| `style`    | 样式更改（不影响逻辑，如空格、缩进、CSS）              |
| `refactor` | 重构代码（既不修复 bug 也不添加功能）               |
| `perf`     | 性能优化                                |
| `test`     | 添加或修改测试                             |
| `build`    | 影响构建系统或依赖项（如 package.json）          |
| `ci`       | 持续集成配置文件修改（如 GitHub Actions、Travis） |
| `revert`   | 回滚之前的提交                             |

> 💡 推荐使用 [commitlint](https://commitlint.js.org/) 工具来校验提交格式。

### 2. `scope`（可选）：影响范围，比如模块名、组件名等

例如：
```text
feat(auth): add password strength meter
```

### 3. `subject`（必填）：简洁描述本次提交内容（不超过 50 字）

- 使用祈使句（如 “add” 而不是 “added”）
- 不要以大写字母开头
- 不加句号结尾

✅ 正确示例：
```text
feat: add login form validation
```

❌ 错误示例：
```text
Fix bug in user profile page.
```

### 4. `body`（可选）：详细说明（换行后写）

用一两句话解释做了什么、为什么这么做，以及可能的影响。

### 5. `footer`（可选）：关联 issue 或 breaking changes

- 关联 issue：`Closes #123`
- Breaking change（重大变更）：需在 footer 中注明

示例：
```text
BREAKING CHANGE: The API now returns an error if the user is not authenticated.
```

---

## 三、完整示例

### 示例 1：简单提交
```text
feat: add dark mode toggle
```

### 示例 2：带 body 的提交
```text
feat(settings): add option to export preferences

Add a new button in settings page that allows users to export their preferences as JSON file.
```

### 示例 3：修复 bug 并关联 issue
```text
fix: prevent crash when user is null

Closes #1234
```

### 示例 4：重大变更
```text
feat(auth): use new auth service

BREAKING CHANGE: Old auth API is deprecated, please update your integration code.
```

---

## 四、推荐工具与实践

### 1. Commitizen CLI（交互式提交）

安装：
```bash
npm install -g commitizen cz-conventional-changelog
```

使用：
```bash
git cz
```

它会引导你一步步填写符合规范的提交信息。

### 2. Husky + Commitlint（强制校验提交格式）

#### 安装：
```bash
npm install --save-dev husky @commitlint/config-conventional @commitlint/cli
```

#### 配置 `.commitlintrc.cjs`：
```js
module.exports = {
  extends: ['@commitlint/config-conventional']
};
```

#### 初始化 Husky：
```bash
npx husky-init && npm install
```

然后编辑 `.husky/commit-msg` 文件：
```sh
#!/bin/sh
. "$(dirname "$0")/_/husky-init.sh"
npx --no-install commitlint --edit "$1"
```

这样在每次提交时都会检查格式是否正确。

---

## 五、Git 提交流程建议

| 流程阶段 | 建议 |
|----------|------|
| 开发分支 | 基于 `develop` 或功能分支开发 |
| 提交信息 | 使用 Conventional Commits 规范 |
| PR/MR 描述 | 简述本次改动目的和影响 |
| Code Review | 每次合并前必须经过至少一人审核 |
| 合并方式 | 推荐使用 `Squash and Merge` 或 `Rebase`，保持主分支整洁 |

---

## 六、常用命令速查表

| 目的 | 命令 |
|------|------|
| 提交规范提交 | `git cz` |
| 查看提交历史 | `git log --oneline` |
| 查看某个提交详情 | `git show <commit-hash>` |
| 修改最后一次提交信息 | `git commit --amend` |
| 强制推送已修改的提交 | `git push -f`（慎用） |

---

## 七、总结

| 使用场景 | 推荐做法 |
|----------|-----------|
| 日常提交 | 使用 `git cz` 保证格式一致 |
| 团队协作 | 统一采用 Conventional Commits |
| 自动化构建 | 结合 `commitlint` 和 `husky` 校验 |
| 自动生成 changelog | 使用 `standard-version` 或 `changesets` |
| 发布版本 | 配合 Semantic Versioning（语义化版本号） |

---

如果你正在使用特定工具链（如 Vue CLI、React + Vite、Monorepo），我也可以提供定制化的 Git 提交规范和工具集成方案。欢迎继续提问！