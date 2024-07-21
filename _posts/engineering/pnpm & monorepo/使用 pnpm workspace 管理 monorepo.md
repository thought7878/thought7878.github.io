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
- [10:04](https://www.bilibili.com/video/BV1qg4y157dv/?t=604.535446#t=10:04.54) monorepo的优势
	- 便于管理多个**互相依赖**的项目
	- 便于团队共享知识库
	- 减少项目管理的成本
- 
- [11:46](https://www.bilibili.com/video/BV1qg4y157dv/?t=706.013384#t=11:46.01) monorepo的劣势
	- 版本管理混乱
	- 代码质量参差不齐，互相影响
	- 技术栈升级困难
	- 难以进行权限管理
- 
- [14:20](https://www.bilibili.com/video/BV1qg4y157dv/?t=860.034032#t=14:20.03) 使用场景、什么时候使用
	- 充分条件：
		- 多个项目互相依赖
		- 功能、版本之间存在强关联
		- 项目中存在多个编译入口，且构建条件存在差异
	- 次要条件：
		- 希望在团队中共享知识
		- 降低项目管理的成本
- 