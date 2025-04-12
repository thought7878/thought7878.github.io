[YouTube 视频](https://www.youtube.com/watch?v=zdGfo6I1yrA&ab_channel=LydiaHallie)
[b 站视频](https://www.bilibili.com/video/BV16w4m197PV/?spm_id_from=333.999.0.0&vd_source=22af953ea4c09540ad1966711a2d53f0)

- [00:00](https://www.bilibili.com/video/BV16w4m197PV/?t=0.108247#t=0.11) 视频简介
- [00:11](https://www.bilibili.com/video/BV16w4m197PV/?t=11.645425#t=11.65) Execution Context Overview
  - 调用函数，就会创建一个 Execution Context，并将其推送到 call stack（其实是 execution context stack）
  - 执行上下文是什么？执行上下文本质上*定义了代码执行的环境*。包含很多内部组件，*JS 引擎使用这些组件来跟踪代码的执行流程*。_执行上下文使用 environment record 来保存变量、函数，environment records manage the identifier bindings within the context_（如下图）
  - **执行上下文的两个阶段**：创建阶段和执行阶段 [00:45](https://www.bilibili.com/video/BV16w4m197PV/?t=45.180927#t=45.18)
    - 创建阶段：为上下文中的变量、函数等设置内存空间
    - 执行阶段：将其推送到 call stack，执行代码
  - Global Execution Context 由 3 部分组成： 
	  - Realm 
	  - LexicalEnvironment 
	  - VariableEnvitonment
    ![[base/js/知识点总结/media/6b7a4dcca94cfe94d321b3afa926a111_MD5.png]]
- [01:24](https://www.bilibili.com/video/BV16w4m197PV/?t=84.613012#t=01:24.61) Realm
![[_posts/base/js/知识点总结/media/5b820f1cebbc136b438156335cd45298_MD5.jpeg]]
  - realm 本质上是一个隔离的环境，代码在其中运行
  - **全局执行上下文**，由三部分组成：（参考上图）
    - `Intrinsics（内置的）`：包含内置的对象和函数，提供了所有标准内置对象
    - `Global Object` [01:59](https://www.bilibili.com/video/BV16w4m197PV/?t=119.898017#t=01:59.90)
		- 保存/暴露 Intrinsics
		- `Host Properties（宿主属性）`：Web API，document/localStorage/fetch/setTimeout/history
		- `User properties（自定义属性）`：全局声明的函数、var变量
	- `Global Environment Record`：管理该上下文中的变量（identifier bingdings:标识符和值的绑定） [02:45](https://www.bilibili.com/video/BV16w4m197PV/?t=165.269302#t=02:45.27)
		- `ObjectRecord`：对 Global Object 的直接引用
		- `DeclarativeRecord`：保存 let/const 变量，不是 var 变量和函数声明
		- `GlobalThisValue`：全局的 this，指向 Global Object  [03:29](https://www.bilibili.com/video/BV16w4m197PV/?t=209.731061#t=03:29.73)
		- `OuterEnv`：保存/指向外部的声明该函数的 environment record，函数的 Environment 属性
- [03:57](https://www.bilibili.com/video/BV16w4m197PV/?t=237.500098#t=03:57.50) `Lexical Environment` / `Variable Environment`：
	- **Lexical Environment**，指向包含变量（let、const）、函数（函数表达式）的 Environment Record。词汇环境只是指向包含所有内容绑定的环境记录（the lexical environment just points to the environment record that contains the bindings for everything）
	- **Variable Environment**，指向包含变量（var）、函数（函数声明）的 Environment Record
- [04:26](https://www.bilibili.com/video/BV16w4m197PV/?t=266.597824#t=04:26.60) 例子解释

- [05:41](https://www.bilibili.com/video/BV16w4m197PV/?t=341.015584#t=05:41.02) **执行上下文/函数的 creation 阶段**： 
	- 解析代码：
		- 提升变量
		- 分配、设置内存空间。变量，没有设置值，值是uninitialized（let/const）、undefined（var）；函数表达式，没有设置值；函数声明，初始化，有值（函数体、代码）。
		- 创建函数对象：
			- 函数的两个属性：`[[Environment]]`/`[[Call]]`。
				- _`[[Environment]]`指向声明该函数的外部的 environment record，是实现闭包的基础_；
				- `[[Call]]`方法：每当调用函数时都会调用该方法，来创建自己的 Function Execution Context
![[_posts/base/js/知识点总结/media/8265fef659538695ec9c6714628e6bbb_MD5.jpeg]]
- [06:16](https://www.bilibili.com/video/BV16w4m197PV/?t=376.839284#t=06:16.84) **执行上下文/函数的 execute 阶段**：
    - [06:44](https://www.bilibili.com/video/BV16w4m197PV/?t=404.722898#t=06:44.72) 执行`Call`方法：创建自己的 Function Execution Context
    - [07:14](https://www.bilibili.com/video/BV16w4m197PV/?t=434.361555#t=07:14.36) Function `Environment Record`：
	    - 保存函数内部的变量、声明的函数、 parameters 参数
		- `OuterEnv`：它的值是 Function Object 的 Environment 属性值。该值，即外部的声明该函数的 environment record。
    - [09:00](https://www.bilibili.com/video/BV16w4m197PV/?t=540.666144#t=09:00.67) 变量提升 Hoisting
    - [10:11](https://www.bilibili.com/video/BV16w4m197PV/?t=611.420751#t=10:11.42) **作用域链** Scope Chain。`OuterEnv`是实现作用域链的基础。
    - [10:27](https://www.bilibili.com/video/BV16w4m197PV/?t=627.557933#t=10:27.56) **闭包** Closures
![[_posts/base/js/知识点总结/media/fdf63eeb9e479fa88d722db0d5e06396_MD5.jpeg]]

![[_posts/base/js/知识点总结/media/326c90c1cb44b6a8b98e9e70e2fc5130_MD5.jpeg]]

## Execution Context
![[base/js/知识点总结/media/6b7a4dcca94cfe94d321b3afa926a111_MD5.png]]

主要由这几部分组成：
- Lexical Environment
	- Environment Record
- Variable Environment
	- Environment Record

### 执行上下文的3个生命周期/阶段
- **创建阶段**（creation phase）：
	- *变量：提升变量；为变量、函数声明设置内存空间*。变量，没有设置值，值是uninitialized（let/const）、undefined（var）；函数表达式，没有设置值；函数声明，初始化，有值（函数体、代码）。
	- *函数：创建函数对象*：
		- 函数的两个属性：`[[Environment]]`/`[[Call]]`。
			- _`[[Environment]]`指向声明该函数的外部的 environment record，应是暂存，然后赋值给environment record的OuterEnv_；
			- `[[Call]]`方法：每当调用函数时都会调用该方法，来创建自己的 Function Execution Context
![[_posts/base/js/知识点总结/media/8265fef659538695ec9c6714628e6bbb_MD5.jpeg]]
- **执行阶段**（execution phase）：
	- execution context 入栈 Call Stack
	- 执行代码：
		- 为变量初始化，
		- 执行函数（调用`[[Call]]` => 执行上下文的创建阶段 => 执行上下文的执行阶段）
- **销毁阶段**（destroy phase）：
	- execution context 出栈 Call Stack

### 全局执行上下文


## Lexical Environment
Lexical Environment 是 Execution Context 的一部分、组件，内部包含 Environment Record。如上图。

**Lexical Environment**，*指向*包含变量（let、const）、函数（函数表达式）的 Environment Record。词汇环境只是指向包含所有内容绑定的环境记录（the lexical environment just points to the environment record that contains the bindings for everything）

## Variable Environment
**Variable Environment**，*指向*包含变量（var）、函数（函数声明）的 Environment Record

## Environment Record

环境记录保存*变量、参数、声明函数、this*等。

**`OuterEnv`属性**：值等于 Function Object 的 Environment 属性值，即外部的声明该函数的 environment record（outer environment）。*作用域链是基于这个属性实现的*。

## 函数
### Function Object
函数本质上就是对象。两个私有属性：`[[Call]]`和`[[Environment]]`
![[_posts/base/js/知识点总结/media/fdf63eeb9e479fa88d722db0d5e06396_MD5.jpeg]]

#### Call 属性
`Call`方法，每当调用该函数时都会调用该方法，来创建自己的 Function Execution Context


#### Environment 属性
_`Environment`指向声明该函数的外部的 environment record，是实现闭包的基础，应是暂存然后赋值给environment record的OuterEnv_；


### 创建阶段（执行上下文的创建阶段）
- 执行`Call`方法：创建自己的 Function Execution Context
- 为函数内部的变量、参数，分配内存空间，未初始化值，执行阶段才赋值
- 为函数内部的声明函数，分配内存空间，初始化、创建函数对象（这就是声明函数可以提前被使用的原因）

### 执行阶段（执行上下文的执行阶段）



## ai 总结

该视频主要讲解 JavaScript 中执行上下文相关知识，通过示例代码展示其创建和执行过程，还介绍了提升、作用域链和闭包等概念，具体内容如下：

1. **执行上下文** ：加载脚本或调用函数时，会创建执行上下文并推送到调用栈。执行上下文定义代码执行环境，包含多个内部组件，用于跟踪代码执行流程，通过环境记录维护标识符绑定。（00:00:00 - 00:00:15）
2. **全局执行上下文创建阶段（00:00:47 - 00:03:56）**
   - **相关组件（00:01:14 - 00:03:56）**：包含领域（realm）、词法环境（lexical environment）和变量环境（variable environment）等组件。领域是代码运行的隔离环境，由内在函数、全局对象、全局环境记录等构成。全局对象有规范定义、主机定义和用户定义属性。全局环境记录管理标识符绑定，包含对象记录和声明性记录，还存储`this`关键字的值，其外部环境属性在全局时为`null`。词法环境指向除`VAR`变量外的环境记录，变量环境指向存储`VAR`变量绑定的环境记录，在全局执行上下文下，二者都指向全局环境记录。
   - **代码解析（00:03:59 - 00:05:41）**：在创建阶段解析代码，遇到`const`声明的变量`first name`和`let`声明的变量`last name`，它们使用词法环境和声明性记录处理标识符绑定，变量被提升但未初始化。函数声明`greet`由对象记录管理，在创建阶段初始化，创建新函数对象，包含指向声明环境的`environment`属性和`call`方法。
3. **全局执行上下文执行阶段（00:06:17 - 00:08:54）**：全局执行上下文添加到调用堆栈并执行，`first name`和`last name`变量初始化。调用`greet`函数时，创建新的函数执行上下文，其词法环境包含新的函数环境记录，管理函数内参数、变量和函数声明的标识符绑定，外部环境指向全局环境记录。函数参数`name`立即初始化，`const`声明的`full name`变量添加到函数环境记录但未初始化。执行阶段，`full name`变量通过作用域链找到`last name`变量的值，函数返回结果后，函数执行上下文从调用堆栈移除，全局执行上下文执行完也从调用堆栈移除。
4. **相关概念（00:09:04 - 00:11:22）**
   - **提升（00:09:04 - 00:09:58）**：发生在执行上下文创建阶段，`const`、`let`声明的变量、类和导入被提升但未初始化，访问会报错，此区域为临时死区；`VAR`声明的变量提升且初始化为`undefined`，执行阶段重新定义；函数在创建阶段就用实际函数对象初始化，可在声明前调用。
   - **作用域链（00:10:13 - 00:10:29）**：通过环境记录的`outer`属性实现，当访问当前环境记录不存在的属性时，引擎遍历作用域链查找绑定。
   - **闭包（00:10:29 - 00:11:22）**：内部函数引用外部函数的环境记录时形成，通过函数对象的`environment`属性实现。修改代码返回内部函数对象后，调用时新执行上下文的函数环境记录的`outer`属性指向外部函数环境记录，内部函数可访问外部函数的变量。
