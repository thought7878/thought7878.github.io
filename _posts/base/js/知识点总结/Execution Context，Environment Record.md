[YouTube视频](https://www.youtube.com/watch?v=zdGfo6I1yrA&ab_channel=LydiaHallie)
[b站视频](https://www.bilibili.com/video/BV16w4m197PV/?spm_id_from=333.999.0.0&vd_source=22af953ea4c09540ad1966711a2d53f0)


- [00:00](https://www.bilibili.com/video/BV16w4m197PV/?t=0.108247#t=0.11) 视频简介
- 
- [00:11](https://www.bilibili.com/video/BV16w4m197PV/?t=11.645425#t=11.65) Execution Context Overview
	- 执行上下文是什么？执行上下文本质上定义了代码执行的环境。包含很多内部组件，JS引擎使用这些组件来跟踪代码段的执行流程，执行上下文使用environment record来跟踪和维护为变量、函数声明创建的标识符（绑定）的值
	- Global Execution Context 由3部分组成：
		- Realm
		- LexicalEnvironment
		- VariableEnvitonment
![[base/js/知识点总结/media/6b7a4dcca94cfe94d321b3afa926a111_MD5.png]]
- [01:24](https://www.bilibili.com/video/BV16w4m197PV/?t=84.613012#t=01:24.61) Realm
	- realm本质上是一个隔离的环境，代码在其中运行
	- 由三部分组成：
		- `Intrinsics/内置函数`：包含内置的对象和函数
		- `Global Object` [01:59](https://www.bilibili.com/video/BV16w4m197PV/?t=119.898017#t=01:59.90) 
			- 保存/暴露Intrinsics
			- `宿主属性/Host Properties`： document/localStorage/fetch/setTimeout/history
			- `自定义属性/User properties`：全局声明的函数/var声明的变量
		- `Global Environment Record`：管理该上下文中的变量（identifier bingdings:标识符和值的绑定） [02:45](https://www.bilibili.com/video/BV16w4m197PV/?t=165.269302#t=02:45.27)
			- `ObjectRecord`：对Global Object的直接引用
			- `DeclarativeRecord`：保存let/const变量，不是var和函数声明的变量
			- `GlobalThisValue`：全局的this [03:29](https://www.bilibili.com/video/BV16w4m197PV/?t=209.731061#t=03:29.73) 
			- `OuterEnv`：保存/指向外部的声明该函数的environment record，函数的Environment属性
- [03:57](https://www.bilibili.com/video/BV16w4m197PV/?t=237.500098#t=03:57.50) LexicalEnvironment / VariableEnvironment：
- [04:26](https://www.bilibili.com/video/BV16w4m197PV/?t=266.597824#t=04:26.60) 例子解释
	- [05:41](https://www.bilibili.com/video/BV16w4m197PV/?t=341.015584#t=05:41.02) **函数的create阶段**；函数的两个属性：Environment/Call。Environment指向声明该函数的environment record；Call方法，每当调用该函数时都会调用该方法
	- [06:16](https://www.bilibili.com/video/BV16w4m197PV/?t=376.839284#t=06:16.84) **函数的execute阶段**：
		- [09:00](https://www.bilibili.com/video/BV16w4m197PV/?t=540.666144#t=09:00.67) 变量提升 Hoisting
		- [10:11](https://www.bilibili.com/video/BV16w4m197PV/?t=611.420751#t=10:11.42) **作用域链** Scope Chain
		- [10:27](https://www.bilibili.com/video/BV16w4m197PV/?t=627.557933#t=10:27.56) **闭包** Closures