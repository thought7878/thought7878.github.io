使用 pnpm workspace 管理 monorepo
https://www.bilibili.com/video/BV1qg4y157dv/?spm_id_from=333.788.top_right_bar_window_history.content.click&vd_source=22af953ea4c09540ad1966711a2d53f0

- [02:54](https://www.bilibili.com/video/BV1qg4y157dv/?t=174.441418#t=02:54.44) pnpm简介
- What：
	- 快速的节省磁盘空间的包管理工具
- Why/优点：
	- 快速：pnpm会将package保存到全局统一的目录，减少二次安装需要的时间
	- 节省磁盘空间：它会全局保存一份package，把项目需要的package软连接到保存的package，不需要像npm安装多份
	- 节省网络带宽：同上
	- 更好的依赖处理逻辑

- [05:13](https://www.bilibili.com/video/BV1qg4y157dv/?t=313.731749#t=05:13.73) monorepo简介
- What：
	- 在一个大的代码仓库中，管理多个不同的项目/package，这个大的代码仓库叫做monorepo。mono，单一/单个；repo，repository，仓库/存放处
	- 