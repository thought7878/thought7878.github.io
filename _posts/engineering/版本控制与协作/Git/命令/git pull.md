`git pull` 是 Git 中用于**从远程仓库获取更新并合并到当前分支**的命令。它本质上是两个命令的组合：

```
git fetch + git merge
```

---

### 基本用法

```bash
git pull
```

这个命令会：
- 从远程仓库（默认是 origin）*获取最新提交*；
- *自动尝试将远程分支的更新合并*到你当前所在的本地分支。

例如，如果你当前在 `main` 分支，该命令会拉取远程 `origin/main` 的更新，并尝试合并。

---

### 指定远程仓库和分支

```bash
git pull origin main
```

这表示从名为 `origin` 的远程仓库中拉取 `main` 分支的更新，并合并到当前分支。

---

### 使用 rebase 合并更新（推荐保持提交历史清晰）

```bash
git pull --rebase
```

或者指定分支：

```bash
git pull --rebase origin main
```

这样会使用 `rebase` 方式而不是 `merge` 来整合远程更改，避免产生额外的合并提交，使提交历史更线性、清晰。

> 注意：如果之前已经配置了默认合并方式为 rebase，可以不需要加 `--rebase`。

---

### 查看拉取后的差异

你可以使用以下命令查看拉取前后的变化：

```bash
git log HEAD@{1}..
```

这会显示自上次拉取以来新增的提交。

---

### 常见问题处理

#### 1. 出现冲突时如何处理？

当 Git 无法自动合并改动时，会提示冲突文件。

步骤如下：

```bash
# 查看冲突文件
git status

# 手动编辑冲突文件，解决冲突内容

# 标记冲突已解决
git add <filename>

# 继续合并（如果是普通 merge）
git commit
```

如果是使用 `--rebase` 拉取，则继续执行：

```bash
git rebase --continue
```

若想放弃 rebase 过程中的合并：

```bash
git rebase --abort
```

---

### 与 git fetch 的区别

| 命令             | 是否下载更新 | 是否自动合并 | 是否修改工作区 | 推荐用途 |
|------------------|---------------|----------------|----------------|-----------|
| `git pull`       | 是            | 是             | 可能           | 快速同步远程更新 |
| `git fetch`      | 是            | 否             | 否             | 安全查看后再决定是否合并 |

---

### 总结常用命令

```bash
# 拉取远程更新并自动合并
git pull

# 拉取指定分支的更新
git pull origin main

# 使用 rebase 方式拉取更新
git pull --rebase
git pull --rebase origin main

# 解决冲突后继续 rebase
git rebase --continue

# 放弃 rebase
git rebase --abort
```

如果你有特定场景或操作目标，我可以为你写出完整的命令流程。