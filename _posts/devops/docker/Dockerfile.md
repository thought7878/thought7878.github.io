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
