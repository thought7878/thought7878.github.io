以下是 `git restore` 的系统化总结。作为 Git 2.23 引入的**现代文件恢复命令**，它彻底拆分了 `git checkout` 的混乱职责，是当前团队协作、CI 脚本、日常开发的安全首选。内容按原理、参数、场景对照、底层逻辑、工程实践、面试考点组织，直接对标一线开发标准。

---
## 一、核心定位与本质
| 维度 | 说明 |
|------|------|
| **诞生背景** | Git 2.23 与 `git switch` 同期引入，解决 `git checkout` “既切分支又恢复文件”的语义歧义与误操作风险。 |
| **核心职责** | **纯文件级状态恢复**：仅操作工作区（磁盘文件）与暂存区（`.git/index`），**绝对不移动 HEAD 或分支指针**。 |
| **设计哲学** | 语义明确、职责单一、安全可控。用“手术刀”替代“瑞士军刀”，降低新手与脚本误触概率。 |
| **版本要求** | Git ≥ 2.23（2019 年发布）。老旧 CI 环境或服务器需升级 Git 方可使用。 |

---
## 二、核心参数与语法
```bash
git restore [<options>] [--] <pathspec>...
```

| 参数 | 作用 | 默认行为 |
|------|------|----------|
| `--worktree` / `-W` | 恢复工作区文件（磁盘） | 未指定 `--staged` 时默认生效 |
| `--staged` / `-S` | 恢复暂存区（取消暂存 / unstage） | 仅动 `.git/index`，工作区代码保留 |
| `--source=<tree>` / `-s <tree>` | 指定恢复来源 | 默认 `HEAD`。可填 commit hash、分支名、tag |
| `--overlay`（默认） | 叠加模式：仅覆盖源树存在的文件，保留其他文件 | 安全，推荐日常使用 |
| `--no-overlay` | 严格模式：删除源树不存在的文件 | ⚠️ 危险，可能误删未跟踪文件 |
| `--recurse-submodules` | 同步恢复子模块 | Monorepo/子模块项目专用 |

📌 `--` 的作用：分隔参数与文件路径。当文件名与分支名/参数冲突时必加（如 `git restore -- -config.json`）。

---
## 三、高频场景与命令对照（🔥 实战核心）
| 场景 | 传统命令（易混淆） | `git restore` 现代命令 | 行为说明 |
|------|-------------------|------------------------|----------|
| 丢弃工作区修改 | `git checkout -- <file>` | `git restore <file>` | 恢复到最后一次 `commit` 或 `add` 的状态 |
| 取消暂存（unstage） | `git reset HEAD <file>` | `git restore --staged <file>` | 仅清空暂存区，工作区代码**完整保留** |
| 同时丢弃暂存+工作区 | `git checkout HEAD -- <file>` | `git restore --staged --worktree <file>` | 彻底回退到 `HEAD` 快照，代码完全还原 |
| 从指定分支提取文件 | `git checkout <branch> -- <file>` | `git restore --source=<branch> <file>` | 精准拉取其他分支的单个文件，不切分支 |
| 恢复误删的已跟踪文件 | `git checkout HEAD -- <file>` | `git restore <file>` | 文件未 commit 前删除，可一键找回 |
| 清理未跟踪文件 | `git clean -fd` | ❌ `restore` 不处理 untracked | 需用 `git clean`，`restore` 仅管已跟踪文件 |

---
## 四、底层机制与行为逻辑
| 机制 | 说明 |
|------|------|
| **操作边界** | 仅修改 `.git/index`（二进制暂存缓存）与磁盘文件。不触碰 `.git/HEAD`、`refs/heads/*`、`reflog`。 |
| **数据源读取** | 默认从 `HEAD` 对应的 Tree 对象提取 Blob 内容。指定 `--source` 时，从目标 commit/tag/branch 的 Tree 提取。 |
| **与 `checkout` 区别** | `checkout` 是多功能命令（切分支/恢复文件/进 detached），文件名与分支名同名时优先切分支，易误触；`restore` 强制只认文件路径，语义绝对安全。 |
| **与 `reset` 区别** | `reset` 的核心是**移动 HEAD/分支指针**，`--mixed/--hard` 只是顺带更新 index/worktree；`restore` 指针绝对静止，纯文件级操作。 |
| **冲突处理限制** | `git restore` **不支持** `--ours` / `--theirs`。合并冲突时仍需用 `git checkout --ours/--theirs <file>` 或手动解决。 |

