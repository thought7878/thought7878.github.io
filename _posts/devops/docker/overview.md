![](assets/overview/2023-10-18-13-58-26-image.png)

## 镜像image

类比虚拟机中的iso/img文件。

### 查看镜像

查看所有本机上的镜像

```shell
docker images

#可选项
-a, --all    #列出所有镜像
-q, --quiet    #只显示镜像的id
```

### 搜索镜像

```shell
docker search mysql

# 可选项，搜索过滤  
--filter=STARTS=3000    # 过滤星数不少于3000的

docker search mysql --filter=STATRS=3000
```

### 下载镜像

```shell
# docker pull 镜像名[:tag]
# 如果不写tag,默认就是latest(最新版)

# 指定版本下载
docker pull mysql:5.7    # 必须官网上有
```

### 删除镜像

```shell
docker rmi -f 容器id #删除指定的容器
docker rmi -f 容器id容器id容器id容器id # 删除多个容器
docker rmi -f $(docker images -aq) # 删除全部的容器
```

## 容器

类比正在运行中的虚拟机。类似于一个轻量级的沙盒，可以将其看作一个极简的Linux系统环境。Docker引擎利用容器来运行、隔离各个应用。容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响。

镜像与容器的关系，类似于代码与进程的关系。

- `docker run` 创建容器
- `docker stop` 停止容器
- `docker rm` 删除容器

### 新建容器并启动

使用 `docker run` 来启动容器

```shell
docker run -d --name nginx -p 8888:80 nginx:alpine
```

- `-d`: 启动一个 `daemon` 进程
- `--name`: 为容器指定名称
- `-p host-port:container-port`: 宿主机与容器端口映射，方便容器对外提供服务
- `nginx:alpine`: 基于该镜像创建容器
- --rm: 当容器停止运行时，自动删除容器

### 启动/重启/停止容器

```shell
docker start 容器id # 启动容器
docker restart 容器id # 重启容器
docker stop 容器id # 停止容器
docker kill 容器id # 强制停止当前容器
```

### 进入容器

进入容器环境中,修改一些配置

```shell
#我们通常容器都是使用后台方式运行的,需要进入容器,修改一些配置
# 方式一
docker exec -it nginx sh
docker exec -it 容器id /bin/bash
# 方式二
docker attach 容器id 

# docker exec 进入容器后开启一个新的终端,可以在里面操作(常用)
# docker attach 进入容器正在执行的终端,不会启动新的进程!
```

### 拷贝文件:容器/主机

```shell
# 从容器内拷贝文件到主机上
docker cp 容器id:容器内路径 主机路径
docker cp 容器id:/home/test.js /home

# 从主机内拷贝文件到容器里
docker cp 主机路径/文件名 容器名(或者id):容器路径
```

### 查看容器

`docker ps` 列出所有容器，列出当前正在运行的容器 -a #列出当前正在运行的容器+带出历史运行过的容器 -n=? #显示最近创建的容器 -q #只显示容器的编号

```shell
docker ps -aq
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                    NAMES
404e88f0d90c        nginx:alpine         "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes        0.0.0.0:8888->80/tcp     nginx
498e7d74fb4f        nginx:alpine         "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes        80/tcp                   lucid_mirzakhani
2ce10556dc8f        redis:4.0.6-alpine   "docker-entrypoint.s…"   2 months ago        Up 2 months         0.0.0.0:6379->6379/tcp   apolloserverstarter_redis_1
```

- -a #列出当前正在运行的容器+带出历史运行过的容器 

- -n=? #显示最近创建的容器 

- -q #只显示容器的编号

`docker port` 查看容器端口映射

```shell
docker port nginx
80/tcp -> 0.0.0.0:8888
```

`docker stats` 查看容器资源占用

```shell
docker stats nginx
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
404e88f0d90c        nginx               0.00%               1.395MiB / 1.796GiB   0.08%               632B / 1.27kB       0B / 0B             2
```

### 查看日志

