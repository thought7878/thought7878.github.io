这是一份系统化、结构化的 Git 知识点全景指南。内容从底层原理到工程实践，兼顾日常开发与面试深度，特别标注了前端场景与高频考点。

---
## 一、核心架构与底层原理（理解 Git 的“灵魂”）
| 概念          | 说明                                                                      | 面试/实战意义                                      |
| ----------- | ----------------------------------------------------------------------- | -------------------------------------------- |
| **三大区域**    | 工作区（Working Directory）→ 暂存区（Index/Stage）→ 本地仓库（Repository）              | 理解 `add`、`commit`、`checkout` 的作用边界           |
| **快照机制**    | Git 每次 commit 保存的是**项目完整快照**，未修改的文件仅保存指针。非 SVN 的差异补丁。                   | 解释为什么 Git 快、分支轻、回退安全                         |
| **内容寻址**    | 所有数据通过 SHA-1/SHA-256 哈希值标识，内容变则哈希变，保证完整性与不可篡改。                          | 理解 commit hash 的唯一性、`git fsck` 校验原理          |
| **四大对象**    | `Blob`（文件内容）<br>`Tree`（目录结构）<br>`Commit`（元数据+父指针+Tree指针）<br>`Tag`（引用标签） | 底层存储模型，高级排查与工具开发基础                           |
| **HEAD 指针** | 指向当前分支最新 commit 的符号引用，本质是 `.git/HEAD` 文件内容。                             | 理解 `HEAD~1`、`ORIG_HEAD`、分离头指针（detached HEAD） |

---
## 二、基础工作流与高频命令
### 1. 初始化与配置
```bash
git init                     # 初始化仓库
git clone <url>              # 克隆远程仓库
git config --global user.name "xxx"
git config --global user.email "xxx@xx.com"
git config --global core.autocrlf input  # Mac/Linux 推荐
git config --global pull.rebase true     # 现代协作推荐
```
📌 `.gitignore` 规则：`*.log`（忽略所有日志）、`!important.log`（反选）、`build/`（目录）、`/dist`（仅根目录）

### 2. 状态与差异
```bash
git status                   # 查看文件状态
git diff                     # 工作区 vs 暂存区
git diff --staged            # 暂存区 vs 最近一次 commit
git diff HEAD~1 HEAD         # 两次 commit 之间的差异
```

### 3. 提交与修改
```bash
git add .                    # 添加所有变更
git commit -m "feat: xxx"    # 提交
git commit --amend           # 修改最近一次 commit（信息或内容）
git commit --amend --no-edit # 仅追加文件，不修改 message
```

---
## 三、分支管理与合并策略（团队协作核心）
### 1. 分支本质
分支仅是 `.git/refs/heads/` 下的一个**文本文件**，内容为 40 位 commit hash。创建/切换成本极低。

### 2. 常用命令
```bash
git branch <name>            # 创建分支
git switch <name>            # 切换分支（推荐替代 checkout）
git branch -d/-D <name>      # 删除本地分支
git merge <branch>           # 合并分支
git rebase <branch>          # 变基（线性历史）
```

### 3. Merge vs Rebase（🔥 必考）
| 维度 | `git merge` | `git rebase` |
|------|-------------|--------------|
| 历史形态 | 保留分叉，生成 merge commit | 线性历史，重写提交 |
| 安全性 | 安全，不改写已存在 commit | 危险，会改变 commit hash |
| 适用场景 | 公共分支合并、保留协作痕迹 | 个人分支整理、提交前 squash |
| **黄金法则** | ✅ 公共分支永远用 merge | ❌ 绝对不要 rebase 已 push 的公共提交 |

### 4. 冲突解决标准流程
1. `git status` 定位冲突文件
2. 手动编辑解决 `<<<<<<<` / `=======` / `>>>>>>>` 标记
3. `git add <file>` 标记已解决
4. `git commit`（merge）或 `git rebase --continue`（rebase）
5. **前端必做**：跑 `npm run lint && npm run test && npm run build` 验证

---
## 四、远程协作与同步机制
### 1. 远程操作
```bash
git remote add origin <url>
git fetch origin             # 仅拉取，不合并（安全）
git pull origin main         # fetch + merge/rebase
git push origin <branch>
git push -u origin <branch>  # 设置上游跟踪
```

### 2. 强制推送的正确姿势
```bash
# ❌ 危险：可能覆盖他人新提交
git push -f origin feature

# ✅ 安全：仅当远程未变化时才强制推送
git push --force-with-lease origin feature
```

### 3. PR/MR 标准流程
`创建功能分支 → 开发提交 → push → 提 PR → Code Review → CI 检查 → 修改 → Squash & Merge`
📌 前端团队通常要求：关联 Issue、过 Lint/Test/Build、至少 1 个 Approve、禁止直接 push 到 main。

---
## 五、历史管理、撤销与恢复（后悔药）
### 1. Reset 三模式（🔥 必考）
| 参数 | commit 历史 | 暂存区 | 工作区 | 适用场景 |
|------|------------|--------|--------|----------|
| `--soft` | 回退 | 保留 | 保留 | 重新组织 commit |
| `--mixed`（默认） | 回退 | 清空 | 保留 | 取消暂存，重新 add |
| `--hard` | 回退 | 清空 | 清空 | 彻底丢弃（危险） |

### 2. 安全回退（已推送的公共历史）
```bash
git revert <commit-hash>     # 生成新提交抵消旧提交，不改写历史
```

