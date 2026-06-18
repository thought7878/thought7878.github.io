好了，数据收集完毕。以下是根据你的偏好（全栈、有一定难度的 Bug 修复、1-3天工作量）筛选出的推荐列表。

---

# shadcn-ui 适合提交 PR 的 Issue 推荐列表

> 筛选标准：Open 状态、无人认领、有明确复现步骤、有可定位的修复范围、适合全栈开发者

---

## 1. [#10549] Sidebar + 宽内容导致页面水平滚动条

| 项目 | 详情 |
|------|------|
| **链接** | [Issue #10549](https://github.com/shadcn-ui/ui/issues/10549) |
| **难度** | ⭐⭐ 中等 |
| **预估工作量** | 0.5-1 天 |
| **技术栈** | React + Tailwind CSS + 布局系统 |
| **评论数** | 3（有社区讨论） |
| **认领状态** | 无 assignee |

**问题描述**：当 Sidebar（非 dialog 模式）配合宽内容（如表格）时，整个页面出现不期望的水平滚动条。根因是 `SidebarInset` 没有限制水平溢出。

**修复要点**：
- 在 `SidebarInset` 组件中添加 `overflow-x-hidden`
- 涉及文件：`apps/v4/registry/new-york-v4/ui/sidebar.tsx` 及所有 style 变体（base-*/radix-* 共 18 个副本）
- 需确保不影响内部子容器的 `overflow-x-auto`

**推荐理由**：Issue 描述清晰，已有明确的根因分析和建议修复方案，修复范围明确。涉及多个 style 变体的同步修改，适合展示对项目的系统性理解。

---

## 2. [#10934] Base UI Data Table 文档错误使用 Radix `asChild` 属性

| 项目 | 详情 |
|------|------|
| **链接** | [Issue #10934](https://github.com/shadcn-ui/ui/issues/10934) |
| **难度** | ⭐⭐ 中等 |
| **预估工作量** | 1 天 |
| **技术栈** | React + Base UI vs Radix UI API 差异 + MDX 文档 |
| **评论数** | 0 |
| **认领状态** | 无 assignee |

**问题描述**：Base UI 版本的 Data Table 文档中，`DropdownMenuTrigger` 使用了 Radix 风格的 `asChild` 属性，而 Base UI 应使用 `render` 属性。文档与实际 API 不一致。

**修复要点**：
- 修改 `apps/v4/content/docs/components/base/data-table.mdx` 中的代码片段
- 将 `DropdownMenuTrigger asChild` 替换为 Base UI 的 `render` 用法
- 参考 `apps/v4/content/docs/components/base/dropdown-menu.mdx` 中的正确写法
- 确保所有 Base UI 上下文中的示例一致

**推荐理由**：问题定位精确，涉及 Base UI 过渡期的核心 API 差异。修复需要理解 Radix 和 Base UI 的组件组合模式差异，有一定技术深度。

---

## 3. [#10665] Item 组件 Extra Small 示例缺少 Description

| 项目 | 详情 |
|------|------|
| **链接** | [Issue #10665](https://github.com/shadcn-ui/ui/issues/10665) |
| **难度** | ⭐⭐ 中等 |
| **预估工作量** | 1 天 |
| **技术栈** | React + TypeScript + 组件 Props |
| **评论数** | 4 |
| **提交者** | Wolfr（活跃社区成员，CONTRIBUTOR） |
| **认领状态** | 无 assignee |

**问题描述**：在 `/create` 页面的 Item 组件 "Outline - Extra small" 示例中，所有 "+ Description" 标记实际上并没有渲染 description 内容。所有 style 变体都受影响，可能是缺少了某个 prop 传递。

**修复要点**：
- 检查 Item 组件示例中 description prop 的传递逻辑
- 涉及 `apps/v4/registry/` 下的 Item 示例文件
- 需要对比正常渲染 description 的示例与 Extra small 示例的差异
- 可能需要在示例数据中添加缺失的 description 字段

**推荐理由**：问题有截图佐证，影响范围明确（所有 style 的 Extra small 变体），修复需要追踪 prop 传递链路，有一定调试深度。

---

## 4. [#10910] /create 页面左侧面板选择项闪烁

| 项目 | 详情 |
|------|------|
| **链接** | [Issue #10910](https://github.com/shadcn-ui/ui/issues/10910) |
| **难度** | ⭐⭐⭐ 中高 |
| **预估工作量** | 1-2 天 |
| **技术栈** | React 状态管理 + Next.js App Router + UI 交互 |
| **评论数** | 2 |
| **认领状态** | 无 assignee |

**问题描述**：`/create` 页面左侧配置面板中，选择 Base Color、Theme 等选项时，选中状态会闪烁并重置，无法正常保持选中。疑似状态被反复重置或重新渲染。

**修复要点**：
- 定位 `/create` 页面的状态管理逻辑（`apps/v4/app/(app)/create/`）
- 检查是否存在不必要的 re-render 导致状态重置
- 可能是 controlled/uncontrolled 组件混用、或 useEffect 依赖循环导致
- 需要理解 URL 参数与组件状态的同步机制

**推荐理由**：这是一个真实的 React 状态管理 bug，修复需要深入理解 Next.js App Router 的状态同步机制，技术挑战性较高，适合展示 React 调试能力。

---

## 5. [#10742] Carousel 滚动偶尔不工作

| 项目 | 详情 |
|------|------|
| **链接** | [Issue #10742](https://github.com/shadcn-ui/ui/issues/10742) |
| **难度** | ⭐⭐⭐ 中高 |
| **预估工作量** | 1-3 天 |
| **技术栈** | React + Embla Carousel 集成 + 事件处理 |
| **评论数** | 1+ |
| **认领状态** | 无 assignee |

**问题描述**：Carousel 组件的滚动功能偶尔失效，属于间歇性 bug。

**修复要点**：
- 检查 Carousel 与 Embla Carousel 的集成代码
- 关注边界条件下的事件处理（快速点击、边界滚动等）
- 可能涉及 `apps/v4/registry/new-york-v4/ui/carousel.tsx`
- 需要排查 race condition 或事件监听器生命周期问题

**推荐理由**：间歇性 bug 的排查和修复是展示调试能力的好机会。需要理解第三方库（Embla）的 API 和生命周期，有一定挑战性。

---

## 综合对比

| Issue | 难度 | 工作量 | 影响面 | 被接受概率 |
|-------|------|--------|--------|-----------|
| **#10549** Sidebar 水平滚动 | ⭐⭐ | 0.5-1天 | 所有使用 Sidebar 的用户 | 高（有明确修复方案） |
| **#10934** Base UI 文档 API 不一致 | ⭐⭐ | 1天 | Base UI 用户 | 高（文档准确性问题） |
| **#10665** Item 示例缺 Description | ⭐⭐ | 1天 | /create 页面用户 | 中高 |
| **#10910** /create 面板闪烁 | ⭐⭐⭐ | 1-2天 | /create 页面用户 | 中（需定位根因） |
| **#10742** Carousel 滚动失效 | ⭐⭐⭐ | 1-3天 | Carousel 用户 | 中（间歇性 bug 难验证） |

---

## 建议

**首选 #10549（Sidebar 水平滚动）**：
- 问题描述最清晰，已有根因分析和建议修复方案
- 修复范围明确，风险低
- 对实际用户有直接帮助
- 适合快速完成并获得 PR 合并

**次选 #10934（Base UI 文档 API 不一致）**：
- 涉及 Base UI 过渡期的核心问题，项目维护者会重视
- 修复需要理解 Radix vs Base UI 的 API 差异，能展示技术深度

开始之前，建议先在对应 issue 下评论表达意愿（如 "I'd like to work on this"），避免与他人重复工作。需要我帮你分析某个具体 issue 的代码修复方案吗？