```shell
docker logs

docker logs -f -t --tail 容器id，没有日志

docker logs --help # 查看帮助

# 显示日志
-tf        # 显示日志
--tail number # number要显示日志条鼓
# docker logs -tf --tail 10 dce7b86171bf
```

### 查看容器中的进程信息

```shell
docker top 容器id
```

### 查看镜像的元数据

```shell
docker inspect 容器id
```

### 退出容器

```shell
exit # 直接容器停止并退出  
Ctrl + P + Q # 容器不停止退出
```

### 删除容器

```shell
docker rm 容器id    # 删除指定的容器,不能删除正在运行的容器，如果要强制删除，需加 -f 参数
docker rm -f $(docker ps -aq)    # 删除所有的容器
docker ps -a -q|xargs docker rm # 删除所有的容器
```

### 命令总结

```shell
attach --> Attach to a running container # 当前shell下attach连接指定运行镜像
build --> Build an image from a Dockerfile #通过Dockerfile定制镜像
commit --> create a new image from a container changes #提交当前容器为新的镜修
cp --> Copy files/folders from the containers filesystem to the host path #从容器中拷贝指定文件或者目录到宿主机中
create --> Create a new container #创建一个新的容器,同run,但不启动容器
diff --> Inspect changes on a container's filesystem #查看docker容器变化
events --> Get real time events from the server #从 docker服务获取容器实时事件
exec --> Run a command in an existing container #在已存在的容器上运行命令
export --> stream the contents of a container as a tar archive #导出容器的内容流作为一个tar归档文件[对应import]
history --> show the history of an image #展示一个镜像形成历史
images --> List images #列出系统当前镜像
import --> Create a new filesystem image from the contents of a tarbal1 #从tar包中的内容创建一个新的文件系统映像[对应export]
info --> Display system-wide information #显示系统相关信息
inspect --> Return low-level information on a container #查看容器详细信息
kill --> kill a running container #kil1指定docker容器
load --> Load an image from a tar archive #从一个tar包中加载一个镜像[对应save]
login --> Register or Login to the docker registry server #注册或者登陆一个docker源服务器
logout --> Log out from a Docker registry server #从当前Docker registry退出
logs --> Fetch the logs of a container #输出当前容器日志信息
port --> Lookup the public-facing port which is NAT-ed to PRIVATE-PORT #查看映射端口对应的容器内部源端口
pause --> Pause all processes within a container #暂停容器
ps --> List containers #列出容器列表
pull --> Pull an image or a repository from the docker registry server #从docker镜像源服务器拉取指定镜像或者库镜像
push --> Push an image or a repository to thel docker registry server #推送指定镜像或者库镜像至docker源服务器
restart --> Restart a running container #重启运行的容器
rm --> Remove one or more containers #移除一个或者多个容器
rmi --> Remove one or more images #移除一个或多个镜像[无容器使用该镜像才可删除,否则需删除相关容器才可继续或-f 强制删除]
run --> Run a command in a new container #创建一个新的容器并运行一个命令
save --> Save an image to a tar archive #保存一个镜像为一个tar包[对应1oad]
search --> Search for an image on the Docker Hub #在docker hub 中搜索镜像
start --> Start a stopped containers #启动容器
stop --> Stop a running containers #停止容器
tag --> Tag an image into a repository #给源中镜像打标签
top --> Lookup the running processes of a container #查看容器中运行的进程信息
unpause --> Unpause a paused container #取消暂停容器
version --> Show the docker version information #查看docker版本号
wait --> B1ock until a container stops, then print its exit code #截取容器停止时的退出状态值
```

## Dockerfile

相当于配置文件，内容是“如何构建image”。

在使用 `docker` 部署自己应用时，往往需要自己构建镜像。`docker` 使用 `Dockerfile` 作为配置文件构建镜像，简单看一个 `node` 应用构建的 `dockerfile`

