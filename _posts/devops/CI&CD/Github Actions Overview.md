# 前言

这是一篇属于面向**前端**的关于`CICD`和`Github Action`的入门文章，其旨在：

1. 入门掌握`Github Action`的用法
2. 学习`CI`和`CD`的含义及其实现细节
3. 基于`Github Action`展示如何给自己手上的项目添加`CICD`的流程
4. 基于`CICD`流程去添加其他延伸机制，如回滚、端对端测试等

事不宜迟，直接开始本文的内容吧。

# 为什么要选择Github Action

本文选择`Github Action`不代表他是最好选择，但在我用过的**CICD 工具**中，他对**开源项目**的支持是最友好的(不过这个项目得放在`Github`，如果是放在`Gitee`就用不到了)。

> 公共仓库和自托管运行器免费使用 `GitHub Actions`。 对于私有仓库，每个 GitHub 帐户可获得一定数量的免费记录和存储，具体取决于帐户所使用的产品。 超出包含金额的任何使用量都由支出限制控制。

`Github Action`在**开源项目**是免费使用的，而在**私有项目**方面的计费会根据你购买的服务而不同，详细可看[关于 GitHub Actions 的计费](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Fbilling%2Fmanaging-billing-for-github-actions%2Fabout-billing-for-github-actions "https://docs.github.com/cn/billing/managing-billing-for-github-actions/about-billing-for-github-actions")。

而对于其他我用过的**CICD 工具**及其没被选为本文实现方式的原因如下所示：

- **`Gitlab CI`**：与`Gitlab`高度绑定，项目放在`Gitlab`就谈不上开源了
- **`Travic CI`**：限时免费，过后按进程收费
- **`Drone CI`**：执行任务时，国内机器从`Github`拉取仓库代码时会偶尔超时，从而导致任务失败
- **`Jenkins CI`**：除了存在与`Drone CI`一样的缺点外，自身比较重量，占用宿主机较多资源

# Github Action初学入门（熟悉的可跳过此章节）

当我们想往自己的项目里接入**Github Actions**时，要在根项目目录里新建`.github/workflows`目录。然后通过编写`yml`格式文件定义**Workflow(工作流程)**去实现`CI`。在阅读`yml`文件之前，我们要先搞懂在**Workflow**中一些比较重要的概念：

- **Event(触发事件)**：指触发 **Workflow(工作流程)** 运行的事件。
- **Job(作业)**：一个**工作流程**中包含一个或多个**Job**，这些**Job**默认情况下并行运行，但我们也可以通过设置让其按顺序执行。每个**Job**都在指定的环境(虚拟机或容器)里开启一个**Runner**(可以理解为一个进程)运行，包含多个**Step(步骤)**。
- **Step(步骤)**：**Job**的组成部分，用于定义每一部的工作内容。每个**Step**在运行器环境中以其单独的进程运行，且可以访问工作区和文件系统。

以下图的`Workflow`作为例子，我们可以更直观地看懂**Event**、**Job**以及**Step**两者的关系：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27deef91333747ab8f7e09bd2649b5bb~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

*在`Github Action`中， **Job** 和 **Step** 以及 **Workflow** 都有资源占用以及时间限制，超出限制就会直接取消运行，关于这些限制可看[Usage limits](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Factions%2Flearn-github-actions%2Fusage-limits-billing-and-administration%23usage-limits "https://docs.github.com/cn/actions/learn-github-actions/usage-limits-billing-and-administration#usage-limits")。*

下面我们用`Github`的官方教程中的一个`Workflow`例子来学习：

```yaml
# 指定工作流程的名称
name: learn-github-actions
# 指定此工作流程的触发事件Event。 此示例使用 推送 事件，即执行push后，触发该流水线的执行
on: [push]
# 存放 learn-github-actions 工作流程中的所有Job
jobs:
  # 指定一个Job的名称为check-bats-version
  check-bats-version:
    # 指定该Job在最新版本的 Ubuntu Linux 的 Runner(运行器)上运行
    runs-on: ubuntu-latest
    # 存放 check-bats-version 作业中的所有Step
    steps:
      # step-no.1: 运行actions/checkout@v3操作，操作一般用uses来调用，
      # 一般用于处理一些复杂又频繁的操作例如拉取分支，安装插件
      # 此处 actions/checkout 操作是从仓库拉取代码到Runner里的操作
      - uses: actions/checkout@v3
      # step-no.2: actions/setup-node@v3 操作来安装指定版本的 Node.js，此处指定安装的版本为v14
      - uses: actions/setup-node@v3
        with:
          node-version: "14"
      # step-no.3: 运行命令行下载bats依赖到全局环境中
      - run: npm install -g bats
      # step-no.4: 运行命令行查看bats依赖的版本
      - run: bats -v

```

整个`learn-github-actions`**工作流程**弄成流程图可如下所示：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf3432f09abe493cbc5a96a94c34a441~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

# 基础篇：来给一个项目添加`CICD`流程

## 初始化项目

