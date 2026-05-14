在前端面试中，Git 的考察通常不会停留在“背命令”层面，而是侧重 **工作原理、团队协作规范、工程化结合以及问题排查能力**。以下是高频考点分类整理，附带答题要点和前端场景提示：

---
### 🔹 一、基础概念与常用命令
| 常见问题                                       | 考察点 & 答题要点                                                                                         |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| Git 的三个区域是什么？                              | 工作区（Working Directory）→ 暂存区（Stage/Index）→ 本地仓库（Repository）。`add` 进暂存，`commit` 进仓库。                 |
| `git pull` 和 `git fetch` 的区别？              | `fetch` 只拉取远程更新到本地远程跟踪分支，不合并；`pull = fetch + merge`（或 rebase，取决于配置）。面试可补充：推荐先 `fetch` 查看差异再合并，更安全。 |
| `git status` / `git diff` / `git log` 的作用？ | 状态查看、差异对比、提交历史。可提 `git diff --staged`、`git log --oneline --graph` 等实用参数。                           |

---
### 🔹 二、分支管理与合并策略（高频）
| 常见问题 | 考察点 & 答题要点 |
|----------|------------------|
| `git merge` 和 `git rebase` 的区别？何时用哪个？ | `merge` 保留分叉历史，生成 merge commit；`rebase` 将当前分支提交“重放”到目标分支上，历史线性。**关键原则：公共分支绝对不用 rebase，个人开发分支可用 rebase 保持整洁。** |
| 如何解决代码冲突？ | ① `git status` 找冲突文件 → ② 手动编辑解决 `<<<<<<<` / `=======` / `>>>>>>>` → ③ `git add` 标记已解决 → ④ `git commit` 或 `git rebase --continue`。可补充：解决后务必本地跑一遍 lint/test/build。 |
| 你们团队用什么分支工作流？ | 常见：GitHub Flow（主分支+功能分支+PR，适合持续部署）、GitLab Flow（带环境分支）、Trunk-Based（主干开发，高频提交）。**避免死背 Git Flow**（较重，现代前端团队较少用）。结合你实际项目说明即可。 |

---
### 🔹 三、撤销、回退与历史修改
| 常见问题 | 考察点 & 答题要点 |
|----------|------------------|
| 如何撤销上一次 commit？ | 分场景：<br>• 保留修改：`git reset --soft HEAD~1`<br>• 取消暂存：`git reset --mixed HEAD~1`（默认）<br>• 彻底丢弃：`git reset --hard HEAD~1`（危险）<br>• 安全回退（已推送）：`git revert HEAD` 生成新提交，不改写历史。 |
| 如何修改最近一次 commit 的信息或内容？ | `git commit --amend`（修改信息或追加文件）。注意：仅适用于未 push 的提交。 |
| `git stash` 的作用？常用命令？ | 临时保存未提交的修改。常用：`stash` / `stash list` / `stash pop` / `stash drop` / `stash apply stash@{0}`。场景：切分支前不想 commit 半成品。 |

---
### 🔹 四、协作与远程操作
| 常见问题 | 考察点 & 答题要点 |
|----------|------------------|
| 什么是 PR / MR？标准流程是什么？ | Fork/建分支 → 开发提交 → push → 提 PR → Code Review → 修改 → 合并。可提：前端团队常要求 PR 关联 Issue、过 CI、至少 1 人 Approve。 |
| 什么时候可以用 `git push -f`？风险是什么？ | 仅用于**个人分支** rebase 或 amend 后同步远程。风险：覆盖他人提交，导致协作灾难。公共分支严禁 force push。 |
| 如何同步远程已删除的分支？ | `git fetch -p` 或 `git remote prune origin` 清理本地残留的远程跟踪分支。 |

---
### 🔹 五、高级技巧 & 问题排查（加分项）
| 常见问题 | 考察点 & 答题要点 |
|----------|------------------|
| 误删分支 / 丢失 commit 怎么找回？ | `git reflog` 查看所有 HEAD 移动记录 → 找到目标 commit hash → `git checkout -b <branch> <hash>` 或 `git reset --hard <hash>`。 |
| `git cherry-pick` 是什么？场景？ | 将指定 commit 应用到当前分支。场景：hotfix 同步到多个版本分支、挑取他人提交。 |
| 如何定位是哪次 commit 引入了 bug？ | `git bisect start` → `bad` / `good` 标记 → Git 二分自动 checkout → 测试标记 → `bisect reset` 结束。体现工程排查能力。 |
| 仓库里误提交了大文件 / 敏感信息怎么办？ | ① 立即 `git rm --cached` + 提交<br>② 用 `git filter-repo` 或 BFG 清理历史<br>③ 敏感信息必须轮换密钥，Git 历史无法真正“删除”。 |

---
### 🔹 六、前端工程化结合（🔥 极高频）
| 常见问题 | 考察点 & 答题要点 |
|----------|------------------|
| Git Hooks 是什么？前端怎么用？ | Git 在特定事件触发的脚本。前端常用：`pre-commit`（lint/test）、`commit-msg`（校验格式）、`pre-push`（跑构建）。 |
| Husky + lint-staged 的原理？ | Husky 安装 Git hooks；lint-staged 只对 `git add` 的文件执行命令，提升速度。典型配置：提交前自动 eslint + prettier + 单元测试。 |
| 如何规范 commit message？ | Commitlint + Conventional Commits（`feat:`, `fix:`, `chore:` 等）。配合 `standard-version` / `changesets` 自动生成 CHANGELOG 和语义化版本。 |
| Git 如何触发前端 CI/CD？ | Push/PR 事件触发 GitHub Actions / GitLab CI，执行 `npm ci → lint → test → build → deploy`。可提缓存策略（node_modules、构建缓存）。 |

---
### 💡 面试答题建议
1. **结合项目经验**：不要只背命令，说清“我们在 XX 项目中用 rebase 保持主分支线性，配合 husky 做提交前校验，PR 必须过 CI 和 1 个 reviewer”。
2. **区分场景**：个人分支 vs 公共分支、未 push vs 已 push、安全操作 vs 危险操作。
3. **体现工程思维**：面试官更看重你是否理解 Git 在团队协作、质量门禁、自动化流程中的角色。
4. **避坑提示**：
   - ❌ “我经常用 `git push -f`” → 直接挂
   - ❌ “rebase 比 merge 好” → 缺少场景限定
   - ✅ “公共分支用 merge 保留协作痕迹，个人分支用 rebase 整理提交，合并前 squash 保持历史清晰”

---
需要我提供一份 **Git 面试速查清单（可打印）** 或针对某个问题（如 rebase 原理、husky 配置、reflog 实战）展开详细示例吗？