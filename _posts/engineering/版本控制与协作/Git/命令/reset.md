# Git Reset 完全总结

`git reset` 是 Git 中最强大但也*最危险的命令之一*。它的**核心作用**是*移动当前分支的 HEAD 指针*，并可*选择性地更新暂存区（Index） 和 工作区（Working Directory）*。

简单来说，它是*本地版本的时间机器*，但**使用不当会导致代码丢失**。

---

## 核心概念：Git 的三棵树

理解 `reset` 前，需明白 Git 的三个区域：

```
1. HEAD (当前分支指针)  → 指向最新的提交
2. Index (暂存区)       → 准备提交的文件
3. Working Dir (工作区)  → 你正在编辑的文件
```

`git reset` 的本质就是**移动 HEAD**，并决定如何处理 Index 和 Working Dir。

---

## 三种模式对比（核心）

| 模式 | 命令 | HEAD 指针 | 暂存区 (Index) | 工作区 (Working Dir) | 安全性 |
|------|------|----------|----------------|----------------------|--------|
| **Soft** | `--soft` | ✅ 移动 | ✅ 不变 (保持暂存) | ✅ 不变 (保持修改) | 🟢 安全 |
| **Mixed** | `--mixed` (默认) | ✅ 移动 | ✅ 重置 (取消暂存) | ✅ 不变 (保持修改) | 🟡 中等 |
| **Hard** | `--hard` | ✅ 移动 | ✅ 重置 (取消暂存) | ❌ **重置 (丢弃修改)** | 🔴 危险 |

### 图解说明
```
假设当前状态：HEAD → Commit C
你想回退到：Commit A

git reset --soft A   → HEAD 指向 A，C 和 B 的修改仍在暂存区 (git status 显示 Changes to be committed)
git reset --mixed A  → HEAD 指向 A，C 和 B 的修改在工作区但未暂存 (git status 显示 Changes not staged)
git reset --hard A   → HEAD 指向 A，C 和 B 的修改彻底丢失 (工作区干净)
```

---

## 常用场景与命令

### 场景 1：撤销最后一次提交（保留修改）
```bash
# 最常用！撤销 commit，但保留代码在暂存区
git reset --soft HEAD^

# 或者保留代码在工作区（未暂存）
git reset --mixed HEAD^
# 简写（默认就是 mixed）
git reset HEAD^
```

### 场景 2：取消暂存文件（Unstage）
```bash
# 误操作 git add . 后，想取消某个文件的暂存
git reset HEAD <filename>

# 取消所有文件的暂存
git reset HEAD
```
> 💡 注意：这里不指定 commit，默认是 HEAD，作用是**只操作暂存区**，不移动 HEAD 指针。

### 场景 3：彻底丢弃本地修改（危险）
```bash
# 回到上一个版本，丢弃所有未提交修改
git reset --hard HEAD^

# 回到指定 commit，丢弃之后的所有内容
git reset --hard <commit-hash>
```
> ⚠️ **警告**：`--hard` 后未提交的修改无法通过常规手段恢复！

### 场景 4：回退到远程版本
```bash
# 本地乱套了，想和远程仓库完全一致
git reset --hard origin/main
```

---

## Reset vs Revert vs Restore

| 命令 | 作用对象 | 是否修改历史 | 是否可推送远程 | 适用场景 |
|------|----------|--------------|----------------|----------|
| **git reset** | 本地 HEAD/暂存/工作区 | ✅ 是 (重写历史) | ❌ 否 (需强制推送) | 本地提交错误，未推送 |
| **git revert** | 创建新提交 | ❌ 否 (保留历史) | ✅ 是 | 已推送的提交需要撤销 |
| **git restore** | 文件或暂存区 | ❌ 否 | ✅ 是 | Git 2.23+ 新版，替代 checkout/reset 部分功能 |

```bash
# 新版替代方案 (Git 2.23+)
git restore --staged <file>   # 替代 git reset HEAD <file>
git restore <file>            # 替代 git checkout -- <file>
```

---

##  安全警告与最佳实践

### 1. 黄金法则
```
❌ 永远不要对已推送到公共分支（如 main/master）的代码使用 git reset --hard
✅ 如果已推送，请使用 git revert
```

### 2. 操作前备份
```bash
# 执行 reset 前，先创建备份分支
git branch backup-before-reset
```

### 3. 范围限制
```bash
# 只重置特定文件（不影响 HEAD 指针）
git reset HEAD <file>

# 重置整个分支（影响 HEAD 指针）
git reset --hard <commit>
```

---

## 救命稻草：找回丢失的提交

如果不小心 `reset --hard` 错了，可以用 `reflog` 找回！

```bash
# 1. 查看所有操作历史（包括 reset 前的 HEAD 位置）
git reflog

# 2. 找到你想要的 commit hash 或 reflog 索引（如 HEAD@{1}）
# 输出示例：a1b2c3d HEAD@{1}: reset: moving to HEAD^

# 3. 重置回去
git reset --hard HEAD@{1}
# 或
git reset --hard a1b2c3d
```

> 💡 `reflog` 是本地操作日志，默认保留 90 天，是误操作后的最后一道防线。

---

## 一分钟速查表

```
┌─────────────────────────────────────────────────────────┐
│  📌 Git Reset 速查                                      │
├─────────────────────────────────────────────────────────┤
│  撤销 commit 保留代码  → git reset --soft HEAD^         │
│  撤销 commit 不暂存    → git reset HEAD^                │
│  取消暂存文件        → git reset HEAD <file>            │
│  彻底丢弃修改        → git reset --hard HEAD^           │
│  同步远程代码        → git reset --hard origin/main     │
│  找回丢失提交        → git reflog → git reset --hard    │
└─────────────────────────────────────────────────────────┘
```

---

## 总结建议

1. **本地开发**：大胆用 `reset` 整理提交历史（如 squash commits）。
2. **公共分支**：严禁用 `reset` 改写历史，改用 `revert`。
3. **不确定时**：先用 `git status` 确认状态，操作前 `git branch backup` 备份。
4. **误操作后**：第一时间 `git reflog` 找回。

如有具体重置场景（如合并冲突后重置、子模块重置等），欢迎继续提问！