在shell中配置git命令的快捷方式有几种方法，我来为你详细介绍：

## 1. 使用Git内置的Alias功能

这是最常见的方式，通过git配置文件设置别名：

```shell
# 基本命令缩写
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

这样设置后，你可以使用`git co`代替`git checkout`，`git br`代替`git branch`等。

## 2. 在Shell配置文件中设置Alias

你也可以在shell配置文件中设置别名，这样可以直接使用更短的命令：

```bash
# 对于bash用户，编辑 ~/.bashrc 或 ~/.bash_profile
# 对于zsh用户，编辑 ~/.zshrc

# 添加以下别名
alias gco='git checkout'
alias gbr='git branch'
alias gci='git commit'
alias gst='git status'
alias ga='git add'
alias gp='git push'
alias gl='git log'
```

设置完成后，运行`source ~/.bashrc`（或相应的配置文件）使配置生效。

## 3. 高级Git Alias配置

你可以在git alias中集成shell命令，通过在alias前加`!`符号：

```bash
# 显示当前仓库路径
git config --global alias.root '!pwd'

# 显示当前分支
git config --global alias.current '!git rev-parse --abbrev-ref HEAD'

# 一键提交并推送
git config --global alias.save '!git add . && git commit -m "save" && git push'
```

这样git会将命令传递给shell执行。

## 4. 推荐的常用快捷方式配置

```shell
# 编辑你的shell配置文件
vi ~/.zshrc  # 或 ~/.bashrc

# 添加以下常用git别名
alias ga='git add'
alias gap='git add -p'
alias gb='git branch'
alias gbd='git branch -d'
alias gc='git commit -v'
alias gca='git commit -v -a'
alias gco='git checkout'
alias gcm='git checkout master'
alias gd='git diff'
alias gdc='git diff --cached'
alias gl='git log'

#alias glg='git log --graph --pretty=format:"%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)\<\%an>%Creset" --abbrev-commit'

alias gm='git merge'
alias gp='git push'
alias gpl='git pull'
alias gr='git remote'
alias grv='git remote -v'
alias gs='git status -sb'
alias gst='git status'

# 一些实用函数
gac() {
    git add . && git commit -m "$1"
}
```

## 5. 配置完成后

保存配置文件后，运行以下命令使配置生效：
```bash
source ~/.zshrc  # 或 source ~/.bashrc
```

这样你就可以使用这些快捷命令了。

通过合理配置这些别名，可以大大提高你的git使用效率，让日常操作变得更加快捷。