## What Is CI/CD? 什么是 CI/CD？

Continuous Integration (CI) and Continuous Delivery/Deployment (CD), collectively known as CI/CD, represent pivotal practices in modern software engineering. CI is the process where developers regularly integrate their code changes into a central repository. Each integration is then automatically tested and verified, promoting high-quality code and early bug detection. On the other hand, CD takes this a step further by automating the delivery of these tested code changes to predefined infrastructure environments, ensuring seamless and reliable software updates. With this automated build, testing, and deployment process, CI/CD practices enable teams to release software faster and more reliably, making it a cornerstone of DevOps culture.  
持续集成 (CI) 和持续交付/部署 (CD) 统称为 CI/CD，代表了<u>现代软件工程的关键实践</u>。 **CI** 是开发人员定期将代码更改集成到中央存储库的过程。然后，<u>每个集成都会自动测试和验证，从而促进高质量的代码和早期错误检测</u>。另一方面，**CD** 更进一步，<u>将这些经过测试的代码更改自动交付到预定义的基础设施环境</u>，确保无缝且可靠的软件更新。<u>通过这种自动化的构建、测试和部署流程，CI/CD 实践使团队能够更快、更可靠地发布软件，使其成为 DevOps 文化的基石</u>。

A CI/CD pipeline compiles incremental code changes made by developers and packages them into software artifacts. Automated testing verifies the integrity and functionality of the software, and automated deployment services make it immediately available to end users. The goal is to enable early detection of defects, increase productivity, and shorten release cycles.  
CI/CD 管道<u>编译</u>开发人员所做的增量代码更改并将其<u>打包</u>到软件工件中。<u>自动化测试</u>验证软件的完整性和功能，<u>自动化部署</u>服务使其立即可供最终用户使用。**目标是尽早发现缺陷、提高生产率并缩短发布周期**。

This process contrasts with the traditional approach to software development—consolidating multiple small software updates into one large release, thoroughly testing it, and only then deploying it. CI/CD pipelines support the agile concept of development in small iterations, enabling teams to deliver value to customers faster, and create a rapid feedback loop for developers.  
**这一过程与传统的软件开发方法形成鲜明对比**——**将多个小型软件更新整合到一个大型版本中，对其进行彻底测试，然后才进行部署**。 **CI/CD 管道支持小迭代中的敏捷开发概念，使团队能够更快地向客户交付价值，并为开发人员创建快速反馈循环**。

## 持续集成、持续交付和持续部署之间有什么区别？

What Are the Differences Between Continuous Integration, Continuous Delivery, and Continuous Deployment?



### 持续集成 Continuous Integration

In the traditional software development process, multiple developers produce code, and only towards the end of a release do they consolidate their work. This caused many bugs and issues, which could only be identified and resolved after a long testing phase. Until all those issues were resolved, the software could not be released. This hurt software quality, and meant that teams could typically only release new versions once or twice a year.  
<u>在传统的软件开发过程中</u>，多个开发人员编写代码，只有在发布结束时他们才会整合他们的工作。<u>这导致了许多错误和问题，只有经过长时间的测试阶段才能识别和解决。在所有这些问题得到解决之前，该软件无法发布</u>。**这损害了软件质量，并且意味着团队通常每年只能发布一两次新版本**。

Continuous Integration (CI) was designed to solve this problem and support agile development processes. CI means that any changes developers make to their code are immediately integrated into the master branch of the software project. The CI system automatically runs tests to catch quality issues, and developers get quick feedback and can fix issues immediately. Developers often commit to the master branch or work on a short-lived feature branch, and a feature is not considered complete until it is integrated with other code changes in the master branch.  
**持续集成（CI）旨在解决这个问题并支持敏捷开发流程**。 CI 意味着开发人员对其代码所做的任何更改都会<u>立即集成到软件项目的主分支中</u>。 CI 系统<u>自动运行测试来发现质量问题</u>，开发人员可以<u>快速获得反馈并立即修复问题</u>。开发人员<u>通常会提交到主分支或在短期功能分支</u>上工作，并且在与主分支中的其他更改的代码集成之前，功能不会被认为是完成/完整的。

In a CI process, a build server is responsible for taking new code changes, running automated tests using multiple tools, integrating the code into the master branch, and generating a build—a new version of software artifacts needed to deploy the software.  
在 CI 流程中，**构建服务器**负责进行新的代码更改，使用多个工具运行自动化测试，然后将代码集成到主分支中，然后生成构建，然后部署软件。

CI greatly improves the quality and speed of software development. Teams can create more features that provide value to users, and many organizations now release software every week, every day, or multiple times a day.  
**CI极大地提高了软件开发的质量和速度**。团队可以创建更多为用户提供价值的功能，<u>许多组织现在每周、每天或每天多次发布软件</u>。

### 持续交付 Continuous Delivery

Traditionally, deploying new software versions has been a large, complex and risky task. After the new version was tested, the operations team was tasked with deploying it into production. Depending on the size of the software it could take hours, days or weeks, requires detailed checklists and many manual steps, and special expertise. Deployments often failed and required developer workarounds or urgent assistance.  
<u>传统上，部署新的软件版本是一项庞大、复杂且危险的任务</u>。新版本经过测试后，<u>运维团队的任务是将其部署到生产中</u>。**根据软件的大小，这可能需要数小时、数天或数周的时间，需要详细的清单和许多手动步骤以及特殊的专业知识**。<u>部署经常失败，需要开发人员解决方法或紧急援助</u>。

There are many problems with this traditional approach—it is stressful for the team, expensive and risky for the organization, and causes bugs and downtime in production environments.  
这种传统方法存在很多问题——<u>给团队带来压力，给组织带来昂贵的成本和风险，并导致生产环境中出现错误和停机</u>。

Continuous Delivery (CD, also known as CDel) aims to solve these problems through automation. The CD approach allows teams to package software and deploy it to production environments with the push of a button. The basic principle of CD is that any change to a software project can be deployed to a production environment immediately, without any special effort.  
**持续交付（CD，也称为CDel）旨在通过自动化来解决这些问题**。 CD 方法允许团队打包软件并将其部署到生产环境中，**只需按一下按钮**。 **CD 的基本原则**是对软件项目的任何更改都可以<u>立即部署到生产环境，而不需要任何特殊的工作</u>。