---
## 五、前端工程化与实战场景
| 场景 | `git restore` 的作用 | 实战技巧 |
|------|----------------------|----------|
| **Husky pre-commit 回滚** | 脚本校验失败时自动取消暂存 | `git restore --staged .` 避免半成品污染暂存区 |
| **CI/CD 环境清理** | 流水线中临时修改配置后快速还原 | `git restore --source=HEAD .` 确保后续步骤基线一致 |
| **Monorepo 局部同步** | 仅拉取主分支某个包的配置，不切分支 | `git restore --source=main packages/ui/tsconfig.json` |
| **Code Review 快速验证** | 临时切出他人 PR 的单个文件对比 | `git restore --source=origin/feat-xxx src/utils/format.ts` |
| **脚本安全替代** | 替代 `checkout --` 避免文件名冲突误切分支 | 自动化脚本统一用 `restore`，提升鲁棒性 |

---
## 六、面试高频考点
| 问题 | 核心答题要点 |
|------|--------------|
| 为什么 Git 要引入 `git restore`？ | 拆分 `checkout` 的过载职责。`checkout` 既管分支切换又管文件恢复，语义歧义且易误操作（文件名与分支名冲突时优先切分支）。`restore` 专注文件/暂存区恢复，不碰 HEAD，更安全清晰。 |
| `git restore --staged` 和 `git reset HEAD` 区别？ | 行为结果一致（取消暂存），但 `restore --staged` 语义明确、不移动 HEAD、不支持范围回退（如 `HEAD~1`），专为 unstage 设计，符合现代 Git 职责分离理念。 |
| 它会移动 HEAD 或分支指针吗？ | **绝对不会**。仅操作 `.git/index` 和工作区文件。这是与 `reset`/`checkout` 的本质区别。 |
| 能恢复未 `git add` 的新文件吗？ | 不能。`restore` 仅处理**已跟踪文件**（tracked）。未跟踪文件需用 `git clean -fd` 清理或手动处理。 |
| 文件名和分支名同名时，`checkout` 和 `restore` 表现？ | `git checkout xxx` 优先切换分支，易误触；`git restore xxx` 强制视为文件路径，安全恢复。现代脚本推荐 `restore`。 |

---
## 七、避坑指南 & 最佳实践
### ⚠️ 常见误区
- 误用 `--no-overlay` 导致未提交的新文件/配置被静默删除
- 以为 `restore` 能处理 untracked 文件（实际需用 `git clean`）
- 在合并冲突时尝试 `git restore --ours`（不支持，需用 `checkout`）
- 老旧 CI 环境（Git < 2.23）直接跑 `restore` 导致流水线失败

### ✅ 推荐做法
1. **日常开发全面替代**：用 `restore` 替换 `checkout --` 和 `reset HEAD <file>`，形成肌肉记忆
2. **恢复前必确认**：先 `git diff` 或 `git diff --staged` 查看差异，再执行恢复
3. **脚本强制加 `--`**：`git restore -- "$FILE"` 避免路径解析歧义
4. **团队统一版本**：确保本地/CI Git ≥ 2.23，或在 `package.json` `engines` 中声明最低版本
5. **冲突场景保留 checkout**：合并冲突时继续使用 `git checkout --ours/--theirs <file>`，`restore` 不越界

---
## 📦 附：速查命令清单（直接复制）
```bash
# 🗑️ 丢弃工作区修改（最常用）
git restore src/App.vue
git restore .                  # 丢弃所有已跟踪文件修改

# 📤 取消暂存（unstage）
git restore --staged package.json
git restore --staged .         # 清空整个暂存区，代码保留

# 🔄 彻底回退到 HEAD（暂存+工作区）
git restore --staged --worktree .

# 🌿 从其他分支/commit 提取单个文件
git restore --source=main README.md
git restore --source=a1b2c3d src/utils/format.ts

# 🛡️ 安全脚本写法（防路径歧义）
git restore -- "$CONFIG_FILE"
```

---
需要我提供：
- 🔹 `git restore` vs `git checkout` vs `git reset` 三维对比决策树（按场景选命令）？
- 🔹 在 Husky `pre-commit` 中结合 `restore --staged` 实现校验失败自动回滚的完整脚本？
- 🔹 一份可打印的 Git 文件状态管理速查卡（含 restore/clean/checkout/reset 边界）？

回复对应方向，我为你展开。