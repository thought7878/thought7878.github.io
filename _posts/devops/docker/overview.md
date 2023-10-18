

![](assets/overview/2023-10-18-13-58-26-image.png)

## 镜像image

类比虚拟机中的iso/img文件

## 容器

类比正在运行中的虚拟机。类似于一个轻量级的沙盒，可以将其看作一个极简的Linux系统环境。Docker引擎利用容器来运行、隔离各个应用。容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响。

## Dockerfile

相当于配置文件，内容是“如何构建image”

## 仓库repository

远程的仓库，保存了很多image，包括官方的image/第三方的image。是Docker用来集中存放镜像文件的地方。<u>注意与注册服务器（Registry）的区别：</u>注册服务器是存放仓库的地方，一般会有多个仓库；而仓库是存放镜像的地方，一般每个仓库存放一类镜像，每个镜像利用tag进行区分，比如Ubuntu仓库存放有多个版本（12.04、14.04等）的Ubuntu镜像。

## tar文件

把image save成一个tar文件，别人可以通过tar文件load成image

## 参考

[【docker入门】10分钟，快速学会docker](https://www.bilibili.com/video/BV1R4411F7t9/?spm_id_from=333.788.top_right_bar_window_history.content.click&vd_source=22af953ea4c09540ad1966711a2d53f0)


