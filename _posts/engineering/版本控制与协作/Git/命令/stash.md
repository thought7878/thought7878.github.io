`git stash` 是 Git 中用于**临时保存工作现场**的命令。它可以**将当前未提交的修改（工作区和暂存区）存储到一个临时栈中，让你干净地切换分支或处理紧急任务，之后再恢复现场**。

`git stash` 是 Git 中一个非常实用的命令，*它允许你**暂时保存**当前工作目录（包括暂存区）的修改，让工作区恢复到干净的状态（即最后一次提交的状态）*。**这样你可以切换到其他分支、拉取更新、修复紧急 bug，之后再恢复这些修改**。

---

## 核心命令速查表

| 命令 | 说明 | 是否保留 stash |
|------|------|----------------|
| `git stash` | 默认保存当前修改（不含未跟踪文件） | ✅ 保留 |
| `git stash save "msg"` | 保存并添加备注信息（旧版语法） | ✅ 保留 |
| `git stash push -m "msg"` | 保存并添加备注（新版推荐） | ✅ 保留 |
| `git stash list` | 查看所有保存的 stash 列表 | - |
| `git stash pop` | 恢复最新的 stash 并**删除**它 | ❌ 删除 |
| `git stash apply` | 恢复最新的 stash 但**保留**它 | ✅ 保留 |
| `git stash drop` | 删除最新的 stash | - |
| `git stash clear` | 删除所有 stash | - |
| `git stash show` | 查看 stash 的变更内容 | - |

---

## 常用场景与工作流

- 正在进行一项功能开发，但突然需要切换到另一个分支修复问题。
- 想测试一个干净的状态，但不希望提交当前未完成的工作。
- 拉取远程分支时，本地的修改产生冲突，需要先暂存。

### 场景 1：紧急切换分支
正在开发功能 A，突然需要修复紧急 Bug

```bash
# 1. 正在开发功能 A，突然需要修复紧急 Bug
git stash push -m "feature A work in progress"

# 2. 工作区变干净了，切换分支
git checkout hotfix-branch

# 3. 修复完 Bug 后，切回原分支
git checkout feature-branch

# 4. 恢复现场
git stash pop
```

### 场景 2：拉取远程代码前
本地有未提交修改，直接 pull 可能冲突

```bash
# 本地有未提交修改，直接 pull 可能冲突
git stash

# 拉取最新代码
git pull origin main

# 恢复修改
git stash pop
# ⚠️ 如果此时有冲突，需手动解决冲突后 commit
```

### 场景 3：保存未跟踪文件（新文件）
```bash
# 默认 stash 不保存新创建的文件（Untracked files）
git stash -u  # 或 --include-untracked
# 恢复时也要加 -u
git stash pop -u
```

### 场景 4：部分保存（只 stash 某些文件）
```bash
# 交互式选择要 stash 的文件
git stash -p
# 按 y 确认保存，按 n 跳过
```

---

## 高级操作技巧

### 1. 查看 stash 详情
```bash
# 查看列表
git stash list
# 输出示例：stash@{0}: On main: feature A work in progress

# 查看某个 stash 的具体变更
git stash show stash@{0}
git stash show -p stash@{0}  # 显示完整差异
```

### 2. 恢复指定的 stash
```bash
# 恢复列表中的某一个（不删除）
git stash apply stash@{1}

# 恢复并删除
git stash pop stash@{0}
```

### 3. 从 stash 创建分支（救命用法）
```bash
# 如果 stash 内容复杂，想基于它开发新分支
git stash branch new-branch-name stash@{0}
# 会自动创建分支并应用 stash，成功后删除该 stash
```

### 4. 清理 stash
```bash
# 删除某一个
git stash drop stash@{0}

# 清空所有
git stash clear
```

---

## 注意事项与风险

| 风险点 | 说明 | 解决方案 |
|--------|------|----------|
| **冲突风险** | `pop/apply` 时可能与当前代码冲突 | 手动解决冲突后 `git add` 即可 |
| **丢失风险** | `stash clear` 或 `drop` 后无法恢复 | 操作前先用 `git stash branch` 备份 |
| **未跟踪文件** | 默认不保存新创建的文件 | 使用 `git stash -u` |
| **索引文件** | 默认不保存已 add 但未 commit 的文件 | 使用 `git stash -a` (所有文件) |
| **长期存储** | stash 不适合长期保存代码 | 重要代码务必 `commit` 到分支 |

---

## Stash 内部原理简述

```
┌─────────────────────────────────────────┐
│  Stash 栈 (LIFO 后进先出)               │
├─────────────────────────────────────────┤
│  stash@{0}  ← 最新保存                 │
│  stash@{1}  ← 次新                     │
│  stash@{2}  ← ...                      │
└─────────────────────────────────────────┘
```
- 每次 `git stash` 相当于压栈（Push）。
- `git stash pop` 相当于出栈（Pop）。
- stash 信息存储在 `.git/refs/stash` 中。

---

## 最佳实践建议

1. **添加备注**：始终使用 `-m "描述"`，方便日后识别。
   ```bash
   git stash push -m "WIP: login page UI"
   ```
2. **及时清理**：定期 `git stash list` 查看，用完即删。
3. **冲突处理**：如果 `pop` 冲突，解决后记得 `git add` 并 `commit`，stash 会自动删除。
4. **重要代码**：*不要依赖 stash 备份代码，重要进度务必 `commit` 到临时分支*。
5. **跨分支恢复**：stash 可以在不同分支间恢复，但需注意文件路径冲突。

---

## 一分钟速记

```bash
# 存现场
git stash push -m "备注"

# 看列表
git stash list

# 恢复现场（删 stash）
git stash pop

# 恢复现场（留 stash）
git stash apply

# 删某个 stash
git stash drop stash@{0}

# 清空所有
git stash clear
```

如有具体 stash 冲突解决或恢复丢失 stash 的问题，欢迎继续提问！