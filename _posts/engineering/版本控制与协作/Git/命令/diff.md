以下是 `git diff` 的系统化总结。作为 Git 的**差异计算引擎**，它不仅是日常审查代码的基石，更是 CI 条件触发、lint-staged 按需校验、PR 审查的底层依赖。内容按原理、算法、场景、工程联动、面试考点组织，直接对标现代前端协作标准。

---
## 一、核心定位与本质
| 维度           | 说明                                                                    |
| ------------ | --------------------------------------------------------------------- |
| **本质**       | **按需计算差异**。Git 不存储 diff，而是*实时比对两个快照（Tree/Blob）*，输出标准 Unified Diff 格式。 |
| **作用边界**     | 纯只读命令。不修改工作区、暂存区或历史。输出可被 `patch`、CI、IDE 直接消费。                         |
| **设计哲学**     | 快照对比 + 语义对齐。通过算法智能匹配行级变更，支持重命名推断、空白忽略、词级高亮。                           |
| **与 SVN 区别** | SVN 存增量补丁；Git 存完整快照，`diff` 是动态计算结果。故 Git diff 更快、更灵活、可跨任意 commit 比对。  |

---
## 二、底层机制与算法
| 机制        | 说明                                                                                  |
| --------- | ----------------------------------------------------------------------------------- |
| **比对路径**  | `RefA → TreeA → BlobA` vs `RefB → TreeB → BlobB`。*仅对 hash 不同的文件执行行级 diff*。          |
| **核心算法**  | 默认 **Myers 差分算法**（O(ND) 复杂度）。支持 `--patience` / `--histogram` 提升长文件/重构场景的语义对齐。       |
| **重命名检测** | Git **不显式跟踪重命名**。通过比对 Blob 内容相似度（默认阈值 50%）推断 `R`（rename）/ `C`（copy）。参数：`-M` / `-C`。 |
| **输出格式**  | 标准 Patch：`--- a/` / `+++ b/` → `@@ -旧行,旧数 +新行,新数 @@` → `+`（新增）/ `-`（删除）/ 空格（上下文）。   |
| **性能特征**  | 大仓库/大文件可能卡顿。可通过路径限定、`--stat` 预览、外部 diff 工具或 `diff.algorithm` 优化。                    |

---
## 三、高频**参数**与语法速查
| 参数                              | 作用                    | 场景                     |
| ------------------------------- | --------------------- | ---------------------- |
| `--staged` / `--cached`         | 暂存区 vs HEAD           | 提交前审查（两者等价）            |
| `HEAD`                          | 工作区 vs HEAD           | 查看未暂存+已暂存的全部变更         |
| `A..B`                          | B 有但 A 没有的变更          | 直接差异比对                 |
| `A...B`                         | **从共同祖先起** B 的变更      | ✅ PR 审查标准用法（忽略 A 的新提交） |
| `--stat`                        | 文件级统计（增删行数）           | 快速评估影响范围               |
| `--name-only` / `--name-status` | 仅输出文件名 / 带状态(A/M/D/R) | 脚本过滤、CI 条件触发           |
| `-U<n>`                         | 上下文行数（默认 3）           | 调大便于理解逻辑，调小精简输出        |
| `-w` / `--ignore-all-space`     | 忽略所有空白字符变更            | 过滤 Prettier/格式化噪音      |
| `--color-words`                 | 词级高亮（非行级）             | 配置/文档/长字符串变更审查         |
| `--find-renames[=<n>%]`         | 启用重命名检测（默认 50%）       | 文件移动后保持历史连贯            |
| `--exit-code`                   | 有差异返回 1，无差异返回 0       | Shell 脚本条件判断神器         |

---
## 四、**核心场景**与命令对照（🔥 实战核心）
| 场景            | 命令                              | 说明                                             |
| ------------- | ------------------------------- | ---------------------------------------------- |
| **查看未暂存修改**   | `git diff`                      | **工作区 vs 暂存区（查看未添加到暂存区的工作区的修改）**               |
| **查看已暂存修改**   | `git diff --staged`             | **暂存区 vs HEAD（提交前必查）**                         |
| **查看工作区全部变更** | `git diff HEAD`                 | 未暂存 + 已暂存 vs HEAD                              |
| **对比两次提交**    | `git diff a1b2c3d..f4e5d6c`     | 精确 commit 级差异                                  |
| **PR 审查（推荐）** | `git diff main...feature`       | *仅看 feature 自分支点以来的变更*，**不受 main 新提交干扰**       |
| 仅看文件列表        | `git diff --name-status HEAD~1` | 输出 `M src/a.ts` / `A src/b.vue` / `D src/c.js` |
| 忽略格式化噪音       | `git diff -w HEAD~1`            | 过滤空格/换行/缩进变更，聚焦逻辑                              |
| 调用外部 GUI 工具   | `git difftool HEAD~1`           | 唤起 VSCode / Beyond Compare / Meld              |