为了让读者更多地专注于`CICD`的实现细节，这里我用`Vite`的[创建指令](https://link.juejin.cn?target=https%3A%2F%2Fvitejs.cn%2Fguide%2F%23scaffolding-your-first-vite-project "https://vitejs.cn/guide/#scaffolding-your-first-vite-project")简单新建一个项目。通过`yarn create vite cicd-study --template react-ts`创建一个技术栈为`Vite`、`React`、`Typescript`的前端项目，安装依赖后运行起来的页面效果如下所示：

![vite-react-ts.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/49744ea6dcd24fa2870900518f1371bf~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

读者也可以通过[stackblitz](https://link.juejin.cn?target=https%3A%2F%2Fstackblitz.com%2Fedit%2Fvitejs-vite-rfo4fe%3Ffile%3Dindex.html%26terminal%3Ddev "https://stackblitz.com/edit/vitejs-vite-rfo4fe?file=index.html&terminal=dev")查看项目的目录结构和文件代码。

接下来会在这个项目上添加`CI`和`CD`流程。完整项目地址可看[ciccd-study](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FHitotsubashi%2Fcicd-study "https://github.com/Hitotsubashi/cicd-study")。

## 添加`CI`流程

### `CI`的概念

**CI**的全称是**Continuous Integration**，直译为**可持续集成**，而普遍对其的解释是**频繁地（一天多次）将代码集成到主干**。对于这个解释我们要搞懂其中的两个概念：

1. **主干**：是指包含多个已上和即将上线的特性的分支。
2. **集成**：是指把含新特性的分支合并(`merge`)到**主干**上的行为

我们借`github flow`分支管理策略作为例子来更加深入了解`CI`及上面的两个概念：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e231ae5101154085bcb13b18443c5e2d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

`github flow`在开发新特性的运行模式如下所示：

1. 基于`master`创建新的分支`feature`进行开发。注意这需要保证`master`的代码和特性永远是最稳定的。
2. 开发期间，定期提交更改(`commit and push change`)到远程仓库的`feature`分支
3. 在编码以及自测完成后，通过创建`pull request`去对`master`发起合并`feature`的请求
4. `pull request`在经过审核确认可行后合并到`master`分支
5. 删除已合并的特性分支`feature`

更多详细细节可看[GitHub flow](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Fget-started%2Fquickstart%2Fgithub-flow "https://docs.github.com/cn/get-started/quickstart/github-flow")。

在`github Flow`模型中，**主干**指`master`分支，广义上是一个包含多个已上和即将上线的特性的分支；**集成**指的是在`pull request`通过后把特性分支`merge`合并到**主干**，也就是`master`分支上。

阮一峰老师的[持续集成是什么？](https://link.juejin.cn?target=https%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2015%2F09%2Fcontinuous-integration.html "https://www.ruanyifeng.com/blog/2015/09/continuous-integration.html")里说到过：

> 持续集成的目的，就是让产品可以快速迭代，同时还能保持高质量。它的核心措施是，代码集成到主干之前，必须通过自动化测试。只要有一个测试用例失败，就不能集成。

而`github flow`模型**保证高质量的核心措施**是：在**集成**前通过`pull request`，从而触发审核（审核可以是一系列的自动化校验测试以及代码审核**Code Review**），在审核通过后再合并到**主干**，从而保证**主干**的稳定性。

下面我们就按照`github flow`模型的机制，在开头创建的项目上添加`CI`流程。

### 在项目中实现`CI`

根据上面所说的`github flow`模型**保证高质量的核心措施**可知，我们要定义的执行`CI`的**Workflow**（下称**CI Workflow**）的**Event**是`master`分支的`pull request`事件。而`Job`和`Step`的话没具体说明，而我们可以把目前最普遍的 **代码测试（Test）** 和 **代码扫描（Lint）** 加入其中。

其实现思路是，首先要借助一些第三方插件，在`package.json`中的`scripts`定义可以执行**代码测试（Test）**和**代码扫描（Lint）**的命令，然后在把这些命令行加到**CI Workflow**的**Step**里。

具体流程图如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8cd2441838274ff39f63af4fccb134a4~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 代码扫描命令实现

一般公司里都会通过类似`Sonar`这类代码质量管理插件来保证代码质量。不过我们也可以通过前端样式三剑侠：`eslint`+`prettier`+`stylelint`来简单保证。这里我直接使用本人比较喜好和经常使用的`umi`的代码规范：[**@umijs/fabric**](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fumijs%2Ffabric "https://github.com/umijs/fabric")来规定三剑侠的规则了，使用方式如下所示：

**.eslintrc.js**

js

复制代码

`module.exports = {   extends: [require.resolve("@umijs/fabric/dist/eslint")], };`

**.prettierrc.js**

js

复制代码

`const fabric = require("@umijs/fabric"); module.exports = {   ...fabric.prettier, };`

**.stylelintrc.js**

js

复制代码

`const fabric = require("@umijs/fabric"); module.exports = {   ...fabric.stylelint, };`

然后在`package.json`的`script`上加上对应的执行命令即可：

json

复制代码

`"scripts": {   "dev": "vite",   "build": "tsc && vite build",   "preview": "vite preview",   "lint": "npm run lint:js && npm run lint:style && npm run lint:prettier",   "lint:js": "eslint --cache --ext .js,.jsx,.ts,.tsx ./src",   "lint:prettier": "prettier --check \"src/**/*\" --end-of-line auto",   "lint:style": "stylelint --fix 'src/**/*.{css,scss,less}' --cache" }`

这样子就完成了**代码扫描**部分了。通过`yarn run lint`执行后的效果如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed18411c0d2c41108d62ab0fd1716779~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 自动化测试命令实现

前端测试主要有**单元测试（Unit Test）**、**集成测试（Integration Test）**、**UI 测试（UI Test）**。由于项目里只有一个页面组件，且本章节的重点是实现`CI`而不是**前端自动化测试**，因此这里用**单元测试**来实现一下：

为了多写点测试用例给测试代码加点内容，我给页面对应组件`App.tsx`加了个`props`，代码如下所示：

**App.tsx**

tsx

复制代码

`import type { FC } from "react"; import { useState } from "react"; import logo from "./logo.svg"; import "./App.css"; interface Props {   value: string; } const App: FC<Props> = ({ value }) => {   const [count, setCount] = useState(0);   return (     <div className="App">       <header className="App-header">         <img src={logo} className="App-logo" alt="logo" />         <p>Hello Vite + React!!!!!!!!</p>         <p>           {/*             测试代码中需要获取的DOM元素用role属性标记，这里的role属性只会在测试代码中用到，             这样子就可以避免代码因需求改动时，因DOM属性改变导致测试不通过。有利于TDD（测试驱动开发）开发的进行           */}           <button             role="button"             type="button"             onClick={() => setCount((v) => v + 1)}           >             count is: {count}           </button>         </p>         <p role="props">{value}</p>       </header>     </div>   ); }; export default App;`

这里采用`ts-jest`+`@testing-library`来编写测试代码（当然对于`React`还有别的选择，例如`ts-jest`+`enzyme`），测试代码如下所示：

**App.test.tsx**

ts

复制代码

``import React from "react"; import { render, screen, fireEvent } from "@testing-library/react"; import App from "./App"; test("props is avaliable", () => {   const value = "123";   // 为了多写点测试用例，我给App组件加了个prop   render(<App value={value} />);   expect(screen.getByRole("props")).toHaveTextContent(value); }); test("click of button is avaliable", () => {   render(<App value="123" />);   fireEvent.click(screen.getByRole("button"));   expect(screen.getByRole("button")).toHaveTextContent(`count is: 1`); });``

`jest.config.js`的配置比较复杂，可以从[此处](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FHitotsubashi%2Fcicd-study%2Fblob%2Fmain%2Fjest.config.js "https://github.com/Hitotsubashi/cicd-study/blob/main/jest.config.js")查看。配置好后运行`yarn test`后控制台输出如下所示：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/916b1e12446b45f796f1cc376d635af1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 配置**CI Workflow**

在项目根目录里的`.github/workflows`文件夹上新建`ci.yml`，代码如下所示：

**ci.yml**

yml

复制代码

`name: CI # Event设置为main分支的pull request事件， # 这里的main分支相当于master分支，github项目新建是把main设置为默认分支，我懒得改了所以就保持这样吧 on:   pull_request:     branches: main jobs:   # 只需要定义一个job并命名为CI   CI:     runs-on: ubuntu-latest     steps:       # 拉取项目代码       - name: Checkout repository         uses: actions/checkout@v2       # 给当前环境下载node       - name: Use Node.js         uses: actions/setup-node@v3         with:           node-version: "16.x"       # 检查缓存       # 如果key命中缓存则直接将缓存的文件还原到 path 目录，从而减少流水线运行时间       # 若 key 没命中缓存时，在当前Job成功完成时将自动创建一个新缓存       - name: Cache         # 缓存命中结果会存储在steps.[id].outputs.cache-hit里，该变量在继后的step中可读         id: cache-dependencies         uses: actions/cache@v3         with:           # 缓存文件目录的路径           path: |             **/node_modules           # key中定义缓存标志位的生成方式。runner.OS指当前环境的系统。外加对yarn.lock内容生成哈希码作为key值，如果yarn.lock改变则代表依赖有变化。           # 这里用yarn.lock而不是package.json是因为package.json中还有version和description之类的描述项目但和依赖无关的属性           key: ${{runner.OS}}-${{hashFiles('**/yarn.lock')}}       # 安装依赖       - name: Installing Dependencies         # 如果缓存标志位没命中，则执行该step。否则就跳过该step         if: steps.cache-dependencies.outputs.cache-hit != 'true'         run: yarn install       # 运行代码扫描       - name: Running Lint         # 通过前面章节定义的命令行执行代码扫描         run: yarn lint       # 运行自动化测试       - name: Running Test         # 通过前面章节定义的命令行执行自动化测试         run: yarn test`

关于上面的`Cahe`步骤中，7 天内未被访问的任何缓存条目将会被删除。 可以存储的缓存数没有限制，但存储库中所有缓存的总大小限制为 10 GB。更多内容请看[缓存依赖项以加快工作流程](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Factions%2Fusing-workflows%2Fcaching-dependencies-to-speed-up-workflows "https://docs.github.com/cn/actions/using-workflows/caching-dependencies-to-speed-up-workflows")。

---

当创建`pull request`合并到主干时，**CI Workflow**触发运行，此时可以看到下面的情况：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da87fc3688394d11a1be7391721250e1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

当**CI Workflow**运行完成时，其效果如下所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f59410d312a4dc8aa517701f98a94b7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

我们可以通过点击`Details`查看执行详细信息，如下所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a907606f73e4894a13fc72d4f9bb731~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

也可以点开每个`step`查看控制台输出信息：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69118f7b049d4ec0ba3746493be1a653~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

确认代码安全可靠后就可以点击`Merge pull request`来把新代码**集成**到**主干**上。从而基于`CI`完成一次**bug 修复**或**新特性迭代**。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dcecb9fa1df148f291d0ce40fe88a5f6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

合并成功后，可以点击`Delete branch`以删除已合并的特性分支。

### 补充

部分`DevOps`平台中的`CI`流程比上面我们实现的流程里多一个阶段：**编译并整理产物**，即基于当前版本的代码打包构建产物。在这篇文章中我把这个阶段放在`CD`流程里。

## 添加`CD`流程

### `CD`的概念

`CD`指的是 **持续交付（Continuous delivery）** 或者 **持续部署（continuous deployment）** 或者是两者的并集，我们借用[AWS 中对持续交付说明](https://link.juejin.cn?target=https%3A%2F%2Faws.amazon.com%2Fcn%2Fdevops%2Fcontinuous-delivery%2F "https://aws.amazon.com/cn/devops/continuous-delivery/")来说明下这两者的解释，如下：

> ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/539a9328755340e69a1fa5d50abd3bb5~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?) 采用持续交付时，系统会构建并测试每一个代码变更，然后将其推送到非生产测试环境或临时环境中。生产部署前可能存在多个并行测试阶段。**持续交付与持续部署之间的区别在于，需要手动批准才能更新到生产环境。对于持续部署，生产会在没有明确批准的情况下自动发生。**

从上面的解释中可知其有三个步骤：

1. 生成制品
2. 自动部署到测试环境以校验其稳定性
3. 部署到生产环境（自动的是**持续部署**，手动的是**持续交付**）

基于本文是以入门为主，且很多读者也就只有一个服务器来直接部署自己的小项目，因此本章节的`CD`实现中，我们以**持续部署（continuous deployment）** 且跳过上面第二步来实现，也就是生成制品后直接自动部署到生产环境。

---

其实对于**持续交付（Continuous delivery）** 和 **持续部署（continuous deployment）** ，不同`DevOps`平台有不同的解释，而不同的企业和项目也有不同的实现方式。但本质上不会有太大区别，而我们也没必要去花时间咬文嚼字，借用**Red Hat**对 [**CICD**说明](https://link.juejin.cn?target=https%3A%2F%2Fwww.redhat.com%2Fzh%2Ftopics%2Fdevops%2Fwhat-is-ci-cd "https://www.redhat.com/zh/topics/devops/what-is-ci-cd") 里的一句话总结，如下：

> CI/CD 既可能仅指持续集成和持续交付构成的关联环节，也可以指持续集成、持续交付和持续部署这三项构成的关联环节。更为复杂的是，有时"持续交付"也包含了持续部署流程。
> 
> 归根结底，我们没必要纠结于这些语义，您只需记得 CI/CD 其实就是一个流程（通常形象地表述为管道），用于实现应用开发中的高度持续自动化和持续监控。因案例而异，该术语的具体含义取决于 CI/CD 管道的自动化程度。许多企业最开始先添加 CI，然后逐步实现交付和部署的自动化（例如作为云原生应用的一部分）。

### 在项目中实现`CD`

这是我们在本章节要实现的`CD`机制的流程图：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/34de1e458c6e4f5f9429ca6251272a77~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 先前准备

在编写**CD Workflow**前，我们要准备以下东西：

1. 内置`nginx`的服务器一台：用于部署制品
2. 服务器的密钥对：用于提供给流水线通过 ssh 免密登录到服务器进行部署
3. `Github`里的**Personal Access Token**：用于提供给流水线免密登录`github`账号进行发布制品的操作
4. 把步骤 2 和步骤 3 及其他关于机器的信息都放在对应仓库的**Secret**里

下面对上述需要准备的东西逐一讲解：

1. **准备一台服务器，在里面启动`nginx`服务。**
   
   读者可以直接通过`apt`下载`nginx`到指定目录后启动。我个人习惯以`docker`启动容器以开启`nginx`服务。因此我直接通过下面的`docker-compose.yml`去创建启动`nginx`容器：
   
   yml
   
   复制代码
   
   `# 指定docker-compose解析的版本 version: "3" services:   pure-nginx:     image: nginx:latest     # 指定容器名     container_name: pure-nginx     restart: always     # 指定持久卷，格式为 宿主机目录路径:容器目录路径     # CD Workflow会通过密钥登录该服务器，然后把生成的制品放在/data/www里，在此之后直接访问宿主机的ip即可访问到项目页面     volumes:       - /data/www:/usr/share/nginx/html     ports:       - 80:80`

2. **创建服务器的密钥对：用于提供给流水线通过 ssh 免密登录到服务器进行部署**
   
   每个平台都有创建密钥的教程，例如我的机器是腾讯云的，因此参考[这篇文章](https://link.juejin.cn?target=https%3A%2F%2Fcloud.tencent.com%2Fdocument%2Fproduct%2F1207%2F44573 "https://cloud.tencent.com/document/product/1207/44573")去创建密钥，密钥分公钥和私钥。公钥存放在服务器上，私钥我们自己下载保存。在配置`CD Workflow`的免密登录机器的**步骤 step**之前，大家也可以参考这篇文章[使用密钥登录](https://link.juejin.cn?target=https%3A%2F%2Fcloud.tencent.com%2Fdocument%2Fproduct%2F1207%2F44643 "https://cloud.tencent.com/document/product/1207/44643")，使用`VSCode`中的[Remote - SSH](https://link.juejin.cn?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Dms-vscode-remote.remote-ssh "https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh")通过配置私钥尝试是否可以免密登录机器，如果成功后就可以放心交给流水线去登录。

3. **创建`Github`里的 Personal Access Token ：用于提供给流水线免密登录`github`账号进行发布制品的操作**
   
   参考`Github`官方文档[Creating a personal access token](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Fauthentication%2Fkeeping-your-account-and-data-secure%2Fcreating-a-personal-access-token "https://docs.github.com/cn/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token")创建**Personal Access Token**。
   
   在上述文档里的第 8 步 **Select scopes** 时直接点击`repo`即可，其余的可不选，如下所示：
   
   ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/64beb416e2ef41beb21ea45994ff9dcc~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

4. **把步骤 2 和步骤 3 及其他关于机器的信息都放在`github`仓库的 Secret 里**
   
   **Secret**是一些相对机密重要的信息，这些信息在 **Workflow** 里面需要用到，但又不能以明文的形式直接写在文件里以免泄露。此时我们可以放在**Secret**里，在 **Workflow** 运行时这些**Secret**会以环境变量的形式注入到`Runner`里，此时可以以`${{ secrets.xxx }}`的形式读取。
   
   在如图所示的页面下点击右上角的`New repository secret`去创建`secret`，在接下来要创建的`CD Workflow`中需要用到如图红字标记所示的四个**Secret**。
   
   ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc7dfad31cd2485490a1170dd9548701~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 配置**CD Workflow**

这里我们把执行`CD`的**Workflow**（下称**CD Workflow**）的**Event**定义为`master`分支的`push`事件，因为**CD Workflow**的执行是在`Merge pull request`完成后的，而合并行为会触发**主干**的`push`事件。

接下来在`.github/workflows`里新建`cd.yml`来定义**CD Workflow**，代码如下所示：

yml

复制代码

`name: CD on:   # 以主干的push事件作为触发条件   push:     branches: main jobs:   CD:     runs-on: ubuntu-latest     steps:       # 拉取代码       - name: Checkout repository         uses: actions/checkout@v2       # 下载Node       - name: Use Node.js         uses: actions/setup-node@v3         with:           node-version: "16.x"       # 添加缓存，逻辑和CI Workflow里的一样       - name: Cache         id: cache-dependencies         uses: actions/cache@v3         with:           path: |             **/node_modules           key: ${{runner.OS}}-${{hashFiles('**/yarn.lock')}}       # 安装依赖。命中缓存则跳过此步       - name: Installing Dependencies         if: steps.cache-dependencies.outputs.cache-hit != 'true'         run: yarn install       # 从package.json里获取version属性的值       # 在CD Workflow中会给每个生成的制品打上标签，而标签取值于version值       - name: Read Version         # 读取出来的值会放在steps.[id].outputs.value供其他步骤step读取         id: version         uses: ashley-taylor/read-json-property-action@v1.0         with:           path: ./package.json           property: version       # 打包生成制品，且把制品压缩到assets.zip压缩包里       - name: Building         run: |           yarn build           zip -r assets ./dist/**       # 基于当前commit进行版本发布(Create a release)，tag_name是v前缀加上package.json的version值       - name: Create GitHub Release         # 此步骤中，版本发布后会返回对应的url，以供下面上传制品的步骤中读取使用         id: create_release         uses: actions/create-release@v1         env:           # GITHUB_TOKEN是准备工作步骤三申请的Personal Access Token           GITHUB_TOKEN: ${{ secrets.PROJECT_ACCESS_TOKEN }}         with:           tag_name: v${{steps.version.outputs.value}}           release_name: v${{steps.version.outputs.value}}           draft: false           prerelease: false       # 把assets.zip上传到仓库对应的发布版本Release上       - name: Update Release Asset         id: upload-release-asset         uses: actions/upload-release-asset@v1         env:           GITHUB_TOKEN: ${{ secrets.PROJECT_ACCESS_TOKEN }}         with:           upload_url: ${{ steps.create_release.outputs.upload_url }}           asset_path: ./assets.zip           asset_name: assets.zip           asset_content_type: application/zip       # 把制品上传到部署机器       - name: Upload to Deploy Server         uses: easingthemes/ssh-deploy@v2.0.7         env:           # SSH_PRIVATE_KEY为准备工作步骤三中生成密钥对里的私钥           SSH_PRIVATE_KEY: ${{ secrets.DEPLOY_TOKEN }}           # 指定当前目录中要上传的内容           SOURCE: "dist/"           # 指定上传到部署机器的哪个目录下           TARGET: "/data/www"           # 上传前指令，此处用于清空TARGET下的文件           ARGS: "-avzr --delete"           # REMOTE_HOST为机器的公网IP           REMOTE_HOST: ${{ secrets.REMOTE_HOST }}           # REMOTE_USER为登录机器时用到账号名           REMOTE_USER: ${{secrets.REMOTE_USER}}`

这样子就完成了**CD Workflow**的流程了，运行效果如下所示：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9aa0c1c9e3b48fba193017b9de0898f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

在**CD Workflow**运行完成后，可以输入自己机器的公网 IP 查看部署的项目。**注意：每次提 pr 时要确保 package.json 中的 version 值有变化，不然 CD Workflow 会在 Create GitHub Release 的步骤里报已存在 Tag 的错误。**

**关于 CD Workflow 细节补充：**

1. *为什么要获取`package.json`中`version`值，有什么作用?*
   
   `version`值在**CD Workflow**主要用于版本发布，此过程需要填写指定的`tag_name`。发布的版本如下所示：
   
   ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b2f57ce89f64881be4635e41ce2a9f1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
   
   点进去后可以看到当前项目的所有版本，如下所示：
   
   ![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62b357bd98724867946a81b3a45ab151~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
   
   我们也可以在通过[VSCode GitGraph 插件](https://link.juejin.cn?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Dmhutchie.git-graph "https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph")来看到自己发布的版本标签（commit 写的有点随便，不要介意），如下所示：
   
   ![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97aa18a6e64045ed9d1246bf5798db92~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
   
   在每次**主干**更新后进行版本发布不仅符合开源项目的更新流程，而且利于我们之后部署特定**发布版本**的制品（可看下面**深入章节的回滚流程**）。

2. 为什么要有 **Update Release Asset** 这个步骤？
   
   首先，把制品放在对应的**发布版本**上是很常见的开源行为，读者也可以把制品下载下来放到`nginx`直接查看页面效果。其次也是很重要的，是为了**回滚（下面深入篇会写回滚机制的实现）**的实现，回滚需要快速获取前一个**发布版本**的制品覆盖到部署机器上。因此需要我们把每个制品都存放在对应的**发布版本**以实现持久化。

3. *为什么 **Update Release Asset(上传制品)** 阶段不使用[`actions/upload-artifact`](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Factions%2Fupload-artifact "https://github.com/actions/upload-artifact")：*
   
   首先在**Release**中上传制品是很多开源项目中常见的操作，其次`actions/upload-artifact`中存在两个缺点：
   
   1. 上传的制品只能供同一个`Workflow`的不同`Job`中使用。换言之，不同`Workflow`是不能使用这个制品的，这样子我们就不利于我们在不同的流程去调用这个制品。例如**回滚（下面深入篇会写回滚机制的实现）**，在当前部署机器上的项目存在问题时，我们可以通过回滚迅速把前一个稳定的版本的制品覆盖到部署机器上。
   
   2. 上传的制品最多只存在 90 天，不能做到持久化。

  因此这里没选择`actions/upload-artifact`。

## 添加状态徽章

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c002a4f5f8946d7a77a2eac0721475f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

我们可以在项目的`README.md`中加了`CI`和`CD`两个状态徽章来代表这个项目已成功实现了`CI`和`CD`的流程，如上图所示。这两个状态徽章是根据你指定的**Workflow**的名称和最近一次运行的结果动态变化的。如果对应的流水线并没有运行记录，则效果如图上的`E2E-Test`状态徽章所示。

徽章可以直接以下面的格式来插入到自己的`README.md`上：

md

复制代码

`![example workflow](https://github.com/<OWNER>/<REPOSITORY>/actions/workflows/<WORKFLOW_FILE>/badge.svg)`

例如我的`CI`和`CD`徽章分别如下所示：

md

复制代码

`![CI](https://github.com/Hitotsubashi/cicd-study/actions/workflows/ci.yml/badge.svg) ![CD](https://github.com/Hitotsubashi/cicd-study/actions/workflows/cd.yml/badge.svg)`

关于更多有关工作流程状态徽章的可看官方文档[添加工作流程状态徽章](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Factions%2Fmonitoring-and-troubleshooting-workflows%2Fadding-a-workflow-status-badge "https://docs.github.com/cn/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge")

# 深入篇：完善流程

## 设置邮件通知

如果想要流水线在运行结束后把运行结果发送通知到邮件上，不必在流水线里修改，直接点击自己的头像，依次进入 **Settings > Notifications** 页面，如下所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/baf0a9218a6e46a18e553dd8d620263e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

此页面滚到下面有`Actions`通知设置，此处如果勾选，则只会在 **Workflow** 运行失败后发送通知到邮箱，取消勾选则在是 **Workflow** 在运行结束后无论成功失败都会发送通知到邮箱：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d04dba7972de4d1eb774aab0c03caf22~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

发送的邮箱是当前绑定你登录的`github`账号的邮箱，当然也可以自己在 **Settings > Notifications**添加新的邮箱如图所示：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8854460597774f80aa738d9c8945af87~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

## 添加回滚机制

在项目发布后，如果发现当前项目存在重大**bug**时，一般操作就是手动回滚到上一个版本。因此需要新增一个用于**回滚**的**workflow**(下称**Rollback Workflow**)。

首先还是要确定**Rollback Workflow**的触发条件**Event**。这里我们选择[`workflow_dispatch`](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Factions%2Fusing-workflows%2Fevents-that-trigger-workflows%23workflow_dispatch "https://docs.github.com/cn/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch")。`workflow_dispatch`事件还能支持手动输入信息，然后把这个信息当作环境变量供整个**Workflow**读取。

为了先了解`workflow_dispatch`事件类型的效果，下面我截取**Rollback Workflow**的`on`部分的代码如下所示：

yml

复制代码

`on:   workflow_dispatch:     # inputs下面可以指定要手动输入的信息     inputs:       # 例如这里我指定了手动输入version信息       version:         # 这是对该信息的描述         description: "choose a version to deploy"         # 指定必须填写         required: true`

然后在`Github`对应的**Rollback Workflow**页面中，点击**Run workflow**按钮后填写`version`后点击**Run workflow**就可以触发该流水线的执行：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a4fb27e8b5742129f43670142c6fd2e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

下面来看看整个**Rollback Workflow**的代码：

yml

复制代码

`name: Rollback on:   workflow_dispatch:     inputs:       # 这里的version指的是我们要部署的哪个发布版本的制品       # 这里输入的信息会作为github.event.inputs.version供此Workflow下的job读取       version:         description: "choose a version to deploy"         required: true jobs:   Rollback:     runs-on: ubuntu-latest     steps:       # 输出我们输入的version值       - name: Echo Version         env:           VERSION: ${{ github.event.inputs.version }}         run: |           echo "Version: $VERSION"       # 获取对应发布版本的制品       - name: Download Artifact         uses: dsaltares/fetch-gh-release-asset@master         with:           version: "tags/${{ github.event.inputs.version }}"           # 指定存放制品的压缩包           file: "assets.zip"           # 这里需要CD Workflow中准备工作里的Github Personal Access Token           token: ${{ secrets.GITHUB_TOKEN }}       # 下载压缩包后解压       - name: Unzip         run: |           unzip assets.zip           ls -a ./dist       # 把制品放到部署机器上       - name: Upload to Deploy Server         uses: easingthemes/ssh-deploy@v2.0.7         env:           SSH_PRIVATE_KEY: ${{ secrets.DEPLOY_TOKEN }}           ARGS: "-avzr --delete"           SOURCE: "dist/"           REMOTE_HOST: ${{ secrets.REMOTE_HOST }}           REMOTE_USER: ${{secrets.REMOTE_USER}}           TARGET: "/data/www"`

在触发**Rollback Workflow**后运行效果如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33e619c777ef46319934353298bd12ea~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

由于这里的项目比较小，因此整个**回滚**过程只需要 7 秒。

## 添加端对端测试

### 概念

**端对端测试(E2E Test)**指对部署在测试环境或正式环境的项目进行模拟真实用户操作的测试。一般会用如`puppeteer`这类能通过**Devtools 协议**控制浏览器的测试框架来实现。

我们在写前端测试的理想占比应如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03354a783dd645b1aae3397fc2db4aae~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

因为**端对端测试**本质注定其需要投入的工作量比较多，因此，主要用于覆盖应用的核心特性。对于其余次要特性我们可以用**集成测试**和**单元测试**去覆盖。

### 代码实现

下面我就通过`puppeteer`和`jest`结合去简单写一个针对`App.tsx`的端对端测试，首先新建一个用于**端对端测试**的`jest`配置文件**jest.config.e2e.js**，代码如下所示：

**jest.config.e2e.js**

js

复制代码

`module.exports = {   preset: "jest-puppeteer",   setupFilesAfterEnv: ["expect-puppeteer"],   testTimeout: 100000,   testMatch: ["<rootDir>/src/e2e/**/*.{spec,test}.{js,jsx,ts,tsx}"],   globals: {     "ts-jest": {       tsConfig: "<rootDir>/tsconfig.json",     },   }, };`

然后开始编写针对`App.tsx`的端对端测试代码：

**src/e2e/App.test.tsx**

tsx

复制代码

`const DEFAULT_URL = "http://localhost:3000/"; describe("Check Page", () => {   // beforeAll函数会在所有测试用例运行前先运行   beforeAll(async () => {     // 此处会设计了一个根据命令行参数来指定测试网址的逻辑     // 可通过npm run test:e2e -- --URL=*** 或 yarn test:e2e --URL=*** 来指定测试网址     // 否则默认测试网址为 DEFAULT_URL     let dynamicUrl = "";     process.argv.forEach((item) => {       const matches = item.match(/^\-\-URL\=(.*)$/);       if (matches) {         dynamicUrl = matches[1];       }     });     await page.goto(dynamicUrl || DEFAULT_URL);   });   // 测试点击按钮是否有效，一般端对端测试是用快照来对比判断是否成功的，这里为了方便直接获取DOM元素的content值来判断   it("click button", async () => {     await page.click('[role="button"]');     const content = await page.$eval('[role="button"]', (el) => el.textContent);     await expect(content).toEqual("count is: 1");   }); });`

在`package.json`的`scripts`添加命令行：`"test:e2e": "jest --config=jest.config.e2e.js"`后运行，控制台输出如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6439a9a1bad94b97bc8ee38cfc532472~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

关于`puppeteer`的更多用法请看[官方文档](https://link.juejin.cn?target=https%3A%2F%2Fpptr.dev%2F "https://pptr.dev/")。

**注意：运行前请确保测试网址上的应用可正常访问**

### 添加流水线

#### 实现端对端测试流水线

**端对端测试**是一项比较消耗资源的操作，因为每一个测试用例都需要开启一个**无头浏览器（non-headless）**来执行。所以一般会用一个单独的 **Job** 或者单独的 **Workflow** 来执行(这样可以开启一个单独的**Runner**，即进程来执行)。

首先要确定**端对端测试**的*运行时机*：

1. 在**CD 部署**或者**回滚**后都可能调用**端对端测试**来测试刚部署的项目的稳定性
2. 有时候也要手动触发**端对端测试**的运行，而由于**端对端测试**是对部署在测试环境或生产环境的项目上进行测试的，因此前端、后端、数据库同样会影响**端对端测试**的运行结果，因此在**后端迭代发布**或者**数据库变更**后，也要手动触发运行**端对端测试**以保证项目的稳定性。

针对此，我们把**端对端测试**设计为单独的流水线**E2E Test Workflow**，其触发条件**Event**设计为两个：

- `workflow_call`：[`workflow_call`](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Factions%2Fusing-workflows%2Fevents-that-trigger-workflows%23workflow_call "https://docs.github.com/cn/actions/using-workflows/events-that-trigger-workflows#workflow_call")可以使**E2E Test Workflow**成为可被调用的流水线，因此我们可以在**CD Workflow**和**Rollback Workflow**里调用用到。对应上面*运行时机的第 1 点*。

- `workflow_dispatch`：[`workflow_dispatch`](https://link.juejin.cn?target=https%3A%2F%2Fdocs.github.com%2Fcn%2Factions%2Fusing-workflows%2Fevents-that-trigger-workflows%23workflow_dispatch "https://docs.github.com/cn/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch")可以让我们随时手动触发**E2E Test Workflow**运行，对应*运行时机的第 2 点*。

接下新建`e2e-test.yml`来编写**E2E Test Workflow**，代码如下所示：

yml

复制代码

`name: E2E-Test # on设计为数组的模式，指定workflow_call 和 workflow_dispatch两种触发机制 on: [workflow_call, workflow_dispatch] jobs:   E2E-Test:     runs-on: ubuntu-latest     steps:       # 拉取代码       - name: Checkout repository         uses: actions/checkout@v2       # 下载Node       - name: Use Node.js         uses: actions/setup-node@v3         with:           node-version: "16.x"       # 添加缓存       - name: Cache         id: cache-dependencies         uses: actions/cache@v3         with:           path: |             **/node_modules           key: ${{runner.OS}}-${{hashFiles('**/yarn.lock')}}       # 安装依赖       - name: Installing Dependencies         # 在 if 条件下使用表达式时，可以省略表达式语法 (${{ }})，因为 GitHub 会自动将 if 条件作为表达式求值。         if: steps.cache-dependencies.outputs.cache-hit != 'true'         run: yarn install       # 运行端对端测试，指定测试网址为部署机器的公网IP       - name: Running E2E Test         run: yarn test:e2e --URL=http://${{ secrets.REMOTE_HOST }}/`

手动运行起来效果和`Rollback Workflow`的差不多，这里就不再重复展示了。

#### 在`CD`中调用端对端测试

我们要在**CD Workflow**里调用**E2E Test Workflow**，因此需要在**CD Workflow**对应的配置文件`cd.yml`里添加代码，如下所示：

yml

复制代码

`name: CD on: #...代码没变，省略 jobs:   CD: # ...代码没变，省略   # 添加名为E2E-Test的Job   E2E-Test:     # 指明要调用的流水线所在的路径     uses: ./.github/workflows/e2e-test.yml     # 指定该Job在Job CD 执行完后执行，不然Job会并行执行     needs: [CD]     # 指定调用的流水线集成当前流水线的secret     secrets: inherit`

在触发**CD Workflow**后运行如下效果所示：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e099366efc148b6ae4723a1586dd19c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

同样的也可以点进去看每个步骤的控制台输出：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f9656e63dfce4e07a8fff8c43954b1e4~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

#### 在回滚中选择性调用端对端测试

**回滚**后可以根据需要选择是否进行**端对端测试**，因此我们在原有的手动输入基础上加一个勾选框决定是否运行**端对端测试**即可，其余的逻辑与**CD Workflow**的**E2E-Test Job**类似。我们对**Rollback Workflow**对应的配置文件`rollback.yml`如下所示：

yml

复制代码

`name: Rollback on:   workflow_dispatch:     inputs:       version: # ...代码没变，省略       # 新增勾选框决定是否执行端对端测试       E2ETest:         description: "enable torun e2e test"         # 类型为boolean时，控件会以勾选框的形式呈现         type: boolean         # 默认值设为true，代表默认勾选         default: true jobs:   Rollback: # ...代码没变，省略   E2E-Test:     uses: ./.github/workflows/e2e-test.yml     needs: [Rollback]     # 条件判断 E2ETest勾选框 是否被勾选，勾选则执行，没勾选则跳过     if: github.event.inputs.E2ETest == true     # 指定调用的流水线集成当前流水线的secret     secrets: inherit`

手动执行时效果如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2e2308a4a1d04c1d8666255ad94a5529~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

如果把`E2E-Test`勾选框取消勾选后运行，效果如下所示：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/17b8a7e934fd470795b2713776947f2f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

## 思考：完整的 CD 玩法（待补充）？

前面的**CD 章节**中，考虑到入门以及大多数读者只有一台服务器，因此在设计步骤中跳过了测试环境的部署与测试，使其有点瑕疵。在之后我会继续更新这篇文章来补充这部分的章节。



## 参考

[作为前端，要学会用Github Action给自己的项目加上CICD - 掘金](https://juejin.cn/post/7113562222852309023#heading-29)