```dockerfile
# 选择一个体积小的镜像 (~5MB)
FROM node:14-alpine

# 设置为工作目录，以下 RUN/CMD 命令都是在工作目录中进行执行
WORKDIR /code

# 把代码置于镜像
ADD . /code

# 装包
RUN yarn

# 暴露3000端口
EXPOSE 3000

# 启动 Node Server
CMD npm start
```

### build：构建镜像

并不是所有的镜像都可以在镜像仓库中找到，另外我们也需要为我们自己的业务应用去构建镜像。

使用 `docker build` 构建镜像，**`docker build` 会使用当前目录的 `dockerfile` 构建镜像**，至于 `dockerfile` 的配置，参考下节。

```shell
# -t 指定标签
# -t node-base:10: 镜像以及版本号
# .: 指当前路径
docker build -t node-base:10 .

# 构建一个名为 simple-app 的镜像
# -t: "name:tag" 构建镜像名称，不加tag，默认是latest
# . 代表当前路径
docker build -t simple-app .

# git rev-parse --short HEAD: 列出当前仓库的 CommitId
# 也可将当前 Commit 作为镜像的 Tag
# 如果该前端项目使用 git tag 以及 package.json 中的 version 进行版本维护，也可将 version 作为生产环境镜像的 Tag
docker build -t simple-app:$(git rev-parse --short HEAD)
```

当构建镜像成功后可以使用 `docker push` 推送到镜像仓库

### WORKDIR

设置工作目录为code， RUN/CMD 命令都是在工作目录中进行执行

```dockerfile
WORKDIR /code
```

### FROM

基于一个旧有的镜像，格式如下

```dockerfile
FROM <image> [AS <name>]

# 在多阶段构建时会用到
FROM <image>[:<tag>] [AS <name>]
```

### ADD

把目录或者 url 地址文件，加入到镜像的文件系统中

```dockerfile
ADD [--chown=<user>:<group>] <src>... <dest>
```

### RUN

执行命令，由于 `ufs` 的文件系统，它会在当前镜像的顶层新增一层

```dockerfile
RUN <command>
```

### CMD

指定容器如何启动。指定这个容器启动的时候要运行的命令,只有最后一个会生效,可被替代。

**一个 `Dockerfile` 中只允许有一个 CMD**

```dockerfile
# exec form, this is the preferred form
CMD ["executable","param1","param2"] 

# as default parameters to ENTRYPOINT
CMD ["param1","param2"]

# shell form
CMD command param1 param2 
```

### 指令总结

- FROM # 基础镜镜像,一切从这里开始构建
- MAINTAINER # 镜像是谁写的,姓名+邮箱
- RUN #镜像构建的时候需要运行的命令
- ADD # 例如：步骤: tomcat镜像,这个tomcat压缩包!添加内容
- WORKDIR # 镜像的工作目录
- VOLUME # 挂载的目录
- EXPOSE # 保留端口配置
- CMD # 指定这个容器启动的时候要运行的命令,只有最后一个会生效,可被替代
- ENTRYPOINT # 指定这个容器启动的时候要运行的命令,可以追加命令
- ONBUILD # 当构建一个被继承DockerFile这个时候就会运行ONBUTLD 的指令,触发指令.
- COPY # 类似ADD ,将我们文件持贝到镜像中
- ENV # 构建的时候设置环境变量!

## 仓库repository

远程的仓库，保存了很多image，包括官方的image/第三方的image。是Docker用来集中存放镜像文件的地方。<u>注意与注册服务器（Registry）的区别：</u>注册服务器是存放仓库的地方，一般会有多个仓库；而仓库是存放镜像的地方，一般每个仓库存放一类镜像，每个镜像利用tag进行区分，比如Ubuntu仓库存放有多个版本（12.04、14.04等）的Ubuntu镜像。

## tar文件

把image save成一个tar文件，别人可以通过tar文件load成image

## 参考

[【docker入门】10分钟，快速学会docker](https://www.bilibili.com/video/BV1R4411F7t9/?spm_id_from=333.788.top_right_bar_window_history.content.click&vd_source=22af953ea4c09540ad1966711a2d53f0)
