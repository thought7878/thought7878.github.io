# install jenkins 通过 docker

## server 安装 docker

[Install Docker Engine on Ubuntu | Docker Docs](https://docs.docker.com/engine/install/ubuntu/#installation-methods)

## 安装 jenkins

### 拉取 image

下载 Jenkins 的 Docker 镜像，执行以下命令：

```shell
docker pull jenkins/jenkins
```

### 创建容器

```shell
docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins
```

解释一下上述命令中的参数：

- -d：将容器放到后台运行；

- -p 8080:8080：将容器的 8080 端口映射到主机的 8080 端口，用于访问 Jenkins 的 Web 界面；

- -p 50000:50000：将容器的 50000 端口映射到主机的 50000 端口，用于 Jenkins 的代理节点通信；

- -v jenkins_home:/var/jenkins_home：将主机上的 jenkins_home 目录挂载到容器的/var/jenkins_home 目录，用于持久化 Jenkins 的数据。

### 安装的问题："STATUS : Exited (137)"

使用 512MB RAM 的服务器，会遇到"STATUS : Exited (137)"这个问题。

- 原因：

内存不够用的问题，[当进程因使用过多内存而终止时，会出现退出代码 137](https://www.airplane.dev/blog/exit-code-137)

### 配置 Jenkins

在容器创建完成后，打开浏览器，访问 http://localhost:8080，这是 Jenkins 的 Web 界面。

首次访问时，会要求输入初始管理员密码。可以通过以下命令从容器内获取该密码：

```shell
docker exec <容器ID> cat /var/jenkins_home/secrets/initialAdminPassword
```

输入初始管理员密码后，按照引导完成 Jenkins 的配置。