### 3. 暂存与恢复
```bash
git stash                    # 保存未提交修改
git stash list               # 查看列表
git stash pop                # 恢复并删除
git stash apply stash@{0}    # 恢复但保留记录
git stash -u                 # 包含 untracked 文件
```

### 4. 救命命令：reflog
```bash
git reflog                   # 查看所有 HEAD 移动记录
git checkout -b recover <hash> # 从 reflog 找回误删分支/commit
```
📌 `reflog` 仅本地有效，默认保留 90 天。是误操作后的第一救援工具。

---
## 六、高级特性与前端工程化结合（🔥 极高频）
### 1. Git Hooks 原理
Git 在特定事件触发 `.git/hooks/` 下的脚本。常用：
- `pre-commit`：代码检查、格式化
- `commit-msg`：校验 commit message 格式
- `pre-push`：跑完整测试/构建
- `post-merge`：依赖更新提示

### 2. Husky + lint-staged（现代前端标配）
```json
// package.json
"husky": { "hooks": { "pre-commit": "lint-staged" } },
"lint-staged": {
  "*.{js,ts,jsx,tsx}": ["eslint --fix", "prettier --write"],
  "*.{css,scss,json}": ["prettier --write"]
}
```
**原理**：Husky 安装 Git hooks → lint-staged 读取 `git diff --staged` 文件列表 → 仅对暂存文件执行命令 → 大幅提升速度。

### 3. Commit 规范与自动化
- **Conventional Commits**：`feat:`, `fix:`, `docs:`, `style:`, `refactor:`, `test:`, `chore:`, `perf:`, `ci:`
- **Commitlint**：提交时校验格式
- **自动化发版**：`standard-version` / `changesets` / `semantic-release` 基于 commit 自动 bump version、生成 CHANGELOG、打 tag

### 4. CI/CD 触发与优化
- 触发事件：`push`、`pull_request`、`release`
- 前端 CI 典型流程：`npm ci → lint → test → build → upload artifacts → deploy`
- 缓存策略：`~/.npm`、`node_modules`、构建缓存（Vite/Webpack cache）、Playwright 浏览器缓存

---
## 七、排查工具与高级命令
| 命令 | 作用 | 场景 |
|------|------|------|
| `git bisect start/bad/good/reset` | 二分查找定位 bug commit | 线上回退排查 |
| `git blame <file>` | 逐行查看最后修改者与 commit | 追责/理解代码意图 |
| `git log -S "keyword"` | 搜索代码内容变更历史 | 找某段逻辑何时引入 |
| `git cherry-pick <hash>` | 将指定 commit 应用到当前分支 | hotfix 同步多版本 |
| `git filter-repo` | 清理历史大文件/敏感信息 | 仓库瘦身/安全合规 |
| `git worktree add <path> <branch>` | 同一仓库多分支并行工作 | 同时开发+修 bug 不切换分支 |

---
## 八、最佳实践与避坑指南（血泪经验）
### ✅ 推荐做法
1. **原子化提交**：一个 commit 只做一件事，方便 review/revert/cherry-pick
2. **本地多 commit，推送前整理**：`git rebase -i HEAD~3` 合并/重排/修改 message
3. **先 fetch 再合并**：避免 `pull` 直接产生意外 merge commit
4. **敏感信息零容忍**：`.env` 进 `.gitignore`，提交前用 `git-secrets` 扫描
5. **统一换行符**：团队配置 `core.autocrlf` 或 `.gitattributes`：`* text=auto eol=lf`

### ❌ 致命错误
- 在 `main`/`develop` 上 `rebase` 或 `reset --hard`
- 使用 `git push -f` 覆盖公共分支
- 提交 `node_modules/`、`dist/`、`.DS_Store`、`*.log`
- Commit message 写 `update`、`fix bug`、`111`
- 冲突解决后不跑构建/测试直接 push

---
## 九、底层原理进阶（面试深度追问）
| 问题 | 核心答案 |
|------|----------|
| Git 为什么比 SVN 快？ | 本地操作、快照机制、C 实现、packfile 压缩、索引缓存 |
| `.git/index` 是什么？ | 二进制暂存区缓存文件，加速 `status`/`diff`，记录文件元数据与哈希 |
| `git gc` 做了什么？ | 清理松散对象、打包成 packfile、删除不可达对象、优化性能 |
| 分支切换为什么快？ | 仅更新 HEAD 指针与工作区文件，无网络/差异计算开销 |
| 如何彻底删除历史敏感文件？ | `git filter-repo` 重写历史 → 强制推送 → **所有协作者重新 clone** → 轮换密钥（Git 历史无法真正“擦除”） |

---
## 📚 学习与实战建议
1. **可视化辅助**：使用 `git log --oneline --graph --all` 或 GitKraken/SourceTree 理解指针移动
2. **沙盒练习**：`mkdir git-lab && cd git-lab && git init`，故意制造冲突、误删、rebase，用 `reflog` 恢复
3. **阅读官方文档**：[Pro Git 中文版](https://git-scm.com/book/zh/v2)（第 2、3、7 章必读）
4. **前端专项**：亲手配置一次 `husky + lint-staged + commitlint + changesets`，跑通 PR → CI → 自动发版流程

需要我提供：
- 🔹 `git rebase -i` 交互式整理提交的完整实操示例？
- 🔹 Husky v8 + lint-staged + commitlint 现代配置模板？
- 🔹 Git 底层对象存储与 packfile 原理图解？
- 🔹 一份可打印的 Git 命令速查表（按场景分类）？

回复对应编号或具体方向，我为你展开。