After the CI system consolidates the new changes and creates a new build, the CD system packages the new version, deploys it to a test environment, automatically evaluates its behavior, and pushes it to the production environment. This last step can be manually approved, but no manual action is required to deploy the new version to production.  
<u>CI系统</u>整合新的变更并创建新的构建后，<u>CD系统</u>打包新版本，将其部署到测试环境，自动评估其行为，并将其推送到生产环境。<u>最后一步可以手动批准</u>，但无需手动操作即可将新版本部署到生产环境。

Implementing CD requires automating the entire software development lifecycle, including build, test, environment setup, and deployment. All artifacts must reside in a source code repository, and an automated mechanism is required to create and update the environment.  
**实施 CD 需要自动化整个软件开发生命周期**，包括构建、测试、环境设置和部署。所有工件都必须驻留在源代码存储库中，并且需要自动化机制来创建和更新环境。

A true CD pipeline has great advantages. It allows development teams to deliver value to customers quickly and create truly agile development processes.  
真正的CD管道有很大的优势。它使开发团队能够快速为客户提供价值并创建真正敏捷的开发流程。

### 持续部署 Continuous Deployment

Continuous Deployment (CDep) goes one step further than continuous delivery. All changes going through all stages of the production pipeline undergo automated tests, and if these tests pass, they are immediately deployed to production and exposed to customers.  
持续部署 (CDep) 比持续交付更进一步。生产管道各个阶段的所有变更都会经过自动化测试，如果这些测试通过，它们就会立即部署到生产中并暴露给客户。

Continuous deployment puts an end to release dates, and is a great way to speed up the customer feedback loop and reduce stress on the team. Developers can focus on building the software and see it running in production minutes after completion.   
持续部署结束了发布日期，是加快客户反馈循环并减轻团队压力的好方法。开发人员可以专注于构建软件，并在完成后几分钟内看到它在生产中运行。

Continuous deployment can be difficult to implement. It requires seamless automation at all stages of the process, robust automated testing suites, and a culture of “continuous everything” that enables detection and rapid response to production issues.  
持续部署可能很难实施。它需要流程各个阶段的无缝自动化、强大的自动化测试套件以及能够检测和快速响应生产问题的“一切持续”文化。

***Learn more in our detailed guides to:  
请参阅我们的详细指南了解更多信息：***

