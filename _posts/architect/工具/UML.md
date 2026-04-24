---
title: 『这就是UML！』系列内容第6讲：类图 - ProcessOn知识社区
source: https://www.processon.com/knowledge/classdiagram
author:
published:
created: 2026-04-24
description: 类图是UML模型中静态视图，它用来描述系统中的有意义的概念，包括具体的概念、抽象的概念、实现方面的概念等。如在图书馆借阅系统中，图书、借书、还书、读者、超期罚款等，这些都是这个系统中的一些有意义的概念。
tags:
---
类图是UML模型中静态视图，它用来描述系统中的有意义的概念，包括具体的概念、抽象的概念、实现方面的概念等。如在图书馆借阅系统中，图书、借书、还书、读者、超期罚款等，这些都是这个系统中的一些有意义的概念。

在前面的文章中曾经说过，静态视图包括类图和对象图，这篇文章只介绍类图，并给大家演示通过 [ProcessOn](https://www.processon.com/) 制作的类图模板。

![[_posts/architect/工具/media/cdb9b8e59290cd30beba381a0ac885af_MD5.png]]

						UML类图

### 什么是类图

类图（Class Diagram）是描述类、接口、协同以及他们之间关系的图，用来显示系统中这些概念的静态结构。类图也是其它图的基础，我们可以在类图的基础上，使用状态图、协作图、组件图和配置图等。

类图的主要作用有：

**1，对系统的词汇进行建模**

**2，对简单的协作进行建模**

**3，对逻辑数据库模式进行建模**

类图主要由 **类、接口和各种关系** 组成，关系主要包括泛化关系、依赖关系、关联关系和实现关系。

### 类

![[_posts/architect/工具/media/82b7189a423c497cd643343760760d37_MD5.png]]

						类详细解释

**1，类的构成**

在UML中，一个类通常由名称（Name）、属性（Attribute）和操作（Operation）构成。除此之外，类的构成还包含类的职责（Responsibility）、约束（Constraint）和注释（Note）等信息。

在UML中，类使用下面的图形来表示：

![[_posts/architect/工具/media/66598da5eb2bf8df6d170a9e8183bff6_MD5.png]]

				类的图形表示

类的图形符号从上到下分为三部分：类的名称、类的属性和类的操作。在实际中可能还有如下三种形式：

![[_posts/architect/工具/media/dc94b661aa86a4d6ef01dc80c5a54c45_MD5.png]]

					类的3种图形形式

**2，关于类的名称**

类的名称应该是一个名词，类名应该准确清晰的反映出问题域中的概念。按照UML约定，类的名称中的每个词的首字母应大写，且使用正体名称来表示可实例化的类，使用斜体名称表示抽象的类。

上方插图的Book类就是一个可实例化的类，而下面的这个Shape类属于抽象类：

![[_posts/architect/工具/media/1a6d51c847571290acf21da00919aae1_MD5.png]]

			抽象类

类的名称可以加上路径名称。如下图所示的例子：

![[_posts/architect/工具/media/25ef4bd720b0df6993549b21d1528a11_MD5.png]]

		类的名称添加路径名称

上图说明Order这个类来自Business包中，也可以在命名时写成如下的形式：

**Business::Order**

**::左边是包的名称，右边是类的名称**

**3，关于类的属性**

类的属性（Attribute）用于描述类的一个特征，这个特征是类的每个实例所共有的。一个类可以有零到多个属性。在UML中，类的属性定义语法如下：

\[可见性\] 属性名称 \[:数据类型\] \[=初始值\] \[{属性字符串}\]

上面的语法中，\[\]中的内容表示是可选的

在UML中，类的属性定义语法如下：

**（1）可见性**

可见性用于控制该属性被类的外部成员的可访问性。主要有以下四种情况：

+：公有属性，其它类可以访问该属性；

\-：私有属性，不能被其它类访问（默认为私有）；

\#：保护属性，只能被本类及其派生类访问；

~：包内可见，可以被本包中的其它类访问；

**（2）属性名称**

能够准确描述类特征的一个标识符，属性名通常是一个名词或名词短语。一般单字属性使用小写字母，多字属性从第2个单词开始，每个单词的第一个字符要大写。

**（3）数据类型**

属性所属的数据类型，如布尔类型、整型、浮点型，也可以是用户自定义的类型。

**（4）初始值**

属性的默认值，在类的实例没有赋其它值时，将采用该值作为该属性的值。

**（5）属性字符串**

用来指定该属性的其它信息。任何希望进一步描述该属性又没有合适的地方时都可以放在此处。

以上关于属性的描述内容虽然很多，但一般属性的可见性和属性名称是必需的部分。

**4\. 关于类的操作**

类的操作是类的行为特征或动态特征。类的操作相当于该类提供的一项服务，该服务可以由类的任何对象请求以影响其行为。操作在面向对象编程中通常被称为函数或方法，一个类可以拥有多个操作，也可以没有操作。

在UML中，类的操作描述语法如下：

\[可见性\] 操作名称 \[(参数表)\]\[:返回类型\]\[{属性字符串}\]

**（1）可见性**

与属性相同，规定该操作的可访问范围。

操作的可见性也是有+（公有）、-（私有），#（保护）和~（包内可见）四种。

![[_posts/architect/工具/media/40481dba0f118ee91704b590745058f1_MD5.png]]

			类的可见性操作

**（2）操作名称**

在实际建模中，操作名是用来描述所属类的行为的动词或动词短语。在UML中，和属性名的表示类似，单字的操作名小写；多字的操作名，除第一个单词外，其余单词的开头字母要大写。

![[_posts/architect/工具/media/35203cbcfbf85d201e93ad85c49cd0db_MD5.png]]

			类的操作名称

**（3）参数表**

参数表是一些按顺序排列的属性定义了操作的输入；

参数表是可选的；

参数的定义使用“名称：类型”的定义方式；

如果存在多个参数，则将各个参数用逗号隔开；

参数可以具有默认值，适用调用时没有提供参数值的情况。

如下面的time，其类型为Date，默认值为currentdate（即当前时间）：

![[_posts/architect/工具/media/2f92388137fffe56fe0886c534b685d3_MD5.png]]

			类的参数表

**（4）返回类型**

此项为可选项目，即操作不一定必须有返回类型。但在具体编程语言中使用void关键字来代表这种无返回值的情况。

![[_posts/architect/工具/media/9fd465071ee41b245b86b854928a085b_MD5.png]]

			类的返回类型

**（5）属性字符串**

任何在其它地方没有描述清楚的内容都可以在这里进行描述。

**5\. 关于类的职责**

在UML中，可以在操作部分的下面再添加一个区域，用来说明类的职责，即说明类或其它元素的契约或义务。

创建一个类时，同时声明这个类的所有对象具有相同种类的状态和相同种类的行为，在较高层次上，这些相应的属性和操作正是要完成类的职责和特性。职责可以使用一个短语、一个句子或一段短文的形式来描述。

在ProcessOn中，可以从UML编辑器左侧图形组件区的“基础图形”中选择矩形元素，然后把矩形拖拽到类图下方，通过这种非正式的方式在类图的下方增加一栏，将该类的职责逐条描述出来。如下图所示：

![[_posts/architect/工具/media/51f61c90e5981de255b91db2e5e44653_MD5.png]]

			类的职责

**6\. 类的约束**

类的约束指定了该类所要满足的一个或多个规则，在UML中，约束使用一个大括号括起来的文本信息：

![[_posts/architect/工具/media/94d0c7b72bbffcd3fbcdec4c12cb74c2_MD5.png]]

			类的约束

上面已经给大家详细介绍了类的相关知识，包括类的构成、名称、属性、操作、职责、约束等内容。


### 接口

![[_posts/architect/工具/media/675731ecf8f94a554fd53a2c7ca9f576_MD5.png]]

			接口详细解释

接口是在没有给出对象的实现和状态的情况下对对象行为的描述；

接口包含操作但不包含属性，且它没有对外界可见的关联；

一个类可以实现一个或多个接口，从而支持接口所指定的操作；

接口可以使用下面两种形式来表示：

![[_posts/architect/工具/media/850274594cbb07c6af7924834c6c8fa4_MD5.png]]

			接口两种表现形式

### 类图中的关系

![[_posts/architect/工具/media/6e82884f29476d89a84da2a8b6e278f6_MD5.png]]

				类与类的各种关系

在类图中的主要关系有四种：

**泛化关系（generalization）** ：表示一般和特殊关系；

**实现关系（realization）** ：表示规格说明和实现之间关系；

**依赖关系（dependency）** ：表示类之间使用关系；

**关联关系（association）** ：表示对象之间结构关系；

关联关系又延伸出 **聚合关系** 和 **组合关系** 。

**泛化关系**

泛化关系是存在于一般元素和特殊元素之间的分类关系。其中，特殊元素与一般元素兼容，且还包含自己特有的信息。

泛化可以用于类、接口、用例、参与者、包、状态机以及其它模型元素。泛化关系描述的是“is a kind of”的关系，使用带空心三角形的箭头来表示，箭头从子类指向父类：

![[_posts/architect/工具/media/692a9d643e821e98ce93d08a867827e1_MD5.png]]

			泛化关系

下面是一个车辆Vehicle与Truck之间的泛化表示：

![[_posts/architect/工具/media/86a59511e5f7275da8eca1fda647da46_MD5.png]]

	车辆Vehicle与Truck之间的泛化关系

实现这种关系的Java代码如下：

![[_posts/architect/工具/media/5733740934f1ff37ea84f925b888902e_MD5.png]]

			Java表示泛化关系

**实现关系**

实现关系是关于规格说明和其实现之间的关系，它将一种模型元素与另一种模型元素连接起来，比如类和接口。实现关系将不同语义层上的元素连接起来，这种关系不仅使用于接口和类之间，也可以是类的不同等级之间的联系，如粗设计与细设计之间。

实现一般使用带空心三角形的虚线箭头表示，箭头指向接口。下面是关于接口和实现接口类之间的标识方法：

![[_posts/architect/工具/media/c21cb707a05aa518fe1dfc4199782a78_MD5.png]]

			实现关系

下面是一个具体的接口和实现之间的例子。IUser是一个接口，VipUser是一个类，VipUser类要实现接口IUser：

![[_posts/architect/工具/media/db296a04cd3ec45dfac93ef3b6763072_MD5.png]]

			实现关系举例

其Java代码可以表示成下面的形式：

![[_posts/architect/工具/media/348e805e8161aabccc590035065697f0_MD5.png]]

			Java表示实现关系

**依赖关系**

它表示这样一种情形，对于一个元素（提供者）的某些改变可能会影响或提供消息给其它元素（客户），即客户以某种形式依赖于其它类元。

![[_posts/architect/工具/media/e3a04fa3fd286396e65723db91d610a5_MD5.png]]

				依赖关系

这主要的情形为：

客户类的操作需要提供者类的参数；

客户类的操作返回提供者类；

客户类的操作在实现中使用提供类的对象。

下面举个例子来说明这三种情形：

![[_posts/architect/工具/media/22d23b2dfd2884f06e3765173788aa09_MD5.png]]

		依赖关系举例

使用Java程序来表示上面这种情形：

![[_posts/architect/工具/media/15df21f122f8b9fa79f27d0478099f47_MD5.png]]

		Java表示依赖关系

上面这段代码表示了常见类之间依赖关系的三种情形：提供者类作为参数、提供者类作为方法的返回类型、提供者类作为方法中的一个变量。

**关联关系**

关联关系是一种结构关系，指出了一个事物的对象与另一个事物的对象之间的语义上的连接。

关联的任何一个连接点都叫做关联端。关联端可以有自己的角色、多重性、可见性等修饰。关联也可以有自己的名称。在UML中，关联关系用一条连接两个类的实线表示：

![[_posts/architect/工具/media/fda9fc316067c0c19ba27304db616e4f_MD5.png]]

			关联关系

上面的图中，“拥有”为关联的名称，“客户”为类Person的角色，“交通工具”为类car的角色。角色也可以有自己的可见性，“客户”和“交通工具”前的“+”即代表其可见性为公有。关联端的“1”和“0..n”是描述的多重性，下面将会做进一步的介绍。

关联的多重性（Multiplicity）是指有多少个对象可以参与该关联，可用来表达一个取值范围、特定值、无限定的范围或一组离散值。多重性是UML中使用最广泛的约束，主要有以下几种形式：

<table><tbody><tr><td colspan="1" rowspan="1" width="161">形式</td><td colspan="1" rowspan="1" width="161">含义</td><td colspan="1" rowspan="1" width="161">形式</td><td colspan="1" rowspan="1" width="161">含义</td></tr><tr><td colspan="1" rowspan="1" width="161">0</td><td colspan="1" rowspan="1" width="161">恰为0</td><td colspan="1" rowspan="1" width="161">0..n</td><td colspan="1" rowspan="1" width="161">0到多个</td></tr><tr><td colspan="1" rowspan="1" width="161">1</td><td colspan="1" rowspan="1" width="161">恰为1</td><td colspan="1" rowspan="1" width="161">1..n</td><td colspan="1" rowspan="1" width="161">1到多个</td></tr><tr><td colspan="1" rowspan="1" width="161">0..1</td><td colspan="1" rowspan="1" width="161">0或1</td><td colspan="1" rowspan="1" width="161">3..n</td><td colspan="1" rowspan="1" width="161">3到多个，至少3个</td></tr><tr><td colspan="1" rowspan="1" width="161">n</td><td colspan="1" rowspan="1" width="161">0或多个</td><td colspan="1" rowspan="1" width="161">3,5,7</td><td colspan="1" rowspan="1" width="161">3个或5个或7个</td></tr></tbody></table>

关联关系多重性

关联关系又可以分为单向关联、双向关联、自关联、聚合关联和组合关联5种情形。

**（1）单向关联**

单向关联用带箭头的实线表示，箭头由源类指向目标类，这种关联实际上是带导航的关联。它指在源类中要使用目标类的对象作为成员：

![[_posts/architect/工具/media/549b49c2dcbaf2a04542576c48de98c7_MD5.png]]

			单向关联

上面类之间的关系可以使用下面的Java代码来表示：

![[_posts/architect/工具/media/96b4c0093dde56655d1c0a5c3918edf8_MD5.png]]

			Java表示单向关联

**（2）双向关联**

双向关联关系不再绘制箭头，使用直线直接连接两个类即可，如下面两个类之间的关系即是双向关联关系：

![[_posts/architect/工具/media/6572166f9d5d02b8b8b204279057cd80_MD5.png]]

			双向关联

实现这种关系的Java代码可以如下表示：

![[_posts/architect/工具/media/e20edb493030aa882744d76d38dea48c_MD5.png]]

Java表示双向关联

带有角色修饰的双向关联：

![[_posts/architect/工具/media/1b5cf9ff7970abf07c929dcc68a44788_MD5.png]]

带有角色修饰的双向关联

实现这种关联关系的Java代码可表示如下：

![[_posts/architect/工具/media/cada8af1f460002474ef0081c0e71b59_MD5.png]]

Java程序表示带有角色修饰的双向关联

**（3）自关联**

自关联关系即一个类与自己进行关联。下面类关系的意思是employee类中有一个为employee类型的leader成员，表示员工的领导：

![[_posts/architect/工具/media/6cff6ae62bd54a0a37ed2b99c18a02fb_MD5.png]]

自关联

使用Java代码可以表示如下形式：

```
public class employee{    private employee leader;}
```

**（4）聚合关联**

聚合关联表示整体与部分关系的关联，关联关系中一组元素组成了一个更大、更复杂的单元。

聚合关系描述了“has a ”的关系，在UML中聚合关系用带空心菱形头的实线来表示，头部指向整体。这种关系中的空心菱形并不是箭头，它只表明哪端是整体。聚合的双向关联的一个例子，它表示College类是University类的一个聚合成分：

![[_posts/architect/工具/media/5ae8a823ae31dd648585a61f83803d72_MD5.png]]

聚合关联

使用Java实现的代码如下所示：

![[_posts/architect/工具/media/15757517b555aa28a5e7ba4624c9a66f_MD5.png]]

Jave表示聚合关联

如果是单向的聚合关系，可以使用下面的方式来表示：

![[_posts/architect/工具/media/9207cf94f46100986193ef32b9e1249d_MD5.png]]

单向聚合关联关系

跟上面那个例子的区别是，这种聚合关联只在University类中声明College类的成员对象，不在College类中声明University的成员对象。使用Java代码实现如下：

![[_posts/architect/工具/media/7bfc6030dba44d60230cd66deca9219e_MD5.png]]

Java表示单向聚合关联关系

当然单向的聚合关联关系也可以表示下图的形式：

![[_posts/architect/工具/media/5eba1dce757b53a4e5d63d24fcd2d63e_MD5.png]]

单向聚合关联表现形式

使用Java代码实现如下：

![[_posts/architect/工具/media/937f0de5315adf56d7c542edc9a4fdf0_MD5.png]]

Java表示单向聚合关联

**（5）组合关联**

组合关联是聚合的一种特殊情况，是更强形式的聚合(即强聚合)，成员的生命周期取决于聚合的生命周期。聚合不仅控制着成员对象的行为，而且控制着成员对象的创建和解构。

在UML中，组合使用带实心菱形头的实线来表示，其菱形在整体这一端：

![[_posts/architect/工具/media/5f0b0fcf1c9f96b8792c92b78644c062_MD5.png]]

组合关联

上面的Triangle类由多个Side类对象组合而成，当Triangle对象创建时，Side的对象被创建，Triangle对象被销毁时，其Side对象也将被销毁。使用Java代码实现如下：

![[_posts/architect/工具/media/51c4ce260020f85a9995bae9c5ee556a_MD5.png]]

Java表示组合关联

类图中的关系相当复杂，相信经过这么详细的讲解你也基本掌握了，可以来ProcessOn先创作一个简单的UML类图，梳理类图之间的关系，点击 [免费使用](https://www.processon.com/diagrams) 。

![[_posts/architect/工具/media/53937191428a719d778a014936e0c395_MD5.png]]

### 制作类图

制作类图的过程就是对领域问题及其解决方案的一个分析和设计的过程，关键是要准确地找出现实世界的对象类和类之间的联系，今天就用 [ProcessOn](https://www.processon.com/) 流程图给大家讲解制作步骤。

制作类图的一般步骤：

1，研究分析问题领域，确定系统的需求；

2，发现对象与对象类，明确他们的含义和责任，确定属性和操作。

3，发现类之间的静态联系；

4，设计类与联系；

5，绘制类图并编制相应的说明。

![[_posts/architect/工具/media/b350afb2b291cd243990f98298148992_MD5.png]]

制作类图的步骤

下面继续以前面文章中 [《『这就是是UML！』系列内容第5讲：用例图案例学习》](https://www.processon.com/knowledge/yonglitucase) 介绍的案例来设计其类图。案例中可以分析出以下几个类：

![[_posts/architect/工具/media/97887b00c952a4973a5376ca1a9908bc_MD5.png]]

S校图书馆借阅系统的”类“

经过认真分析类之间的关系，使用ProcessOn工具绘制的类图如下：

![[_posts/architect/工具/media/a1b4c12cc2a1ada9e7f5f4cbeddebc31_MD5.png]]

ProcessOn绘制的S校图书馆借阅系统类图

**往期内容推荐**

[这就是UML——第1讲 UML简介](https://www.processon.com/knowledge/umlintroduce)

[这就是UML——第2讲 UML通用知识上-事务、关系和公共机制](https://www.processon.com/knowledge/umluniversal)

[这就是UML——第3讲 UML通用知识下-视图和图](https://www.processon.com/knowledge/umlknowledge)

[这就是UML——第4讲 用例图](https://www.processon.com/knowledge/umlyonglitu)

[这就是UML——第5讲 用例图案例学习](https://www.processon.com/knowledge/yonglitucase)

[免费在线协同思维导图流程图 免费使用](https://www.processon.com/diagrams)