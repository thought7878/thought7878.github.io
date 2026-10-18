# 《43丨以VS Code为例，看大型开源项目是如何应用软件工程的？》总结

> 来源：本专栏第 43 讲 · 经典案例解析篇

## 一句话结论

通过观察 VS Code 的开发过程组织、团队分工、各阶段运作和工具使用，印证软件工程"对开发过程的组织、方法的运用、工具的使用"核心，并可借鉴到自己的项目。

## 4 周迭代开发过程

| 周次 | 主要工作 |
| --- | --- |
| 第一周 | 偿还技术债务（重构、优化性能）、修复上版本紧急 Bug、讨论制定下一迭代计划 |
| 第二、三周 | 按计划开发新功能、修 Bug，全部通过 GitHub Issue 分配跟踪，线上紧急 Bug 优先 |
| 第四周 | End game（测试周）：只做测试和修 Bug，更新文档、写 Release Notes，发布预发布版本 |
| 下一迭代第一周 | 周三左右正式发布上一迭代完成的版本 |

启示：VS Code 稳定的 4 周迭代证明"小团队缩短到 2-4 周迭代"完全可行，是其保持月月更新的关键。

## 团队角色与分工

- 团队约 20 人，苏黎世和西雅图各一半，几乎全是开发，结构扁平，按模块侧重分工（如 Git 功能、编辑器功能）。
- **Inbox Tracker**（轮值）：收集、验证、跟踪 Bug。Issue 总量超 5000、每日新增约 100；先用 VSCodeBot 机器人自动打标签/回复，人工再处理剩余；提问类引导去 StackOverflow，Bug 打标签指派模块负责人。
- **Endgame Master**（轮值）：无专职测试，开发自测；提前制定测试计划、生成 Check List；为消除自测盲区，自己的功能让其他人测试；测试计划作为 GitHub Issue 发布审查；测试期每天在 Slack 同步进展。

## 各阶段运作要点

1. **需求收集与版本计划**：需求来自团队内部和社区（GitHub、Twitter、StackOverflow），统一用 Issue 管理；每半年/一年制定 Roadmap 公布在 WIKI；用 Milestone 关联迭代：当前迭代、On Deck（下一迭代）、Backlog、Recovery（已完成待补丁）。
2. **设计与开发**：小功能基于现有架构直接开发，大改造先写设计文档并 Review；采用 GitHub Flow：新分支开发 → PR → 核心成员审查 + 全部自动化测试通过才能合并；CI 用 Azure DevOps，每次提交在 Windows/Linux/macOS 三系统跑测试；约 4581 个单元测试（1 分多钟跑完）、466 个集成测试（约 3 分钟）。
3. **测试**：按 Check List 逐一验证，修好的 Bug 在 Issue 打 verified 标签；人工测试后由 Endgame Master 跑冒烟测试。
4. **发布**：从 master 创建 release 分支（如 release/1.10）；Bug 修复同时合并 master 和 release 分支，每次改动重跑冒烟测试；Release 代码修改后 24 小时内不发布，先发预发布版给约 2 万内部用户试用，24 小时无问题才发正式版；发布前由 Endgame Master 写 Release Notes；每日编译绿色 Logo 内部测试版，"吃自己狗粮"。

## 使用的工具

- 源代码管理：GitHub（托管 + 开发流程全基于 GitHub）
- 任务跟踪：GitHub Issue（Label 区分 bug / feature-request / debt 等类型和状态，Milestone 标注版本）
- 持续集成：早期 Travis CI + AppVeyor，后迁移至 Azure Pipelines
- 文档：GitHub WIKI（操作说明）+ 官网博客（技术分享）
- 自研小工具：VSCodeBot 自动处理 Issue
