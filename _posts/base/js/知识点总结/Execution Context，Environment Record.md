[YouTube视频](https://www.youtube.com/watch?v=zdGfo6I1yrA&ab_channel=LydiaHallie)
[b站视频](https://www.bilibili.com/video/BV16w4m197PV/?spm_id_from=333.999.0.0&vd_source=22af953ea4c09540ad1966711a2d53f0)


- [00:00](https://www.bilibili.com/video/BV16w4m197PV/?t=0.108247#t=0.11) 视频简介
- 
- [00:11](https://www.bilibili.com/video/BV16w4m197PV/?t=11.645425#t=11.65) Execution Context Overview
	- 执行上下文是什么？执行上下文本质上定义了代码执行的环境。包含很多内部组件，JS引擎使用这些组件来跟踪代码段的执行流程，执行上下文使用environment record来跟踪和维护为变量、函数声明创建的标识符（绑定）的值
	- 
- [01:24](https://www.bilibili.com/video/BV16w4m197PV/?t=84.613012#t=01:24.61) Realm
	- realm本质上是一个隔离的环境，代码在其中运行
	- 由三部分组成：
		- Intrinsics/内置函数：包含内置的对象和函数
		- Global Object
			- 保存/暴露Intrinsics
			- Host Properties： document/localStorage/fetch/setTimeout/history
			- 自定义属性/User properties：全局声明的函数/var声明的变量
		- Global Execution Context [02:45](https://www.bilibili.com/video/BV16w4m197PV/?t=165.269302#t=02:45.27)
			- Global Object：对Global Object的直接引用
			- DeclarativeRecord：保存let/const变量