

本视频讲述了**成为React contributor的好处、提交PR的完整流程**，包括*Fork项目、克隆本地、运行测试、提交修改、创建Pull Request、等待自动化检查与合并结果*，并说明了**如何处理多次修改和多分支贡献**。

![](https://img-view-c-sz.drive.quark.cn/9Oete2Z1/4787011611/9ed604526e9c4a0eba5157152951ca4768682be1/68682be1552275f972924c23a6ef14509bfab84d/preview?auth_key=1767694718-1-10800-707ffca7f352fca80231866c53829d5a&sp=100&token=5-c958601b5eb547522c489024c079a75a-0-3-1024-ca4b14e51fb8404080c666094689f357-0-0-0-0-1767683918709-d87d497b493c81339c85ef4f2ebbd4e4&ork=b229vlT8Q156Py2J274GobRPQL0v5lIlT9d77yhwC&ud=24-0-3-N-N-N-11-N-1-N-0-N-12-N-N&dfi=152)

### 成为React Contributor的好处 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)

- **证明个人能力**  
    参与React等知名开源项目能体现个人源码能力和学习能力。
- **简历加分**  
    若修改的是重要代码部分，可为简历增色；核心成员身份更具优势。
- 展示自我  
    *提交记录可在GitHub commit历史中被他人查看*，有助于建立技术影响力。
- 实际案例分享  
    发言人曾因提交PR被开源项目组和国外团队联系，即使PR未被合并也可能获得关注。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第3章 学习前：底层源码的高效学习方法分享/media/3abda508db6f9888b9a2924eb7372591_MD5.webp]]

PR未被接受的实例分析：
[01:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=86)

- 修改内容描述  
    提议删除硬编码值 `hasTimeRemaining: true`，认为该字段无实际用途且增加讲解负担。
- 团队反馈  
    PR被关闭，理由是“硬编码”，但后续其他核心成员自行删除了相同代码。
- 结论总结  
    开源社区存在不同意见，PR是否通过取决于维护者判断，但提交行为本身仍可能带来机会。

### 提交PR的标准流程 
[02:30](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=150)

- **第一步：Fork项目到个人账户** [02:45](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=165)  
    不能直接在 `facebook/react` 上修改，需先Fork一份至自己的GitHub账号下。
- **第二步：克隆到本地** [03:17](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=197)  
    将Fork后的仓库克隆到本地进行开发与测试。
- **第三步：本地修改** [03:48](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=228)  
    
- **第四步：运行测试** [03:57](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=237)  
    推荐不运行全部测试（`npm run test`），选择性运行测试用例，*运行指定模块的测试以提高效率*，例如：
    - 运行 `schedule/src/__tests__` 下的测试。
    - 分开运行多个相关测试更快。
- **第五步：本地提交更改，推送到远程** [04:39](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=279)  
    修改完成后，在本地Git分支提交commit。
    - *提交信息*需清晰说明修改位置、原因。
    - *复杂修改*应附带新增测试用例。
    - Bug修复需提供复现步骤。
- **第六步：创建PR（Pull Request）** [05:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=323)
    - 刷新个人项目页面，点击“Compare & pull request”按钮。
    - *填写详细的PR描述*，包括*变更目的、影响范围、是否包含breaking change等*。
    - ![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第3章 学习前：底层源码的高效学习方法分享/media/804709bf3dfc8448bbb2c87f8d6980d1_MD5.webp]]
- **第七步：等待CI自动化检查** [06:19](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=379)  
    系统自动运行CI流水线，*进行`代码检查`（格式检查、语法检查、类型检查）和`测试验证`*。
    - 若检查*失败*，可根据日志排查问题。
    - 有时失败非本人责任，可能是*CI环境临时故障*，由团队修复后可自动通过。
    - ![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第3章 学习前：底层源码的高效学习方法分享/media/bce417fa6863ec108f4dbe54d4717826_MD5.webp]]
    - 检查通过后（状态由黄变绿）：
    - ![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第3章 学习前：底层源码的高效学习方法分享/media/7fcd73ffdd05e4e8c6f0a8a300b5c3f0_MD5.webp]]
- **第八步：等待维护者审核与合并** [07:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=429)
    - 检查通过后（状态由黄变绿），*进入人工审核阶段*。
    - 可能被合并（merge），也可能被拒绝（close）。
    - 合并后会收到邮件通知，*可在主仓库commit记录中查到贡献*。

### 查看贡献记录的方式 
[08:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=481)

- 方式一：代码文件中的作者标记 [08:06](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=486)  
    在本地或GitHub代码文件上悬停，可看到某行代码的最后修改者。
- 方式二：主仓库commit历史 [08:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=509)  
    访问 `facebook/react` 的commits页面，查找自己用户名下的提交记录。
    - 示例：删除fiber中废弃类型的提交已被合并。
- 方式三：PR状态跟踪 [08:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=531)
    - 已合并的PR显示为merged。
    - 被关闭的PR如 #25547 显示为closed，并注明原因。

### 管理多个提交与分支 
[09:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=554)

- **在同一PR中追加提交，是同一个PR** [09:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=577)
    - 只需在原分支继续提交新commit，这些**将自动附加到同一PR中**。
    - 即使原PR已close，也可通过打开新PR复用分支。
- **创建多个独立PR** [10:43](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=643)
    - 不同问题应**使用不同分支分别提交**，便于维护者审查与合并。
    - 创建新分支方法：
        - 使用命令行：`git checkout -b feature/new-fix`
        - 使用GUI工具切换分支。
    - **每个分支对应一个独立PR**，实现职责分离。

### 课程总结与鼓励 
[11:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=694)

- 回顾重点  
    全面讲解了成为React contributor的意义与具体操作流程。
- 鼓励实践  
    鼓励学员*主动寻找React项目中的可改进点（“找找茬”），提交有意义的PR来检验学习成果*。
- 提供支持  
    学员在过程中遇到问题可联系讲师寻求帮助。