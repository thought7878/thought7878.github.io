# Git 和 GitHub 配置完整指南

## 1. 基础配置

首先配置Git的用户名和邮箱：
```bash
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱@example.com"
```

## 2. SSH密钥配置（推荐）

### 2.1 生成SSH密钥
```bash
ssh-keygen -t ed25519 -C "你的邮箱@example.com"
```
按回车使用默认设置，可以设置密码增强安全性。

### 2.2 启动ssh-agent并添加私钥
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 2.3 复制公钥内容
```bash
cat ~/.ssh/id_ed25519.pub
```
复制输出的全部内容。

### 2.4 在GitHub添加SSH密钥
1. 登录GitHub，点击右上角头像
2. 进入 Settings → Access → SSH and GPG keys
3. 点击 "New SSH key"
4. 输入标题（如"我的Mac"），粘贴刚才复制的公钥内容
5. 点击 "Add SSH key" 保存 

## 3. 验证配置

```bash
ssh -T git@github.com
```
如果看到成功消息，说明SSH配置成功。

## 4. 配置Git仓库

### 4.1 克隆仓库（使用SSH）
```bash
git clone git@github.com:用户名/仓库名.git
```

### 4.2 添加远程仓库
```bash
git remote add origin git@github.com:用户名/仓库名.git
```

### 4.3 推送代码
```bash
git push -u origin main
```

## 5. 常用快捷命令配置

在`~/.gitconfig`文件中添加别名：
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

## 验证配置
```bash
git config --list
ssh -T git@github.com
```

这样就完成了Git和GitHub的基本配置，可以开始使用SSH方式安全地推送和拉取代码了。