---
## 五、前端工程化与 CI/CD 联动
| 场景 | `git diff` 的作用 | 实战技巧 |
|------|-------------------|----------|
| **lint-staged 核心** | 获取暂存文件列表，仅对变更文件跑 lint | `git diff --cached --name-only --diff-filter=ACM` |
| **CI 条件构建** | 按路径变更决定是否跑流水线 | `git diff --name-only HEAD~1 HEAD \| grep -q "packages/ui/" && npm run build:ui` |
| **PR 差异预览** | 生成变更摘要附在 PR 描述 | `git diff --stat main...feature` 输出文件级增删统计 |
| **阻断大文件提交** | pre-commit 检测单次 diff 体积 | `git diff --cached --stat \| awk '{sum+=$4} END {if(sum>500) exit 1}'` |
| **自动化 Patch 流** | 导出/应用差异补丁 | `git diff main...feature > feat.patch` → `git apply feat.patch` |

---
## 六、**面试**高频考点
| 问题                                   | 核心答题要点                                                                                                     |     |                                        |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------- | --- | -------------------------------------- |
| `A..B` 和 `A...B` 在 diff 中的区别？        | `..` 是绝对差异（B 减 A）；`...` 是**从共同祖先（merge-base）到 B 的差异**。PR 审查必须用 `...`，否则会把 main 的新提交也算作 feature 的变更。        |     |                                        |
| Git 如何检测文件重命名？                       | 不显式记录。通过比对 Blob 内容相似度（hash + 文本匹配），超过阈值（默认 50%）标记为 `R`。可用 `-M` 调整阈值或关闭。                                    |     |                                        |
| `git diff` 和 `git diff --staged` 区别？ | 前者比对**工作区 vs 暂存区**（未 add 的修改）；后者比对**暂存区 vs HEAD**（已 add 待提交的修改）。提交前审查用 `--staged`。                         |     |                                        |
| *为什么格式化后 diff 满屏飘红？怎么解决*？            | Prettier/ESLint 修改了空白/换行。解决：① 提交前统一格式化 ② 审查时加 `-w` 忽略空白 ③ 团队配置 `core.autocrlf` + `.gitattributes` 统一换行。    |     |                                        |
| 如何在脚本中判断是否有变更？                       | 用 `--exit-code`：`git diff --quiet HEAD~1 HEAD                                                              |     | echo "有变更"`。返回 0 无差异，1 有差异，适合 CI 条件分支。 |
| diff 性能差怎么优化？                        | ① 限定路径 `git diff -- src/` ② 先用 `--stat` 预览 ③ 配置 `diff.algorithm=histogram` ④ 大二进制文件设 `diff=binary` 跳过文本比对。 |     |                                        |

---
## 七、避坑指南 & 最佳实践
### ⚠️ 常见误区
- 在 PR 审查时用 `git diff main..feature`，导致 main 的新提交被误算进 feature 变更
- 忽略 `--staged`，提交前没审查暂存区，误提调试代码
- 对 `node_modules/` 或构建产物跑 diff，终端卡死（应进 `.gitignore`）
- 以为 Git 显式记录重命名（实际是相似度推断，阈值过低会误判）
- 在 CI 中全量 diff 不限制范围，拖慢流水线

### ✅ 推荐做法
1. **PR 审查统一用 `...`**：`git diff target...source` 是业界标准
2. **提交前必跑**：`git diff --staged --stat` → `git diff --staged` 二次确认
3. **脚本过滤状态**：`--diff-filter=ACMR` 仅关注新增/修改/重命名，忽略删除
4. **配置外部工具**：`git config --global diff.tool vscode` + `git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"`
5. **二进制文件声明**：`.gitattributes` 加 `*.png diff=binary`，避免无效文本比对

---
## 八、速查命令清单（直接复制）
```bash
# 🔍 日常审查
git diff                          # 未暂存修改
git diff --staged                 # 已暂存修改（提交前必查）
git diff HEAD                     # 工作区全部变更

# 🌿 分支/提交比对
git diff main...feature           # ✅ PR 标准审查（自分支点起）
git diff v1.0.0..v1.1.0 --stat    # 版本变更统计
git diff HEAD~1 -- src/utils.ts   # 仅看单文件上次变更

# 📊 脚本与 CI 友好
git diff --name-only --diff-filter=ACM HEAD~1
git diff --quiet HEAD~1 HEAD || echo "有变更，触发构建"
git diff -w HEAD~1                # 忽略空白/格式化噪音

# 🛠️ 外部工具 & 补丁
git difftool HEAD~1               # 唤起 GUI diff 工具
git diff main...feature > pr.patch
git apply pr.patch                # 应用补丁
```

---
需要我提供：
- 🔹 `lint-staged` 底层如何结合 `git diff --cached` 实现按需校验的源码级解析？
- 🔹 GitHub Actions 中基于 `git diff --name-only` 实现 Monorepo 智能按需 CI 的完整 YAML 模板？
- 🔹 一份可打印的 Git Diff 场景决策树与参数速查卡？

回复对应方向，我为你展开。