- **[*Software deployment 软件部署*](https://codefresh.io/learn/software-deployment/)**
- [***Continuous deployment 持续部署***](https://codefresh.io/learn/continuous-delivery-only-for-unicorns/)

## CI/CD 与 DevOps 有何关系？ How Does CI/CD Relate to DevOps?



DevOps promotes better collaboration and communication between development (Dev) and operations (Ops) teams. It often requires changing various aspects of the development lifecycle, including job roles, tools, best practices, and automating the lifecycle.   
<u>DevOps 促进开发 (Dev) 和运营 (Ops) 团队之间更好的协作和沟通</u>。它通常需要改变开发生命周期的各个方面，包括工作角色、工具、最佳实践和生命周期自动化。

DevOps typically involves the following:   
DevOps 通常涉及以下内容：

- Adopting automation, programmable infrastructure deployment and maintenance, and iterative software development.   
  采用<u>自动化</u>、可编程基础设施部署和维护以及<u>迭代</u>软件开发。
- Establishing cross-functional teams while facilitating a culture change to build trust between these previously disparate teams.   
  建立跨职能团队，同时促进文化变革，以在这些以前不同的团队之间建立信任。
- Aligning technologies to business requirements.   
  使技术与业务需求保持一致。

CI/CD supports the efforts of DevOps teams. It enables teams to implement automation across the development lifecycle and rapidly validate and deliver applications to end-users. Here is how it works:  
<u>CI/CD 支持 DevOps 团队的工作</u>。<u>它使团队能够在整个开发生命周期中实现自动化</u>，并快速验证应用程序并将其交付给最终用户。下面是它的<u>工作原理：</u>

- Continuous integration tools help initialize processes, ensuring developers can build, test, and validate code within a shared repository without manual work.   
  持续集成工具有助于初始化流程，确保开发人员**无需手动工作**即可在共享存储库中构建、测试和验证代码。
- Continuous delivery tools extend these automated steps to production testing and configuration for release management.   
  持续交付工具将这些自动化步骤扩展到生产测试和发布管理配置。
- Continuous deployment tools automatically invoke tests, handling configurations, provisioning, monitoring, and rollbacks.  
  持续部署工具自动调用测试、处理配置、供应、监控和回滚。

## CI/CD 管道有哪些阶段？ What Are the Stages of a CI/CD Pipeline?



The CI/CD pipeline performs continuous integration, delivery, and deployment in four phases—source, build, test, and deploy.   
CI/CD 管道分四个阶段执行持续集成、交付和部署：源、构建、测试和部署。

### 源码 Source

Creating source code is the first phase in a CI/CD pipeline. During this phase, developers translate requirements into functional algorithms, features, and behaviors. Tools often vary, depending on the project, the project’s language, and other variables. As a result, there is no uniform source creation pipeline.   
创建源代码是 CI/CD 管道的第一阶段。<u>在此阶段，开发人员将需求转化为功能算法、功能和行为</u>。工具通常会有所不同，具体取决于项目、项目的语言和其他变量。因此，没有统一的源创建管道。

A source code creation pipeline may incorporate any of the following:  
<u>源代码创建管道可以包含以下任何内容：</u>

- A programming framework, such as Java, .NET, C#, or PHP.  
  <u>编程框架</u>，例如 Java、.NET、C# 或 PHP。
- An integrated development environment (IDE) that supports the programming language chosen for the project.   
  支持为项目选择的编程语言的<u>集成开发环境 (IDE)</u>。
- Code-checking tools, such as vulnerability scanners, basic error detection, and tools verifying adherence to coding standards.   
  <u>代码检查工具</u>，例如漏洞扫描器、基本错误检测以及验证是否遵守编码标准的工具。
- Code repositories and version control systems, such as Git.  
  <u>代码存储库和版本控制系统</u>，例如 Git。

### 构建 Build

The build phase involves pulling source code from a repository, establishing links to libraries, dependencies, and modules, and building these components into an executable (.exe) file. It typically requires tools that can generate execution logs, denote errors to correct and investigate, and notify developers once a build is completed.  
**构建阶段**包括<u>从存储库中提取源代码</u>，建立到库、依赖项和模块的链接，并将这些组件构建到可执行 (.exe) 文件中。它通常需要能够生成执行日志、指示错误以纠正和调查以及在构建完成后通知开发人员的工具。

Build tools vary according to the programming language. Some scenarios may require a specific build tool, while others can employ the same IDE for both source and build phases. A build phase may use additional tools to translate an executable file into a deployable or packaged execution environment, such as a virtual machine (VM) or a Docker container.  
<u>构建工具根据编程语言的不同而有所不同</u>。某些场景可能需要特定的构建工具，而其他场景则可以在源代码和构建阶段使用相同的 IDE。构建阶段可以使用附加工具将可执行文件转换为可部署或打包的执行环境，例如虚拟机 (VM) 或 Docker 容器。

### 测试 Test

During the source code creation phase, the code undergoes static testing. The completed build enters the next CI/CD phase to undergo dynamic testing, including:   
<u>在源代码创建阶段，代码经过静态测试</u>。完成的构建进入下一个 CI/CD 阶段**进行动态测试，包括：**

- **Basic functional or unit testing**—helps validate new features work as intended.  
  **基本功能或单元测试**——帮助验证新功能是否按预期工作。
- **Regression testing**—helps ensure changes do not break previously working features.  
  **回归测试** - **有助于确保更改不会破坏以前的功能**。

In addition to functional and regression tests, the build undergoes tests that verify integration, performance, and user acceptance. If errors occur during the testing phase, the process loops these results back to developers for analysis and remediation. Since builds undergo many tests, developers employ automated testing to minimize human error and improve productivity.  
<u>除了功能和回归测试之外，构建还经过验证集成、性能和用户接受度的测试</u>。如果在测试阶段出现错误，该过程会将这些结果循环返回给开发人员进行分析和修复。由于构建需要经过许多测试，因此开发人员<u>采用自动化测试来最大限度地减少人为错误并提高生产力</u>。

### 部署 Deploy

After a build passes the testing phase, it becomes a candidate for deployment. There are two main ways to deploy the build, including:  
<u>构建通过测试阶段后</u>，它就成为部署的候选者。部署构建有两种主要方法，包括：

- **Continuous delivery**—the build is sent to human staff for approval and then deployed. For example, new versions are automatically deployed to a test environment, but promotion to production is gated by a manual approval or merge request.  
  **持续交付**——构建被发送给工作人员批准，然后部署。例如，<u>新版本会自动部署到测试环境，但升级到生产环境需要手动批准或合并请求</u>。
- **Continuous deployment**—the pipeline automatically deploys the build to testing, staging, and production environments, assuming it passes all relevant tests, with no manual approvals.  
  **持续部署**——管道会<u>自动将构建部署到测试、暂存和生产环境，假设它通过了所有相关测试，无需手动批准</u>。

A typical deployment phase creates a deployment environment and moves the build to a deployment target, like a server. You can automate these steps with scripts or workflows in automation tools. Most deployments also integrate with error reporting and ticketing tools to detect unexpected errors post-deployment and alert developers.  
典型的部署阶段会创建部署环境并将构建移动到部署目标（例如服务器）。您可以使用自动化工具中的脚本或工作流程自动执行这些步骤。大多数部署还与错误报告和票务工具集成，以检测部署后的意外错误并向开发人员发出警报。

***Learn more in our detailed guide to the*** [***CI/CD pipeline***](https://codefresh.io/learn/ci-cd/what-is-a-ci-cd-pipeline-a-complete-guide/)  
在我们的 CI/CD 管道详细指南中了解更多信息

## Kubernetes 对 CI/CD 管道的好处

Benefits of Kubernetes for CI/CD Pipelines



The secret to the success of a CI/CD pipeline is ensuring that application updates are performed quickly and in an automated manner. Teams typically face the following challenges when adopting CI/CD:  
**CI/CD 管道成功的秘诀**是<u>确保以自动化方式快速执行应用程序更新</u>。采用 CI/CD 时，<u>团队通常面临以下挑战：</u>

- **Manual steps in the release process**—Many CI/CD processes still use manual testing and deployment steps. This can cause delays and affect production schedules. Manual CI/CD processes can cause code merge conflicts and increase customer wait times for patches and updates.  
  **发布过程中的手动步骤** - 许多 CI/CD 流程仍然使用手动测试和部署步骤。这可能会导致延误并影响生产计划。手动 CI/CD 流程可能会导致代码合并冲突并增加客户等待补丁和更新的时间。
- **Downtime risk**—manual infrastructure management processes can be a headache for DevOps teams because they create the risk of downtime. For example, unexpected traffic spikes that exceed capacity can cause downtime and require manual steps to restore applications.  
  **停机风险**——手动基础设施管理流程可能会让 DevOps 团队头疼，因为它们会带来停机风险。例如，超出容量的意外流量峰值可能会导致停机，并需要<u>手动步骤来恢复应用程序</u>。
- **Inefficient resource utilization**—applications are often deployed on servers in an inefficient way. This means organizations have to pay more for capacity. As applications are added, scaled up and down, it can be difficult to efficiently use available hardware resources. This is true whether the application is running in the cloud or on-premises.  
  **资源利用效率低下**——应用程序通常以低效的方式部署在服务器上。这意味着组织必须为容量支付更多费用。随着应用程序的添加、扩展和缩小，有效利用可用硬件资源可能会变得困难。无论应用程序是在云端还是在本地运行，都是如此。

Kubernetes can solve all three of these problems. It reduces the time and effort required to develop and deploy applications in a CI/CD pipeline. Its efficient resource management model increases hardware utilization, automates management processes, and reduces disruptions that negatively impact customers. Specifically, Kubernetes can:  
**Kubernetes 可以解决所有这三个问题**。<u>它减少了在 CI/CD 管道中开发和部署应用程序所需的时间和精力。其高效的资源管理模型提高了硬件利用率，实现管理流程自动化，并减少对客户产生负面影响的中断</u>。具体来说，Kubernetes 可以：

- **Cluster Management** – Kubernetes takes the best practices for all previous clustering solutions and packages them in a vendor agnostic way. It comes bundled with several critical components such as schedulers and resource managers and contains plugin mechanisms for storage, networking, secrets etc. Writing distributed applications with Kubernetes is much easier compared to legacy clustering solutions as the environment it offers is standardized and without any proprietary mechanisms to closed systems  
  **集群管理**——Kubernetes 采用了所有以前集群解决方案的最佳实践，并以与供应商无关的方式打包它们。它捆绑了多个关键组件，例如调度程序和资源管理器，并包含用于存储、网络、机密等的插件机制。<u>与传统集群解决方案相比</u>，使用 Kubernetes 编写分布式应用程序要容易得多，因为它提供的环境是标准化的，并且没有任何专有机制至封闭系统
- **Orchestrate deployment and provisioning**—coordinating provisioning activities and simplifying deployment. Kubernetes handles hardware and storage resource configuration, software deployment, scalability, and health monitoring, and is fully customizable for specific needs.  
  **协调部署和配置**——协调配置活动并简化部署。 Kubernetes 处理硬件和存储资源配置、软件部署、可扩展性和运行状况监控，并且可以根据特定需求进行完全定制。
- **Declarative constructs**—codifying the final state of the desired environment or application in simple, human readable code. This makes it possible to recover faster from downtime and production issues, better control scaling, and streamline disaster recovery processes.  
  **声明性构造**——将所需环境或应用程序的最终状态编码为简单的、人类可读的代码。这使得从停机和生产问题中更快地恢复、更好地控制扩展并简化灾难恢复流程成为可能。

## 云服务中的 CI/CD



CI/CD in the cloud refers to the practice of using cloud-based services to perform Continuous Integration and Continuous Delivery/Deployment (CI/CD) of software. This enables developers to build, test, and deploy their software faster and more efficiently by leveraging the scalability, flexibility, and cost-effectiveness of the cloud.  
**云中的CI/CD**是指<u>使用基于云的服务来执行软件的持续集成和持续交付/部署（CI/CD）的实践</u>。<u>这使得开发人员能够利用云的可扩展性、灵活性和成本效益，更快、更高效地构建、测试和部署他们的软件</u>。

Cloud providers, such as AWS, Azure, Google Cloud, and others, offer a wide range of services that can be used to implement CI/CD in the cloud. These services include:  
AWS、Azure、Google Cloud 等云提供商提供了多种可用于在云中实施 CI/CD 的服务。<u>这些服务包括：</u>

- **Cloud-based source control:** Providers offer cloud-based source control services, such as GitHub, GitLab, and Bitbucket, which can be used to store and manage code.  
  **基于云的源代码控制：** 提供商提供基于云的源代码控制服务，例如 GitHub、GitLab 和 Bitbucket，可用于存储和管理代码。
- **Build and test automation:** Services such as AWS CodeBuild, Azure DevOps, and Google Cloud Build can be used to automate the building and testing of code.  
  **构建和测试自动化：** AWS CodeBuild、Azure DevOps 和 Google Cloud Build 等服务可用于自动化代码的构建和测试。
- **Containerization and orchestration:** Services such as AWS Elastic Container Service, Azure Container Instances, and Google Kubernetes Engine can be used to containerize, orchestrate, and migrate applications, which makes it easier to deploy and manage them in the cloud.  
  **容器化和编排：** AWS弹性容器服务、Azure容器实例和Google Kubernetes Engine等服务可用于容器化、编排和迁移应用程序，从而更轻松地在云中部署和管理它们。
- **Deployment automation:** Services such as AWS CodeDeploy, Azure DevOps, and Google Cloud Deployment Manager can be used to automate the deployment of code to various environments, including development, staging, and production environments.  
  **部署自动化：** AWS CodeDeploy、Azure DevOps 和 Google Cloud Deployment Manager 等服务可用于<u>自动将代码部署到各种环境，包括开发、暂存和生产环境</u>。
- **Monitoring and logging:** Cloud providers offer various services such as AWS CloudWatch, Azure Monitor, and Google Stackdriver that can be used to monitor and log the performance and status of cloud-based applications and infrastructure.  
  **监控和日志记录：** 云提供商提供各种服务，例如 AWS CloudWatch、Azure Monitor 和 Google Stackdriver，可用于<u>监控和记录基于云的应用程序和基础设施的性能和状态</u>。

By using these services, developers can easily set up, configure and manage a CI/CD pipeline in the cloud, which will increase the speed of delivery of new features and bug fixes to customers and also help to detect and fix problems early in the development cycle.  
通过使用这些服务，开发人员可以轻松地在云中设置、配置和管理 CI/CD 管道，这将提高向客户交付新功能和错误修复的速度，并有助于在开发早期检测和修复问题循环。

Learn more in the detailed guide to [cloud migration](https://faddom.com/what-is-cloud-migration/) by Faddom.  
请参阅 Faddom 的云迁移详细指南了解更多信息。

## 持续集成/持续交付工具

CI/CD Tools

Here is a brief review of popular CI/CD tools.  
以下是流行的 CI/CD 工具的简要回顾。

**Learn more about these and other tools in our detailed guide to [CI/CD tools](https://codefresh.io/learn/ci-cd/ci-cd-tools-16-tools-delivery-pros-must-know-about/)  
在我们的 CI/CD 工具详细指南中了解有关这些工具和其他工具的更多信息**

### 持续集成工具

Continuous Integration Tools



Popular CI tools include Codefresh, Bitbucket Pipelines, Jenkins, CircleCI, Bamboo, and GitLab CI.  
流行的 CI 工具包括 Codefresh、Bitbucket Pipelines、Jenkins、CircleCI、Bamboo 和 GitLab CI。

#### Codefresh

Codefresh is a comprehensive GitOps continuous integration toolset designed for Kubernetes and modern applications. It is built from the ground up for flexibility and scalability around Argo Workflows and Argo Events. It takes the best of the open source toolset and provides essential enterprise features like a unified user interface, a single pane for cloud-wide management, security validated enterprise-grade runtime, end-to-end auditability, and cross-application single sign-on.  
Codefresh 是专为 Kubernetes 和现代应用程序设计的综合 GitOps 持续集成工具集。它是从头开始构建的，旨在围绕 Argo 工作流程和 Argo 活动提供灵活性和可扩展性。它充分利用了开源工具集的优点，并提供了基本的企业功能，例如统一的用户界面、用于云范围管理的单一窗格、经过安全验证的企业级运行时、端到端可审核性和跨应用程序单点登录。在。

#### Bitbucket Pipelines

Bitbucket Pipelines is a CI tool that integrates directly into Bitbucket, a cloud-based source control system. It lets you manage pipelines as code and deploy your projects to production via CD tools. You can use Bitbucket pipelines to create pipeline definitions and kick off builds.  
Bitbucket Pipelines 是一种 CI 工具，可直接集成到基于云的源控制系统 Bitbucket 中。它允许您将管道作为代码进行管理，并通过 CD 工具将项目部署到生产环境。您可以使用 Bitbucket 管道来创建管道定义并启动构建。

#### Jenkins

Jenkins is an open source automation tool that provides plugins to help develop, deploy, and deliver software. It is a server that lets developers distribute tasks across various machines and perform distributed tests and deployments. The Jenkins Pipeline offers several plugins to facilitate the implementation of a continuous integration (CI) pipeline.  
Jenkins 是一个**开源自动化工具**，提供插件来帮助开发、部署和交付软件。它是一个服务器，允许开发人员在各种机器上分配任务并执行分布式测试和部署。 Jenkins Pipeline 提供了多个插件来促进持续集成 (CI) 管道的实施。

***Learn more in the detailed guide to [Jenkins](https://codefresh.io/learn/jenkins/).  
在 Jenkins 详细指南中了解更多信息。***

#### CircleCI

CircleCI is a CI tool that supports various container systems, delivery mechanisms, and version control systems like Github. CircleCI can run complex pipelines with caching, resource classes, and Docker layer caching. You can run this tool in the cloud and on-premises.  
CircleCI 是一个 CI 工具，支持各种容器系统、交付机制以及 Github 等版本控制系统。 CircleCI 可以运行具有缓存、资源类和 Docker 层缓存的复杂管道。您可以在云端和本地运行此工具。

#### Bamboo

Bamboo is an automation server for continuous integration that can automatically build, test, integrate, and document source code to prepare apps for deployment. It offers a simple user interface for CI/CD and various features, including automated merging and built-in deployment support.  
Bamboo 是一个用于持续集成的自动化服务器，可以自动构建、测试、集成和记录源代码以准备应用程序的部署。它为 CI/CD 提供简单的用户界面和各种功能，包括自动合并和内置部署支持。

#### GitLab CI

GitLab CI is an open source CI tool. It lets you use the GitLab API to install and set up projects hosted on GitLab. GitLab CI can help you test and build projects and deploy your builds. It indicates areas that require improvement and lets you secure project data using confidential issues.  
GitLab CI 是一个开源 CI 工具。它允许您使用 GitLab API 来安装和设置 GitLab 上托管的项目。 GitLab CI 可以帮助您测试和构建项目以及部署您的构建。它指出需要改进的领域，并允许您使用机密问题保护项目数据。

### 持续交付和部署工具

Continuous Delivery and Deployment Tools



Popular CD tools include Codefresh, Argo CD, GoCD, AWS CodePipeline, Azure Pipelines, and Spinnaker.  
流行的 CD 工具包括 Codefresh、Argo CD、GoCD、AWS CodePipeline、Azure Pipelines 和 Spinnaker。

#### Codefresh

Codefresh is a modern GitOps software delivery solution powered by Argo with support for advanced deployments like canary, blue-green, and experimental releases. It provides comprehensive dashboards that offer visibility from code to cloud while integrating with your favorite tools. A centralized dashboard gives insight into deployments at scale while providing the security and support enterprises need.  
Codefresh 是由 Argo 提供支持的现代 GitOps 软件交付解决方案，支持金丝雀、蓝绿和实验版本等高级部署。它提供了全面的仪表板，可提供从代码到云的可见性，同时与您喜欢的工具集成。集中式仪表板可以深入了解大规模部署，同时提供企业所需的安全性和支持。

#### Argo CD

Argo CD is a Kubernetes-native CD tool optimized for [GitOps](https://codefresh.io/gitops/). It stores configuration in a Git repository and automatically applies it to Kubernetes clusters, making it easy to integrate with existing workflows. Argo CD can detect configuration drift, monitor application health, and roll back unwanted configuration changes. It also supports progressive delivery strategies like blue/green and canary deployment.  
Argo CD 是针对 GitOps 优化的 Kubernetes 原生 CD 工具。它将配置存储在 Git 存储库中，并自动将其应用到 Kubernetes 集群，从而可以轻松与现有工作流程集成。 Argo CD 可以检测配置漂移、监控应用程序运行状况并回滚不需要的配置更改。它还支持渐进式交付策略，例如蓝/绿和金丝雀部署。

**Learn more in our detailed guide to [Argo CD](https://codefresh.io/learn/argo-cd/)  
在我们的 Argo CD 详细指南中了解更多信息**

#### GoCD

GoCD is an open source CD tool that helps automate the entire build-test-release process, including code check-in and all the way to deployment. It works with Git, Subversion, Mercurial, TFVC (TFS), and Perforce, and has an open plugin ecosystem. It is deployed on-premises.  
GoCD 是一个开源 CD 工具，可帮助自动化整个构建-测试-发布过程，包括代码签入和一直到部署。它可与 Git、Subversion、Mercurial、TFVC (TFS) 和 Perforce 配合使用，并拥有开放的插件生态系统。它部署在本地。

#### AWS CodePipeline

AWS CodePipeline is a cloud-based CD service that helps model, visualize, and automate software release steps and continuous changes. Notable features include release process automation, establishing a consistent release process, and viewing pipeline history details.  
AWS CodePipeline 是一项基于云的 CD 服务，可帮助对软件发布步骤和持续更改进行建模、可视化和自动化。值得注意的功能包括发布流程自动化、建立一致的发布流程以及查看管道历史详细信息。

**Learn more in our detailed guide to [CI/CD in AWS](https://codefresh.io/learn/ci-cd/ci-cd-on-aws-the-basics-and-4-best-practices/)  
请参阅我们的 AWS 中 CI/CD 详细指南了解更多信息**

#### Azure Pipelines

Azure Pipelines is a cloud-based service that helps automatically build, test, and ship code to multiple targets, through a combination of CI and CD mechanisms. It supports many languages, including Python, JavaScript, and Go, most application types, including Node.js and C++, and targets such as virtual machines (VMs), containers, on-premises, and cloud platforms.  
Azure Pipelines 是一项基于云的服务，可通过 CI 和 CD 机制的组合帮助自动构建、测试代码并将其发送到多个目标。它支持多种语言，包括 Python、JavaScript 和 Go，大多数应用程序类型，包括 Node.js 和 C++，以及虚拟机 (VM)、容器、本地和云平台等目标。

**Learn more in our detailed guide to [CI/CD in Azure](https://codefresh.io/learn/ci-cd/building-your-ci-cd-pipeline-in-azure/)  
请参阅我们的 Azure CI/CD 详细指南了解更多信息** 

#### Spinnaker

Spinnaker is an open source CD platform for multi-cloud environments. It offers a pipeline management system and integrates with many cloud providers. Spinnaker provides a pipeline builder to automate releases, and lets you save and reuse existing pipelines as JSON files. It supports Kubernetes and integrates with tools like Prometheus, Datadog, and StackDriver.  
Spinnaker 是一个适用于多云环境的开源 CD 平台。它提供管道管理系统并与许多云提供商集成。 Spinnaker 提供了一个管道构建器来自动发布，并允许您将现有管道保存为 JSON 文件并重复使用。它支持 Kubernetes 并与 Prometheus、Datadog 和 StackDriver 等工具集成。

#### GitHub Actions

GitHub Actions is a CI/CD tool from GitHub, the world’s most popular platform for hosting and collaborating on software projects. GitHub Actions allows developers to automate workflows directly from their GitHub repositories, making it convenient for teams already using GitHub.  
GitHub Actions 是来自 GitHub 的 CI/CD 工具，GitHub 是世界上最受欢迎的软件项目托管和协作平台。 GitHub Actions 允许开发人员直接从 GitHub 存储库自动化工作流程，为已经使用 GitHub 的团队带来便利。

GitHub Actions allows developers to create custom workflows using simple YAML syntax, and they can leverage a marketplace of community-contributed actions to extend their workflows.  
GitHub Actions 允许开发人员使用简单的 YAML 语法创建自定义工作流程，并且他们可以利用社区贡献的操作市场来扩展他们的工作流程。

***Learn more in our detailed guide to*** [***GitHub Actions***](https://codefresh.io/learn/github-actions/)  
在我们的 GitHub Actions 详细指南中了解更多信息

#### Harness.io

Harness.io is a CI/CD platform that emphasizes simplicity of usage. It offers a visual interface for building and managing deployment pipelines and can automate various CI/CD processes, such as canary deployments and rollback decisions. Harness.io automatically verifies deployments in real-time to detect and mitigate issues before they impact end-users.  
Harness.io 是一个强调使用简单性的 CI/CD 平台。它提供了用于构建和管理部署管道的可视化界面，并且可以自动化各种 CI/CD 流程，例如金丝雀部署和回滚决策。 Harness.io 自动实时验证部署，以在问题影响最终用户之前检测并缓解问题。

***Learn more in our detailed guide to*** [***Harness.io***](https://codefresh.io/learn/harness-io/)  
在我们的 Harness.io 详细指南中了解更多信息

***Learn more about these and other tools in our detailed guides to*** [***CI/CD tools***](https://codefresh.io/learn/ci-cd/ci-cd-tools-16-tools-delivery-pros-must-know-about/)  
在我们的 CI/CD 工具详细指南中了解有关这些工具和其他工具的更多信息

## CI/CD 安全风险

CI/CD Security Risks  

### Supply Chain Attacks 供应链攻击

A supply chain attack is a cyber attack that targets weak links in an organization’s supply chain. A [supply chain](https://www.mend.io/resources/blog/software-supply-chain-security-the-basics-and-four-critical-best-practices/) is the network of all individuals, organizations, resources, activities, and technologies involved in creating and selling software products.  
供应链攻击是针对组织供应链中薄弱环节的网络攻击。供应链是参与创建和销售软件产品的所有个人、组织、资源、活动和技术的网络。

Modern software applications rely heavily on dependencies to provide their core functionality. The software ecosystem relies heavily on CI/CD to publish source code and binaries to public repositories. This allows attackers to bypass standard security measures and directly attack the supply chain, infecting many applications and websites simultaneously.  
现代软件应用程序严重依赖依赖关系来提供其核心功能。软件生态系统严重依赖 CI/CD 将源代码和二进制文件发布到公共存储库。这使得攻击者能够绕过标准安全措施并直接攻击供应链，同时感染许多应用程序和网站。

### 不安全的系统配置

Insecure System Configuration



A CI/CD environment consists of several systems from various vendors. To optimize CI/CD security, security teams must focus on the health and resilience of individual systems and the code and artifacts flowing through the pipeline.  
CI/CD 环境由来自不同供应商的多个系统组成。为了优化 CI/CD 安全性，安全团队必须关注各个系统以及流经管道的代码和工件的运行状况和弹性。

Like any other system that stores and processes data, a CI/CD system includes a variety of security settings and configurations at the application, network, and infrastructure levels. These settings have a significant impact on the security posture of a CI/CD environment and its vulnerability to potential breaches. Attackers are on the lookout for ways to exploit potential CI/CD vulnerabilities and misconfigurations.  
与存储和处理数据的任何其他系统一样，CI/CD 系统包括应用程序、网络和基础设施级别的各种安全设置和配置。这些设置对 CI/CD 环境的安全状况及其潜在漏洞的脆弱性产生重大影响。攻击者正在寻找利用潜在 CI/CD 漏洞和错误配置的方法。

### 不安全的代码

Insecure Code 

The demand for rapid software development and delivery has increased the use of open source third-party integrations. Some teams may bring third-party integrations into their deployments without properly scanning the source code for security vulnerabilities. Such integrations could lead to vulnerabilities in the CI/CD pipeline. Developers may not follow code security best practices, increasing the attack surface. Common code vulnerabilities include user input vulnerabilities, buffer overflows, error handling errors, and serialization issues.  
对快速软件开发和交付的需求增加了开源第三方集成的使用。一些团队可能会将第三方集成引入其部署中，而没有正确扫描源代码是否存在安全漏洞。此类集成可能会导致 CI​​/CD 管道中存在漏洞。开发人员可能不遵循代码安全最佳实践，从而增加了攻击面。常见的代码漏洞包括用户输入漏洞、缓冲区溢出、错误处理错误和序列化问题。

### Exposure of Secrets 曝光秘密

Automated processes are a key component of any DevOps infrastructure. CI/CD orchestration and configuration tools are increasingly being deployed into DevOps processes to automate processes and facilitate rapid deployment of software releases.  
自动化流程是任何 DevOps 基础设施的关键组成部分。 CI/CD 编排和配置工具越来越多地部署到 DevOps 流程中，以实现流程自动化并促进软件版本的快速部署。

However, CI/CD tools make extensive use of secrets (like passwords and API access tokens). they access many sensitive resources, such as information from other applications and services, code repositories, and databases. The more secrets you have, the more difficult it is to securely store, transmit, and audit them.  
然而，CI/CD 工具广泛使用机密（例如密码和 API 访问令牌）。他们访问许多敏感资源，例如来自其他应用程序和服务、代码存储库和数据库的信息。您拥有的秘密越多，安全地存储、传输和审核它们就越困难。

Also, secrets are not only used for tool-to-tool authentication. In many cases, confidential information must be provided during the build and deployment process so that deployed resources can access it. This is especially important when deploying microservices using the auto-scaling capabilities of tools like Kubernetes.  
此外，秘密不仅仅用于工具到工具的身份验证。在许多情况下，必须在构建和部署过程中提供机密信息，以便部署的资源可以访问它。当使用 Kubernetes 等工具的自动扩展功能部署微服务时，这一点尤其重要。

## DevSecOps and CI/CD

[DevSecOps](https://www.tigera.io/learn/guides/devsecops/) is a philosophy and organizational culture that adopts security practices in DevOps processes. It is also used to represent a continuous delivery, security-centric software development lifecycle (SDLC).  
DevSecOps 是一种在 DevOps 流程中采用安全实践的理念和组织文化。它还用于表示持续交付、以安全为中心的软件开发生命周期 (SDLC)。

Historically, security was seen as a secondary part of DevOps workflows. Information security practices were applied at the end of the software development lifecycle (SDLC). However, discovering security breaches at the end of SDLC can be very frustrating and issues discovered at that stage are difficult and expensive to resolve. DevSecOps drives security engagement as an active part of the software development lifecycle (SDLC) from its earliest stages.  
<u>从历史上看，安全性被视为 DevOps 工作流程的次要部分</u>。信息安全实践在软件开发生命周期（SDLC）结束时应用。然而，在 SDLC 结束时发现安全漏洞可能会非常令人沮丧，而且在该阶段发现的问题解决起来既困难又昂贵。 DevSecOps 从最早阶段就将安全参与作为软件开发生命周期 (SDLC) 的一个积极组成部分来推动。

A typical DevOps pipeline includes stages such as planning, coding, building, testing, release, and deployment. DevSecOps enforces specific security checks at each stage of the DevOps pipeline:  
典型的 DevOps 管道包括规划、编码、构建、测试、发布和部署等阶段。 DevSecOps 在 DevOps 管道的每个阶段强制执行特定的安全检查：

- **Planning**—during the planning phase, you create a plan that determines when, where, and how to perform a security analysis and test your scenarios.  
  规划 — 在规划阶段，您创建一个计划来确定何时、何地以及如何执行安全分析并测试场景。
- **Coding**—using linting tools and Git controls to secure passwords and API keys.  
  编码——使用 linting 工具和 Git 控件来保护密码和 API 密钥。
- **Building**—using static application testing (SAST) tools to track defects in code before deployment to production. These tools are specific to a programming language.  
  构建——在部署到生产之前使用静态应用程序测试 (SAST) 工具来跟踪代码中的缺陷。这些工具特定于编程语言。
- **Testing**—during application testing, using Dynamic Application Security Testing (DAST) tools to detect errors related to user authentication, authorization, SQL injection, and API endpoints.  
  测试 - 在应用程序测试期间，使用动态应用程序安全测试 (DAST) 工具来检测与用户身份验证、授权、SQL 注入和 API 端点相关的错误。
- **Release**—leveraging security analysis tools for vulnerability scanning and penetration testing. These tools should be used immediately before deploying the application.  
  发布——利用安全分析工具进行漏洞扫描和渗透测试。这些工具应在部署应用程序之前立即使用。
- **Deployment**—after completing the above tests, the secure build is sent to production for final deployment. Deployments should be monitored at runtime for undiscovered security threats or vulnerabilities.  
  部署——完成上述测试后，安全构建将被发送到生产环境以进行最终部署。应在运行时监视部署是否存在未发现的安全威胁或漏洞。

## CI/CD 最佳实践

CI/CD Best Practices 

Here are a few best practices that can help you practice CI/CD more effectively.  
以下是一些可以帮助您更有效地实践 CI/CD 的最佳实践。

***Learn about these and other best practices in our detailed guide to*** [***CI/CD best practices***](https://codefresh.io/devops/enterprise-ci-cd-best-practices-part-1/)  
在我们的 CI/CD 最佳实践详细指南中了解这些和其他最佳实践

### Build Only Once 仅构建一次

Eliminate the practice of building the same source code multiple times. If you need to build, package, or bundle your software, you only need to perform this step once and promote binaries to the next stage of the pipeline.   
消除多次构建相同源代码的做法。如果您需要构建、打包或捆绑软件，则只需执行此步骤一次，并将二进制文件提升到管道的下一阶段。

Most successful CI implementations include the build process as the first step in the CI/CD cycle, making sure that software is packaged in a clean environment. This eliminates human error and reduces the chance of overlooked artifacts or incorrect artifacts included by mistake. Also, any artifacts generated must be versioned and uploaded to Git, to ensure that every time they are needed in the process, the same version of the build is available.  
大多数成功的 CI 实施都将构建过程作为 CI/CD 周期的第一步，确保软件打包在干净的环境中。这消除了人为错误，并减少了被忽视的工件或错误包含的错误工件的可能性。此外，生成的任何工件都必须进行版本控制并上传到 Git，以确保每次在流程中需要它们时，都可以使用相同版本的构建。

### 优先考虑自动化工作

Prioritize Automation Efforts



Organizations moving to automated processes often struggle to identify which processes to automate first. For example, it is useful to automate the code compilation process from scratch. It is a good idea to run automated smoke tests every time developers commit new code. Unit tests are usually automated first to reduce developer workload.  
转向自动化流程的组织通常很难确定首先要自动化哪些流程。例如，从头开始自动化代码编译过程非常有用。每次开发人员提交新代码时运行自动冒烟测试是一个好主意。单元测试通常首先自动化，以减少开发人员的工作量。

In most cases, you will automate functional testing before UI testing. Unlike UI tests, which change more frequently, functional tests do not require frequent updates to automation scripts. Consider all possible dependencies, assess their impact, and prioritize automation as appropriate.  
在大多数情况下，您将在 UI 测试之前自动化功能测试。与更改更频繁的 UI 测试不同，功能测试不需要频繁更新自动化脚本。考虑所有可能的依赖关系，评估其影响，并酌情优先考虑自动化。

***Learn more in the detailed guides to:  
在详细指南中了解更多信息：***

- [*Unit testing 单元测试*](https://brightsec.com/blog/unit-testing/)
- [Unit testing frameworks  单元测试框架](https://brightsec.com/blog/unit-testing-frameworks/)

### 经常发布 Release Often

A commercial release is only possible if the software is release-ready and tested in a production-like environment. Therefore, it is best to add a step that deploys new versions to a realistic pre-production staging environment, or to the production environment itself alongside the current production version.   
只有当软件已准备好发布并在类似生产的环境中进行测试时，才有可能进行商业发布。因此，最好添加一个步骤，将新版本部署到实际的预生产暂存环境，或者与当前生产版本一起部署到生产环境本身。

The following are release strategies that can help you deploy software to staging and production environments with low risk:  
以下是可以帮助您以低风险将软件部署到临时和生产环境的发布策略：

- **Canary deployment**—release the new version for some users, test their response, and if it works well, roll it out to a larger population. If the test fails, roll back and repeat.  
  金丝雀部署——为部分用户发布新版本，测试他们的响应，如果效果良好，则将其推广到更多人群。如果测试失败，则回滚并重复。
- [**Blue/green deployment**](https://codefresh.io/learn/software-deployment/what-is-blue-green-deployment/)—run the current and new version of the software in two identical production environments. Initially, the current version is live and the new version is idle. Then traffic is switched over from the current version to the environment containing the new version. This lets you test the new version on real user traffic, and if something goes wrong, you can immediately roll back to the current stable version.  
  蓝/绿部署——在两个相同的生产环境中运行当前版本和新版本的软件。最初，当前版本处于活动状态，新版本处于空闲状态。然后流量从当前版本切换到包含新版本的环境。这使您可以在真实用户流量上测试新版本，如果出现问题，您可以立即回滚到当前的稳定版本。
- **A/B Testing**—A/B testing is a method used to test the functionality of an application, such as changes to the user experience. Two or more versions of the application, with small differences between them, are served to production users in parallel. Teams observe how users interact with each version, and when one version is deemed successful, it is rolled out to all users.   
  A/B 测试 - A/B 测试是一种用于测试应用程序功能的方法，例如用户体验的更改。应用程序的两个或多个版本（它们之间存在微小差异）并行地提供给生产用户。团队观察用户如何与每个版本交互，当一个版本被认为成功时，就会向所有用户推广。

### 使 CI/CD 管道成为部署到生产环境的唯一方法

Make the CI/CD Pipeline the Only Way to Deploy to Production



Investing in building a reliable, fast, and secure CI/CD pipeline gives you confidence in your build quality, but bypassing that process for any reason can hurt your efforts. Requests to circumvent the release process often occur because changes are minor or urgent—you should not give in to these requests.  
投资构建可靠、快速且安全的 CI/CD 管道可以让您对构建质量充满信心，但以任何原因绕过该流程都可能会损害您的努力。由于更改较小或紧急，经常会出现规避发布流程的请求 - 您不应该屈服于这些请求。

Skipping automated tests runs the risk of production issues, but the problem does not end there. It is much more difficult to reproduce and debug problems, and trace them to specific build artifacts, because builds are not automatically deployed to test and production environments.  
跳过自动化测试会带来生产问题的风险，但问题并不止于此。重现和调试问题并将其跟踪到特定的构建工件要困难得多，因为构建不会自动部署到测试和生产环境。

Even if at some point a team makes an exception and skips the CI/CD process, it is worth understanding the motive. Why was the request to skip the CI/CD pipeline made in the first place? Talk to key stakeholders and identify if the process seems slow or inflexible to them. You may need to make performance or process improvements to address those concerns.   
即使在某个时候某个团队破例并跳过 CI/CD 流程，也值得了解其动机。为什么首先请求跳过 CI/CD 管道？与主要利益相关者交谈，确定他们认为流程是否缓慢或不灵活。您可能需要改进性能或流程来解决这些问题。

By remaining responsive to stakeholder requirements, and communicating the benefits of the CI/CD pipeline, you can convince stakeholders and avoid disrupting the CI/CD process due to urgent requests.  
通过持续响应利益相关者的要求并传达 CI/CD 管道的优势，您可以说服利益相关者并避免因紧急请求而中断 CI/CD 流程。

### 每次发布都清理环境

Clean Up Environments with Every Release



To get the most out of your testing process, it’s worth cleaning up pre-production environments before each deployment.   
为了充分利用测试过程，有必要在每次部署之前清理预生产环境。

If your environment has been running for a long time, it can be difficult to keep track of all configuration changes and updates applied—this is known as configuration drift. This means tests may not return the same results. Maintaining a static environment incurs maintenance costs, slows down testing, and delays the release process.  
如果您的环境已经运行了很长时间，则可能很难跟踪应用的所有配置更改和更新，这称为配置漂移。这意味着测试可能不会返回相同的结果。维护静态环境会产生维护成本、减慢测试速度并延迟发布过程。

By using containers to host environments and run tests, you can easily start and destroy each newly deployed environment by scripting these steps using declarative configuration (for example, Kubernetes YAML files). Instantiating new containers before each deployment ensures consistency, and makes it easy to scale your environment to test multiple builds in parallel.  
通过使用容器来托管环境并运行测试，您可以使用声明性配置（例如 Kubernetes YAML 文件）编写这些步骤的脚本，轻松启动和销毁每个新部署的环境。在每次部署之前实例化新容器可确保一致性，并可以轻松扩展环境以并行测试多个构建。

## Taking CI/CD to the Next Level with Codefresh

使用 Codefresh 将 CI/CD 提升到新的水平

Codefresh has made it our mission since 2014 to help teams accelerate their pace of innovation.  Our platform, powered by Argo, combines the best of the open-source with an enterprise-grade runtime allowing you to fully tap the power of Argo Workflows, Events, CD, and Rollouts. It provides teams with a unified GitOps experience to build, test, deploy, and scale their applications.  
自 2014 年以来，Codefresh 就将帮助团队加快创新步伐作为我们的使命。我们的平台由 Argo 提供支持，将最好的开源技术与企业级运行时相结合，让您能够充分利用 Argo 工作流程、事件、CD 和部署的强大功能。它为团队提供统一的 GitOps 体验来构建、测试、部署和扩展其应用程序。





## Reference

[What is CI/CD? Continuous Integration and Delivery](https://codefresh.io/learn/ci-cd/)


