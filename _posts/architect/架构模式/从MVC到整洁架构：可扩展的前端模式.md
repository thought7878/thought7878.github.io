[从MVC到整洁架构：可扩展的前端模式 | From MVC to Clean Architecture: Frontend Patterns That Scale](https://youtu.be/wLHCauXhZYc?si=RsaCjJEWZk-ME0La)

【【进阶】从 MVC 到整洁架构：可扩展前端项目的架构演进之路】 https://www.bilibili.com/video/BV1DUavzEEBZ/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862


在本视频中，我们将探讨现代前端如何融合多种模式，为何严格使用模式往往会失败，以及基于功能的组织方式为何比传统的技术分层更易于扩展。我们还将介绍关于呈现器、视图模型、依赖规则的实际案例，以及构建可维护且能在生产环境中正常运行的应用程序所需的心智模型。


视频讲述了**前端架构模式从MVC到整洁架构的演进**，以及**如何在现代前端应用（如React、Vue）中应用这些模式**，强调**模式需灵活适配项目**，还介绍了*功能切片*等组织代码的方式，具体如下：


## 视频背景与目标
[00:00]
主讲人Dimma是柏林的资深前端开发者，拥有8年以上跨公司经验，频道聚焦前端面试和高级前端话题。本次视频旨在*填补前端架构模式知识与实践的差距*，*帮助开发者将模式应用于实际开发，助力面试和职业晋升*。


## MVC与MVP模式解析
[01:00]
MVC中`Model`是*数据+业务逻辑（增删改查数据的逻辑）*，`View`是UI，`Controller`是*二者桥梁*；

*MVP与MVC类似，核心区别*是 Presenter 负责更新View，而Controller不负责更新View。主讲人认为*View应与业务逻辑隔离，故以MVP为例*。

在React中，*React组件最初定位为View，仅负责根据props渲染，props其实是来自后端数据库的数据*；*后端为Model*；*前端需Presenter/Controller（如React的Hooks、Vue的Composables）处理API调用、数据准备*，**避免View直接与Model交互，以便框架迁移时减少代码改动**。

![[_posts/architect/架构模式/media/bd69310d229eabc4841faa50bd140ef1_MD5.jpeg]]

![[_posts/architect/架构模式/media/13d870088b99e687949faa0436f109fb_MD5.jpeg]]

![[_posts/architect/架构模式/media/8423b44f96e7041c6fc6f07ec36a046f_MD5.jpeg]]

![[_posts/architect/架构模式/media/ac4bba325e43787e2e32b6fcbab00e19_MD5.jpeg]]


## ViewModel的作用与应用
[07:00]
针对客户端*UI逻辑*（如弹窗开关、颜色变化）和*临时数据*（如未提交的表单数据），**需ViewModel存储**。虽React不支持双向绑定，不能严格称为MVVM，但ViewModel概念仍适用，例如将未提交表单数据存到localStorage，避免浏览器关闭后数据丢失。同时强调*无需严格遵循前端模式，需根据项目调整，因通用模式难以适配所有独特项目*。

![[_posts/architect/架构模式/media/e07db84e618056ac610289b1f3f30607_MD5.jpeg]]

![[_posts/architect/架构模式/media/3741acf9ec1e76bfee2c0526cbb7cffc_MD5.jpeg]]


## 整洁架构在前端的应用
[10:20]
**整洁架构核心**（后端架构）是内层不依赖外层，
- 最内层是`实体 Entities`：企业级业务规则、核心业务规则，通用的业务规则），
- 接着是`用例 Use cases`：应用级业务规则，将Entities具体化、更针对性、更专门化，适用于特定场景，
- `控制器`：接收数据，并为下一层准备数据，
- 数据库等`外部接口`位于外层，因其与View一样可替换，且替换不应影响业务规则。

前端虽无复杂业务规则，但客户端逻辑应置于核心，可借鉴整洁架构的依赖规则，例如修正Controller依赖外层类型的问题，让Controller作为数据类型的“真理来源”，外层依赖Controller。

![[_posts/architect/架构模式/media/d2573bc271a9a7331be617174861659f_MD5.jpeg]]

![[_posts/architect/架构模式/media/20d1188ae39c0ca540cefe539205f635_MD5.jpeg]]

![[_posts/architect/架构模式/media/5928d616d0df4fe6661d5214e8c06fd4_MD5.jpeg]]

![[_posts/architect/架构模式/media/178a6b154d72e0144e773fc7da22d71e_MD5.jpeg]]


## 功能模块组织代码的优势
[16:05]
传统MVC等*模式仅描述整体高层架构，未明确代码内部组织方式*。

在React中，**按功能模块组织代码（如员工功能相关的Hook、组件等集中存放）：** 
- Presenter/Controller：处理API和数据管理，即*Hook*；
- ViewModel：处理UI状态，即*特定Hook*；
- View：负责渲染，即*组件*。

**功能模块组织代码的优势：**
- 便于开发：所有内容都集中在一个地方；
- 便于重构功能：同上；
- 减少开发者协作冲突：不同开发者在不同的功能上独立工作，而不会相互干扰。

各司其职。此方式便于开发（所有内容都集中在一个地方）、重构功能，减少开发者协作冲突，实现责任分离，*比按技术层组织更易扩展*。

![[_posts/architect/架构模式/media/b5fb58a216e1183a509c1bf1b89ebaa9_MD5.jpeg]]

![[_posts/architect/架构模式/media/fc3253ea7594e07c296cd9e09fc994be_MD5.jpeg]]


## 视频总结与建议
[17:49]
掌握前端模式并非要严格套用，而是让开发者拥有共同技术语言，便于沟通（如代码审查时提及View逻辑、Controller等）；模式的核心是明确边界和分离关注点；每个项目独特，需基于模式提供的工具和思维模型，灵活调整应用模式，以适配现代前端技术和项目需求。

## 参考资料
【简述5种最常用的架构模式】 https://www.bilibili.com/video/BV1RapMeYEoQ/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862
