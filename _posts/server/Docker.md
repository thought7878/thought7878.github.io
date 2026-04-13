[【小白debug的作品】Docker是什么？架构怎么样？和k8s是什么关系？](https://www.douyin.com/video/7471545072736046372)

# Docker 与 Kubernetes 核心概念解析

## Docker 是什么

`Docker` 是一款可以**将程序和环境打包并运行**的工具软件，**解决因环境不同导致程序无法运行的问题**。
`程序`依赖`操作系统`及其上的`依赖库`、`配置`等`环境`，**Docker 通过打包环境和程序，确保在不同服务器上一致运行**。

![[_posts/server/media/d630af9d10ca51717c3ada4023256da9_MD5.webp]]

![[_posts/server/media/5d2fdd44f15c1ea925332ad533f86f79_MD5.webp]]

![[_posts/server/media/9351ba957fee0699d857d5d912ecc6fb_MD5.webp]]

## 基础镜像是什么

`基础镜像`是**统一环境的基础，通过阉割操作系统**（仅保留*用户空间的文件系统和依赖库等*）**打包成的 “压缩包”**，如选择 CentOS 或 Ubuntu 的用户空间部分构建，**作为应用环境的底层**。

![[_posts/server/media/2be0d4c10b019353559392c46e7d67d6_MD5.webp]]

![[_posts/server/media/d21e11f205ca76e7a22455c5f6cd6e92_MD5.webp]]

## Dockerfile 是什么

`Dockerfile` 是**描述从基础镜像到应用启动所需步骤（需要做哪些事情）的清单文件**，**以命令行形式列出安装依赖**（如 yum install gcc）、**创建文件夹、运行程序等操作，类似任务清单（todo list）**。

![[_posts/server/media/0f5ac952cce331a253ce53ccf23e0fd5_MD5.webp]]

## 容器镜像是什么

`容器镜像`是**执行 docker build 命令后，按照 Dockerfile 构建的 “压缩包”，包含环境和程序**。
**将其传到任意服务器，“解压缩” 后即可运行，*解决跨服务器部署问题***。


## Registry 是什么

Registry 是管理容器镜像推拉的服务，类似 GitHub 代码仓库。通过 docker push 将镜像上传到 Registry（如官方 Docker Hub、清华大学 TUNA 或公司私有仓库），其他服务器用 docker pull 拉取镜像。

## 容器是什么

`容器`是执行 docker run 命令后，“解压缩” 容器镜像得到的独立运行环境和应用程序。多个容器可在同一操作系统上独立、隔离运行。

## 容器和虚拟机的关系

容器与传统虚拟机类似但不同：传统虚拟机自带完整操作系统，容器不含完整系统，仅包含核心依赖库和配置文件。容器通过 namespace 实现独立操作系统的假象，通过 cgroup 限制计算资源，本质是自带独立运行环境的特殊进程，共享宿主机内核。

## Docker 的架构原理

Docker 采用 client-server 架构，client 为 docker cli（命令行工具），server 为 docker daemon（守护进程）。docker cli 解析命令后调用 daemon 的 RESTful API，daemon 包含 docker server 和 engine 两层：docker server 是 HTTP 服务器，提供操作容器和镜像的 API 接口，接收请求后分发任务给 engine；engine 层创建 job 执行具体工作（如构建镜像、推拉镜像、运行容器）。

## 命令
### docker build 命令

执行 docker build 命令时，job 根据 Dockerfile 指令，像剥洋葱皮似的一层层构建容器镜像文件。

### docker pull 或 push 命令

执行 docker pull 或 push 命令时，job 与外部 Docker Registry 交互，完成镜像的下载或上传。

### docker run 命令

执行 docker run 命令时，基于容器镜像调用 containerd 组件及 runC 组件，创建并运行容器，将 “压缩包” 解压缩为独立环境和应用程序。

## docker-compose 是什么

docker-compose 通过 yaml 文件定义多容器部署信息（容器列表、启动顺序、CPU / 内存占用等），执行 docker-compose up 命令一键按顺序部署整套服务（如先启动数据库，再启动身份验证服务，最后启动 Web 服务）。

## docker-swarm 是什么

docker-swarm 解决多台服务器上整套服务的集群部署问题，可实现服务迁移（如服务器 A 故障后在 B 重新部署）、扩容和缩容。

## Kubernetes 和 docker 的关系

Kubernetes（k8s）与 docker-swarm 是竞品，均为容器编排引擎。docker 部署的容器即 k8s 中 pod 内的 container；docker-compose 创建的多容器服务对应 k8s 的 pod；k8s 通过调度 pod 在多台 node 服务器上实现部署、扩容和缩容，本质是以 API 编程方式管理容器。