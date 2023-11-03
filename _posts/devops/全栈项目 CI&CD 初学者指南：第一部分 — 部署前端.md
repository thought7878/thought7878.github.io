![](https://miro.medium.com/v2/resize:fit:1200/1*23Fh1tq2pofQMaitepMjtg.png)

In this tutorial we will be focusing on CI/CD of a Full Stack Project (Backend, Frontend and Database) where CICD stands for Continuous Integration and Continuous Deployment.  
在本教程中，我们将重点关注全栈项目（后端、前端和数据库）的 CI/CD，其中 CICD 代表持续集成和持续部署。

What that means is, you create a full process for your project where when you push your code to a repository, that project will be build and be deployed live. So, you will be able to write your code and see how will it be when deployed, continuously. We will use local deployment for now, which is in everyway similar once you get terminal of the server.  
这意味着，您为项​​目创建一个完整的流程，当您将代码推送到存储库时，该项目将被实时构建和部署。因此，您将能够编写代码并持续查看部署后的情况。我们现在将使用本地部署，一旦您获得服务器终端，这在各方面都是相似的。

What will we be learning and using:  
我们将学习和使用什么：  
1. **Github Actions Workflows**: For creating routine jobs that will be executed on pushing code to repository.  

1. Github Actions 工作流程：用于创建将在将代码推送到存储库时执行的例行作业。  
   2. **Docker and Docker Hub Registry:** For building projects dockerized applications and keeping them in centralized registry.  
2. Docker 和 Docker Hub 注册表：用于构建项目 Docker 化应用程序并将其保存在集中注册表中。  
   3. **Continuous Deployment:** Since, this is simple tutorial for beginner, we will not be using Kubernetes for container management, rather we will use another docker container(watchtower) which will help us continuously watch changes in Docker registry for newly pushed containers.  
   3.持续部署：由于这是针对初学者的简单教程，因此我们不会使用 Kubernetes 进行容器管理，而是使用另一个 docker 容器（watchtower），它将帮助我们持续监视 Docker 注册表中新推送的容器的变化。

Following are some prerequisite steps:  
以下是一些先决步骤：  

1. Backend and Frontend integrated locally running JS or TS projects. Can be pulled from the following provided repos. (Since, we are focusing on CICD so we will be learning that process instead of development side)  
   1.后端和前端集成本地运行的JS或TS项目。可以从以下提供的存储库中提取。 （因为我们专注于 CICD，所以我们将学习该过程而不是开发方面）  
2. Some Linux knowledge 2.一些Linux知识

Lets start our CICD from here. Pull Repo from [here](https://github.com/sp-sandeshpokhrel/Frontend_for_fullstack_CICD_Tutorial.git).  
让我们从这里开始我们的 CICD。从这里拉回购。

1. **Github Action Workflows Github 操作工作流程  
   **GitHub Action workflows are a powerful automation tool provided by GitHub. They allow you to create a sequence of steps that can be automatically executed whenever certain events occur in your code repository.  
   GitHub Action 工作流程是 GitHub 提供的功能强大的自动化工具。它们允许您创建一系列步骤，只要代码存储库中发生某些事件，这些步骤就可以自动执行。  
   In simple terms, these are tasks like building your code with env variables that are required for the project. And these tasks run on standard platforms i.e. Ubuntu, provided by github which you can specify in your workflow file.  
   简而言之，这些任务例如使用项目所需的环境变量构建代码。这些任务在标准平台（即 Ubuntu）上运行，由 github 提供，您可以在工作流程文件中指定。  
   We need to create .github folder in root of the project and create workflows folder inside it. Now you can create any file inside workflows with .yaml extension. Since we have 2 projects frontend and backend, so let’s see github workflow file for frontend.  
   我们需要在项目的根目录中创建 .github 文件夹，并在其中创建工作流文件夹。现在，您可以在工作流程中创建任何具有 .yaml 扩展名的文件。由于我们有 2 个项目前端和后端，所以让我们看看前端的 github 工作流程文件。

name: Frontend CI/CD  
on:  
  push:  
    branches:  
      - "main"  

env:  
  CI: false  
  env: ${{ secrets.ENV }}  

jobs:  
  deploy:  
    name: build && push image to Docker hub  
    runs-on: ubuntu-latest  
    steps:  
      - uses: actions/checkout@v4  
        with:  
          ref: main  

      - name: import env file  
        run: |  
          touch ./.env && echo "$env" >> ./.env  
    
      - name: Set up Docker Buildx  
        uses: docker/setup-buildx-action@v2  
    
      - name: docker login  
        uses: docker/login-action@v2  
        with:  
          username: ${{ secrets.DOCKER_USERNAME }}  
          password: ${{ secrets.DOCKER_TOKEN }}  
    
      - name: build && push  
        id: build-push  
        uses: docker/build-push-action@v3  
        with:  
          context: .  
          file: Dockerfile  
          push: true  
          tags: fullstackcicd/tutorial-frontend:main

Let’s explain what does the above file do:  
我们来解释一下上面的文件做了什么：

1. `name: Frontend CI/CD`: This is a user-defined name for your GitHub Action workflow. It's a label that helps you identify what this workflow does.  
   `name: Frontend CI/CD` ：这是 GitHub Action 工作流程的用户定义名称。它是一个标签，可帮助您识别此工作流程的作用。
2. `on`: This section defines when the workflow should be triggered.  
   `on` ：此部分定义何时应触发工作流。
- `push`: This workflow is triggered when code is pushed to the repository.  
  `push` ：当代码推送到存储库时会触发此工作流程。
- `branches`: It specifies that this workflow will run when changes are pushed to the 'main' branch.  
  `branches` ：它指定当更改被推送到“主”分支时将运行此工作流。
- `pull_request`: This workflow will also run when pull requests are made to the 'main' branch.  
  `pull_request` ：当向“主”分支发出拉取请求时，此工作流程也会运行。

3. `env`: This section allows you to set environment variables.  
3. `env` ：此部分允许您设置环境变量。

- `CI: false`: Sets the 'CI' environment variable to 'false'.  
  `CI: false` ：将“CI”环境变量设置为“false”。
- `env: ${{ secrets.ENV }}`: It uses a secret called 'ENV' to set the 'env' variable. Secrets are sensitive information that should not be exposed. The ENV are retrieved from github repo setting’s Secrets and Variables and inside actions. You can add ENV in new repository secret.  
  `env: ${{ secrets.ENV }}` ：它使用名为“ENV”的秘密来设置“env”变量。秘密是不应公开的敏感信息。 ENV 是从 github 存储库设置的 Secrets 和 Variables 以及内部操作中检索的。您可以在新的存储库机密中添加 ENV。

4. `jobs`: This section defines the jobs that will be executed in this workflow. Each job has a name, and in this case, there's only one job named 'build.'  
4. `jobs` ：此部分定义将在此工作流程中执行的作业。每个作业都有一个名称，在本例中，只有一个名为“build”的作业。

`runs-on: ubuntu-latest`: This specifies the type of machine the job will run on. In this case, it's an Ubuntu-based machine.  
`runs-on: ubuntu-latest` ：指定作业将在其上运行的计算机类型。在本例中，它是基于 Ubuntu 的计算机。

5. `steps`: These are the individual tasks that make up the 'build' job. They are executed one after the other.  
5. `steps` ：这些是构成“构建”作业的单独任务。他们相继被处决。

- `actions/checkout@v4`: This step checks out the code from the 'main' branch.  
  `actions/checkout@v4` ：此步骤检查“main”分支中的代码。
- `import env file`: It creates an environment file (.env) and adds the value of the 'env' secret to it.  
  `import env file` ：它创建一个环境文件 (.env) 并向其中添加“env”秘密的值。
- `Set up Docker Buildx`: Prepares the environment for Docker builds.  
  `Set up Docker Buildx` ：为 Docker 构建准备环境。
- `docker login`: Logs into Docker Hub using the provided credentials from secrets. These credentials are also pulled from github repo setting. Create 2 new repository secrets with name DOCKER_USERNAME and DOCKER_TOKEN. These are provided by docker hub after registering. TOKEN is nothing but the password.  
  `docker login` ：使用 Secret 提供的凭据登录 Docker Hub。这些凭证也是从 github 存储库设置中提取的。创建 2 个名为 DOCKER_USERNAME 和 DOCKER_TOKEN 的新存储库机密。这些是注册后由 docker hub 提供的。 TOKEN只不过是密码。
- `build && push`: This step builds a Docker image from the code and pushes it to Docker Hub. It sets various parameters for the build, like the context, Dockerfile, and tags.  
  `build && push` ：此步骤从代码构建 Docker 映像并将其推送到 Docker Hub。它为构建设置各种参数，例如上下文、Dockerfile 和标签。

Overall this workflow file builds the code and pushes to your docker hub registry. For now use public mode on docker hub, since it is provided for free. If you need private dockerized images then you need to use private docker hub repository.  
总的来说，这个工作流程文件构建代码并推送到您的 docker hub 注册表。现在在 docker hub 上使用公共模式，因为它是免费提供的。如果您需要私有 dockerized 镜像，那么您需要使用私有 docker hub 存储库。

2. **Docker and Docker Hub Registry**  
2. Docker 和 Docker Hub 注册表

Now let’s go over how Dockerfile in the root directory which helps to dockerize the app.  
现在让我们回顾一下根目录中的 Dockerfile 如何帮助应用程序 Docker 化。

FROM node:18-alpine3.17 AS builder  
WORKDIR /usr/src/app  
COPY . .  
RUN yarn  
RUN yarn build  

FROM node:18-alpine3.17 AS prod  
WORKDIR /usr/src/app  
COPY --from=builder /usr/src/app/.next ./.next  
COPY ./package.json ./  
COPY --from=builder /usr/src/app/node_modules ./node_modules  
CMD [ "yarn", "start" ]

1. `FROM node:18-alpine3.17 AS builder`: This sets the base image for the Docker image. It uses Node.js version 18 on Alpine Linux 3.17. The `AS builder` part gives this image a name or alias, which can be referenced later in the Dockerfile.  
   `FROM node:18-alpine3.17 AS builder` ：这设置 Docker 映像的基础映像。它在 Alpine Linux 3.17 上使用 Node.js 版本 18。 `AS builder` 部分为该镜像提供了一个名称或别名，稍后可以在 Dockerfile 中引用。
2. `WORKDIR /usr/src/app`: This sets the working directory inside the Docker container to `/usr/src/app`. This is where the following commands will be executed.  
   `WORKDIR /usr/src/app` ：这会将 Docker 容器内的工作目录设置为 `/usr/src/app` 。这是将执行以下命令的地方。
3. `COPY . .`: This line copies all the files and directories from your local project directory (where the Docker build is initiated) into the current working directory within the Docker container.  
   `COPY . .` ：此行将本地项目目录（启动 Docker 构建的位置）中的所有文件和目录复制到 Docker 容器内的当前工作目录中。
4. `RUN yarn`: This command installs project dependencies using Yarn package manager. It uses the files you copied in the previous step to resolve and install the required packages.  
   `RUN yarn` ：此命令使用 Yarn 包管理器安装项目依赖项。它使用您在上一步中复制的文件来解析并安装所需的包。
5. `RUN yarn build`: This command builds your Nextjs application. It runs a specific Yarn script called `build`used for building the next app available in package.json file.  
   `RUN yarn build` ：此命令构建您的 Nextjs 应用程序。它运行一个名为 `build` 的特定 Yarn 脚本，用于构建 package.json 文件中的下一个可用应用程序。
6. `FROM node:18-alpine3.17 AS prod`: This starts a new stage in the Dockerfile, using the same base image (Node.js 18 on Alpine Linux 3.17) and gives it the name `prod`. Stages allow you to create multiple images from a single Dockerfile.  
   `FROM node:18-alpine3.17 AS prod` ：这会在 Dockerfile 中启动一个新阶段，使用相同的基础映像（Alpine Linux 3.17 上的 Node.js 18），并将其命名为 `prod` 。 Stages 允许您从单个 Dockerfile 创建多个映像。
7. `WORKDIR /usr/src/app`: Similar to before, this sets the working directory for this new stage.  
   `WORKDIR /usr/src/app` ：与之前类似，这设置了这个新阶段的工作目录。
8. `COPY --from=builder /usr/src/app/.next ./.next`: This line copies the contents of the `.next` directory from the `builder` stage (the first stage) into the `.next` directory in the current stage. The `.next` directory typically contains the build output of a Next.js application.  
   `COPY --from=builder /usr/src/app/.next ./.next` ：该行将 `.next` 目录的内容从 `builder` 阶段（第一阶段）复制到 `.next` 目录中当前阶段。 `.next` 目录通常包含 Next.js 应用程序的构建输出。
9. `COPY ./package.json ./`: This copies the `package.json` file from your local project directory to the current working directory in the Docker container.  
   `COPY ./package.json ./` ：这会将 `package.json` 文件从本地项目目录复制到 Docker 容器中的当前工作目录。
10. `COPY --from=builder /usr/src/app/node_modules ./node_modules`: This line copies the `node_modules` directory from the `builder` stage into the current stage. This is done to include the installed dependencies in the final image.  
    `COPY --from=builder /usr/src/app/node_modules ./node_modules` ：此行将 `node_modules` 目录从 `builder` 阶段复制到当前阶段。这样做是为了将安装的依赖项包含在最终映像中。
11. `CMD [ "yarn", "start" ]`: This specifies the command to run when a container is started from the image. In this case, it runs the `yarn start` command, which likely starts your Node.js application.  
    `CMD [ "yarn", "start" ]` ：指定从映像启动容器时要运行的命令。在本例中，它运行 `yarn start` 命令，这可能会启动您的 Node.js 应用程序。

In summary, this Dockerfile creates two stages: one for building the application and another for the production image. It copies the necessary files and dependencies, builds the application, and sets up the final image to run the production version of the Node.js application. The use of stages helps keep the final image smaller by not including unnecessary build artifacts.  
总之，此 Dockerfile 创建了两个阶段：一个用于构建应用程序，另一个用于生产映像。它复制必要的文件和依赖项，构建应用程序，并设置最终映像以运行 Node.js 应用程序的生产版本。阶段的使用不包括不必要的构建工件，有助于保持最终图像更小。

3**. Continuous Deployment** 3.持续部署

For continuous deployment, we need to watch the docker hub registry for new images continuously. For that we will utilize [Usage overview — Watchtower (containrrr.dev)](https://containrrr.dev/watchtower/usage-overview/) image. This docker container will continuously watch for changes in docker registry and if detects any change then pulls the image, stops the current one and deploys the new one. You will need following things setup in your PC.  
为了持续部署，我们需要持续观察 docker hub 注册表中的新镜像。为此，我们将利用使用概述 — Watchtower (containrrr.dev) 图像。该 docker 容器将持续监视 docker 注册表中的更改，如果检测到任何更改，则拉取映像、停止当前映像并部署新映像。您需要在电脑中进行以下设置。

1. **Linux terminal running Linux终端运行**
2. **Docker installed Docker已安装**

Create a new directory named ‘*deploy*’, we will currently be deploying frontend only and we will deploy full stack in next part.  
创建一个名为“deploy”的新目录，我们当前将仅部署前端，我们将在下一部分中部署完整堆栈。

Create docker compose file in the directory, named docker-compose.yml.  
在目录中创建docker compose文件，命名为docker-compose.yml。

version: "3"  
services:  
  watchtower:  
    image: containrrr/watchtower  
    volumes:  
      - /var/run/docker.sock:/var/run/docker.sock  
    command: --interval 30  

  frontend:  
    image: fullstackcicd/tutorial-frontend:main  
    volumes:  
      - .env:/usr/src/app/.env  
    ports:  
      - 80:3000

`version: "3"`: This specifies the version of the Docker Compose file format. In this case, it's version 3, which indicates the syntax and features available in this Compose file.  
`version: "3"` ：指定 Docker Compose 文件格式的版本。在本例中，它是版本 3，指示此 Compose 文件中可用的语法和功能。

`watchtower`: This service definition starts with the name of the service, "watchtower." It uses the official `containrrr/watchtower` image, which is a tool for automatically updating other Docker containers. It also specifies the following:  
`watchtower` ：此服务定义以服务名称“watchtower”开头。它使用官方的 `containrrr/watchtower` 镜像，这是一个自动更新其他Docker容器的工具。它还指定了以下内容：

- `volumes`: It binds the Docker socket from the host to the container to allow Watchtower to monitor other containers.  
  `volumes` ：它将 Docker 套接字从主机绑定到容器，以允许 Watchtower 监视其他容器。
- `command: --interval 30`: Watchtower is configured to check for updates in docker hub every 30 seconds.  
  `command: --interval 30` ：Watchtower 配置为每 30 秒检查 docker hub 中的更新。

`frontend`: This is another service definition named "frontend." It uses the `tutorial/frontend:main` Docker image, which presumably contains your frontend application. It also specifies the following:  
`frontend` ：这是另一个名为“frontend”的服务定义。它使用 `tutorial/frontend:main` Docker 映像，其中可能包含您的前端应用程序。它还指定了以下内容：

- `volumes`: It binds the local `.env` file to the container's `/usr/src/app/.env` path. This is used for providing environment variables to the frontend application.  
  `volumes` ：它将本地 `.env` 文件绑定到容器的 `/usr/src/app/.env` 路径。这用于向前端应用程序提供环境变量。
- `ports`: It maps port 80 on the host to port 3000 in the container, allowing you to access your frontend application via port 80 on your host machine.  
  `ports` ：它将主机上的端口 80 映射到容器中的端口 3000，允许您通过主机上的端口 80 访问前端应用程序。

In summary, this Docker Compose file defines two services: “watchtower” and “frontend.” “Watchtower” is used to automatically update other containers, and “frontend” runs your frontend application, allowing you to access it on port 80 of your host machine. The Docker Compose version is set to “3,” indicating the format used in this Compose file.  
总之，这个 Docker Compose 文件定义了两个服务：“watchtower”和“frontend”。 “Watchtower”用于自动更新其他容器，“frontend”运行您的前端应用程序，允许您在主机的端口 80 上访问它。 Docker Compose 版本设置为“3”，表示此 Compose 文件中使用的格式。

Now run following command:  
现在运行以下命令：

docker-compose up -d

This will run your docker compose file, which means it will start watchtower container and your frontend app. And after the containers are up, try opening localhost:80 which will map to your app.  
这将运行您的 docker compose 文件，这意味着它将启动 watchtower 容器和您的前端应用程序。容器启动后，尝试打开 localhost:80 ，它将映射到您的应用程序。

Now try changing some code in your frontend and push to the main branch and you will see changes reflected in the deployed app.  
现在尝试更改前端中的一些代码并推送到主分支，您将看到已部署的应用程序中反映的更改。

We will go over to the backend and database cicd in next part.  
我们将在下一部分讨论后端和数据库 CICD。

# Stackademic 堆栈学术

*Thank you for reading until the end. Before you go:  
感谢您阅读到最后。你走之前：*

- *Please consider* ***clapping*** *and* ***following*** *the writer! 👏*  
  请考虑鼓掌并关注作者！ 👏
- *Follow us on* [***Twitter(X)***](https://twitter.com/stackademichq)*,* [***LinkedIn***](https://www.linkedin.com/company/stackademic)*, and* [***YouTube***](https://www.youtube.com/c/stackademic)***.***  
  在 Twitter(X)、LinkedIn 和 YouTube 上关注我们。
- *Visit* [***Stackademic.com***](http://stackademic.com/) *to find out more about how we are democratizing free programming education around the world.*  
  请访问 Stackademic.com，详细了解我们如何在全球范围内实现免费编程教育的民主化。
