## 基本概念

`工作区(workspace)`: 本地电脑存放项目文件的地方，我们平时开发写的代码也是在这个区进行的；

`暂存区(Index)`: 暂时存放文件的地方，通过`add`命令可以将工作区的文件添加到暂存区内；

`本地仓库(Repository)`: `.git`文件夹里还包括`git`自动创建的`master`分支，并且将`HEAD`指针指向`master`分支。使用`commit`命令可以将暂存区中的文件添加到本地仓库中；

`远程仓库(Remote)`: 项目代码在远程 git 服务器上，通常使用`clone`命令将远程仓库拷贝到本地仓库中，开发后通过`push`推送到远程仓库中；

![image](https://raw.githubusercontent.com/goto456/markdown-pictures/master/wengeblog/git/1.png)

## 常用命令

![](https://camo.githubusercontent.com/c0b9718c6be699aa4ff27b12ab21f16864c28d3e1d185d4383657ac2f1b75c30/68747470733a2f2f696c6c75737472617465642d6769742e72656164746865646f63732e6f72672f656e2f6c61746573742f5f696d616765732f6769742d666c6f77732e737667)

![File:Git operations.svg](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d8/Git_operations.svg/734px-Git_operations.svg.png)

![You Can Understand Git & Git Commands with These Diagrams ...](https://miro.medium.com/v2/resize:fit:1200/1*eGdNATA_BGQcA1xUvc9Irg.png)

### 配置信息

##### 查询配置信息

列出当前配置: `git config --list`；

列出 repository 配置: `git config --local --list`；

列出全局配置: `git config --global --list`；

列出系统配置: `git config --system --list`;

##### 设置配置信息

配置用户名: `git config --global/local user.name "your name"`；

配置用户邮箱: `git config --global/local user.email "youremail@github.com"`；

##### 其他配置信息

配置 Git 命令输出为彩色: `git config --global color.ui auto`；

配置 Git 使用的文本编辑器: `git config --global core.editor vi`；

### 工作区操作

#### 新建仓库

创建一个新的本地仓库: `git init`；

#### 提交

提交工作区所有文件到暂存区: `git add .`；

提交工作区指定文件到暂存区: `git add <file1> <file2> ...`；

提交工作区中某个文件夹中所有文件到暂存区: `git add [dir]`；

#### 撤销

删除工作区文件，并且从暂存区删除对应的文件记录: `git rm <file1> <file2> ...`；

<u>撤销工作区</u>指定文件的操作: `git checkout -- <file>`；撤销工作区所有文件的操作: `git checkout -- .`；

#### 更新文件

重命名文件，并将已改名文件提交到暂存区: `git mv [file-original] [file-renamed]`；

> Git 的文件命名默认情况下是不区分大小写的

### 暂存区操作

#### 提交到本地仓库

将暂存区的文件提交到本地仓库: `git commit -m "commit-info"`；

将工作区和暂存区的文件一起提交到本地仓库: `git commit -am "commit-info"`；

覆盖上次的`commit`记录，并将暂存区的文件提交到本地仓库: `git commit --amend -m "commit-info"`；

#### 撤销

<u>撤销</u>已经暂存的文件，恢复成和HEAD一样: `git reset HEAD <file>...`；

<u>删除</u>暂存区文件，但是工作区保留文件: `git rm --cached <file1> <file2>...`；

### 本地仓库操作

从远程仓库拉取代码并合并到本地: `git pull <remote-name> <remote-branch>:<local-branch>`，使用`rebase`的模式进行合并: `git pull --rebase <remote-name> <remote-branch>:<local-branch>`；

从远程仓库中抓取本地仓库中没有的更新: `git fetch [remote-name]`；

从远程仓库抓取所有分支的更新: `git fetch --all`；

将本地仓库某个分支推送到远程仓库上: `git push [remote-name] [branch-name]`，如果想将本地分支推送到远程仓库的不同分支: `git push <remote-name> <local-branch>:<remote-branch>`；

### 远程仓库操作

从远程仓库克隆项目: `git clone <url>`；

查看本地仓库关联的远程仓库: `git remote`，加上`-v`的参数后，会显示远程仓库的`url`地址；

添加远程仓库，一般会取一个简短的别名: `git remote add [remote-name] [url]`；

查看远程仓库的详细信息: `git remote show origin`；

移除远程仓库: `git remote rm [remote-name]`；

### 分支操作

**创建**分支: `git branch <branch-name>`；

从当前分支**切换**到其他分支:

```bash
// 原来的
git checkout <branch-name> | <commite id>
// 新的
git switch <branch-name>
```

**新建并切换**到新建分支: ；

```bash
// 原来的
git checkout -b <branch-name>
// 新的
git switch -c <branch-name>
```

**删除**分支: `git branch -d <branch-name>`；

**强制删除**分支: `git branch -D <branch-name>`；

将当前分支和指定分支进行**合并**: `git merge <branch-name>`；

**显示**本地仓库的所有分支: `git branch`；

**显示远程**仓库的所有分支: `git branch -r`；

显示本地仓库和远程仓库的所有分支: `git branch -a`；

查看哪些分支已经合并到当前分支: `git branch --merged`；

查看当前哪些分支还没有合并到当前分支: `git branch --no-merged`；

把远程分支合并到当前分支: `git merge <remote-name>/<branch-name>`；

**重新命名**分支: `git branch -m <old-branch-name> <new-branch-name>`；

### 差异比较

比较<u>工作区</u>的当前文件和<u>暂存区</u>之间的差异: `git diff <file-name>`；

比较<u>暂存区</u>的文件和<u>本地仓库（HEAD）</u>的差异: `git diff --cached`；

比较<u>暂存区</u>的指定文件和<u>本地仓库</u>（HEAD）的差异: `git diff <file-name> --cached`；

### 常用的操作：独自使用Git时的常见场景

修改最新 commit 的 message：`git commit --amend`；

查询当前工作区所有文件的状态: `git status`；

查看最近的三条提交历史（单行显示）: `git log --oneline -3`；

修改老旧 commit 的 message：

把连续的多个 commit 整理成一个：

把间隔的几个 commit 整理成一个：

### git tag（标签）

列出所有的标签: `git tag`；

搜索符合特定条件的标签: `git tag -l "v1.4.2.*"`；

创建一个轻量级标签: `git tag v1.5`；

创建一个含备注类型的标签: `git tag -a v1.4.2 -m "commit-info"`；

使用`git show`命令查看对应标签的版本信息，并连同显示打标签时的提交对象: `git show v1.4.2`；

将标签推送到远程仓库中: `git push origin v1.4.2`；

将本地所有标签全部推送到远程仓库中: `git push origin --tags`；

### git stash（暂存）

隐藏当前变更，以便能够切换分支: `git stash`；

查看当前所有的暂存列表: `git stash list`；

应用最新的储藏: `git stash apply`，如果想应用其他的储藏: `git stash apply stash{0}`；如果使用`pop`命令不仅可以重新应用储藏，还可以立刻从堆栈中清除`stash`: `git stash pop`；

## .gitignore

`.gitignore` 文件的作用是不再管理指定的文件修改。

### 语法规范

空行不匹配任何文件，因此它可以作为可读性的分隔符；

以 `#` 开头的行是注释；

斜线 `/` 用作目录分隔符；

`*` 匹配除斜杆以外的任何内容；

`?` 匹配除斜杆以外的任何一个字符；

`**` 与完整路径名匹配的模式中的两个连续星号具有特殊含义:

- 前导 `**` 后跟斜杠表示在所有目录中匹配；
- 尾随的 `/**` 匹配里面的所有内容；
- 斜杆后跟两个连续的星号，然后斜杠匹配零个或者多个目录；
- 其他连续的星号被视为常规星号，将根据之前的规则进行匹配；

`!` 表示不忽略匹配到的文件或者目录；

### 配置文件示例

```shell
# 忽略所有目录下文件名为a的任何文件
a.*

# 忽略所有目录下后缀为txt的文件
*.txt

# 不忽略所有目录下的c.txt文件
!c.txt

# 忽略file目录下所有文件，包括其他目录下的file目录下的文件
# 例如 a/file下面的文件会被忽略
file/

# 只忽略当前file目录下所有文件，不包括其他目录下的file目录下的文件
# 例如 a/file下面的文件不会被忽略
/file

# 忽略任何位置file目录下的所有文件
file

# 忽略任何位置file目录下的所有文件 同file
**/file

# 忽略任何直接位于c目录下的file目录下的所有文件
**/c/file

# 同file/
file/**

# 忽略a/file，a/c/file，a/c/d/file 目录下的所有文件
a/**/file
```

> 当 `.gitignore` 配置发生冲突时，后面的配置会覆盖前面的配置

### .gitignore 不生效的解决方案

```shell
git rm -r --cached .

git add .

git commit -m "update .gitignore"
```

## 参考资料

- [Git 常用操作总结](https://juejin.cn/post/6844903586120335367 "https://juejin.cn/post/6844903586120335367")
- [字节研发设施下的 Git 工作流](https://juejin.cn/post/6875874533228838925 "https://juejin.cn/post/6875874533228838925")
- [进公司不会用 Git 拉项目！第二天被开除？天！那还在等什么，快点进来看看](https://juejin.cn/post/6951219502319140878#heading-15 "https://juejin.cn/post/6951219502319140878#heading-15")
