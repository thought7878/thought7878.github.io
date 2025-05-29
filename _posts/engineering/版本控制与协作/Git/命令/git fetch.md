`git fetch` 是 Git 中用于**从远程仓库下载提交和分支信息**的命令，但它 **不会自动合并或修改你当前的工作区内容**。这使得它比 `git pull` 更安全、更可控。

---

## 一、基本概念

### `git fetch` 是做什么的？

- 从远程仓库（如 GitHub）获取最新的提交历史；
- 更新本地对远程分支的追踪信息（如 `origin/main`）；
- 不会改变你当前所在的分支或工作区内容。

---

## 二、常用使用方式

### 1. 获取所有远程分支的更新

```bash
git fetch
```

> 等价于：`git fetch origin`（默认远程仓库为 `origin`）

这个命令会：
- 下载远程仓库中你还没有的 commits；
- 更新你本地对远程分支的引用（如 `origin/main`, `origin/feature-branch`）；
- **不会影响你当前所在的本地分支**。

---

### 2. 获取特定分支的更新

```bash
git fetch origin main
```

只获取远程 `main` 分支的最新内容。

---

### 3. 查看远程分支更新内容

```bash
git log origin/main
```

可以查看远程 `main` 分支的最新提交记录。

或者对比本地和远程分支：

```bash
git log main..origin/main
```

显示本地 `main` 和远程 `origin/main` 的差异提交。

---

### 4. 合并远程更新到当前分支

虽然 `fetch` 不会自动合并，但你可以手动合并：

```bash
git fetch origin
git merge origin/main
```

也可以使用 `rebase` 来合并更新（推荐保持提交线性）：

```bash
git fetch origin
git rebase origin/main
```

---

## 三、与 `git pull` 的区别

| 对比项    | `git fetch`         | `git pull`             |
| ------ | ------------------- | ---------------------- |
| 是否下载更新 | ✅                   | ✅                      |
| 是否自动合并 | ❌（需要手动合并）           | ✅（等价于 `fetch + merge`） |
| 安全性    | **更安全**，可查看后再决定是否合并 | 直接合并，**可能引入冲突**        |
| 推荐使用场景 | 想了解更新内容后再决定如何处理     | 快速同步远程改动，适合信任更新的情况     |

如果你不确定该用 `fetch` 还是 `pull`，*建议优先使用* `fetch`，这样能更安全地查看和处理更新内容

---

## 四、常见使用流程示例

```bash
# 获取远程更新
git fetch origin

# 查看远程分支状态
git status -uno

# 查看具体更新内容
git log ..origin/main

# 确认无误后合并
git merge origin/main
```

或者使用 rebase 方式合并：

```bash
git rebase origin/main
```

---

## 五、小技巧

### 查看远程分支列表

```bash
git branch -r
```

### 删除远程已删除的分支的本地引用

```bash
git fetch --prune
```

或简写：

```bash
git fetch -p
```

---

## 总结

| 命令                            | 说明 |
|----------------------------------|------|
| `git fetch`                      | 获取远程仓库所有分支的更新 |
| `git fetch origin`               | 获取远程 origin 的更新 |
| `git fetch origin main`          | 只获取远程 main 分支的更新 |
| `git log origin/main`            | 查看远程 main 分支的提交历史 |
| `git merge origin/main`          | 将远程 main 分支合并到当前分支 |
| `git rebase origin/main`         | 使用变基方式合并远程更新 |
| `git fetch --prune`              | 清理本地已不存在于远程的分支引用 |



