### Templater 插件简介

**Templater** 是 Obsidian 的一款强大社区插件，用于创建和插入动态模板。它允许用户*通过预定义的模板快速生成笔记内容*，并支持变量、脚本和自动化操作。Templater 的灵活性使其成为*知识管理、任务跟踪、日常记录*的强大工具。

与 Obsidian 的原生模板插件相比，Templater 提供了更高级的功能，如动态变量、条件语句、循环、日期格式化以及执行 JavaScript 脚本的能力。

---

### Templater 插件的核心功能

1. **动态模板**
   - 支持插入动态变量（如日期、时间、用户输入等）。
   - 可以根据当前上下文自动生成内容。

2. **脚本支持**
   - 允许在模板中嵌入 JavaScript 代码，实现复杂的逻辑和自动化操作。

3. **条件语句和循环**
   - 支持 `if` 条件语句和 `for` 循环，用于生成动态内容。

4. **文件操作**
   - 支持创建新文件、读取现有文件内容或修改文件路径。

5. **实时更新**
   - 模板生成的内容可以根据需要实时更新。

---

### 安装和启用 Templater 插件

1. 打开 Obsidian 设置 -> “社区插件”。
2. 点击“浏览”，搜索“Templater”。
3. 安装并启用插件。
4. （可选）在设置中配置模板文件夹路径和其他选项。

---

### 使用 Templater 插件的基本方法

#### 1. 创建模板文件
在指定的模板文件夹中创建一个新的 Markdown 文件（如 `daily-template.md`），并在其中定义模板内容。例如：
```markdown
---
title: {{date("YYYY-MM-DD")}}
tags:
  - 日记
  - 每日记录
---

# 每日日记

## 今日目标
- [ ] {{input("今日主要任务")}}

## 笔记
{{cursor}}

## 总结
{{input("今日总结")}}
```

#### 2. 插入模板
在需要生成内容的笔记中，运行 Templater 插件并选择模板文件。插件会根据模板生成动态内容。

---

### 常见用法示例

#### 示例 1：每日日记模板
以下是一个典型的每日日记模板：
```markdown
---
date: {{date("YYYY-MM-DD")}}
mood: {{input("今天的心情如何？")}}
weather: {{input("今天的天气如何？")}}
---

# 每日日记 - {{date("YYYY年MM月DD日")}}

## 今日任务
- [ ] {{input("任务1")}}
- [ ] {{input("任务2")}}

## 笔记
{{cursor}}

## 总结
{{input("今日总结")}}
```
- 使用 `{{date()}}` 自动生成日期。
- 使用 `{{input()}}` 提示用户输入内容。
- 使用 `{{cursor}}` 定义光标位置。

#### 示例 2：项目计划模板
以下是一个项目计划模板：
```markdown
---
project: {{input("项目名称")}}
start_date: {{date("YYYY-MM-DD")}}
end_date: {{date("YYYY-MM-DD", "7 days")}}
status: 进行中
priority: {{input("优先级（高/中/低）")}}
---

# 项目计划 - {{project}}

## 基本信息
- 开始日期：{{start_date}}
- 结束日期：{{end_date}}
- 优先级：{{priority}}

## 任务列表
- [ ] {{input("任务1")}}
- [ ] {{input("任务2")}}

## 备注
{{cursor}}
```
- 使用 `{{date("YYYY-MM-DD", "7 days")}}` 自动生成未来日期。

#### 示例 3：动态生成任务清单
以下模板会根据用户输入的任务数量动态生成任务清单：
```markdown
# 动态任务清单

## 任务
<%*
let taskCount = parseInt(await tp.system.prompt("请输入任务数量"));
for (let i = 1; i <= taskCount; i++) {
    tR += `- [ ] 任务${i}\n`;
}
_%>
```
- 使用 `tp.system.prompt()` 获取用户输入。
- 使用 `for` 循环动态生成任务。

#### 示例 4：嵌入 JavaScript 脚本
以下模板使用 JavaScript 脚本生成随机数：
```markdown
# 随机数生成器

今天的幸运数字是： <% tp.user.getRandomNumber() %>

<%*
function getRandomNumber() {
    return Math.floor(Math.random() * 100);
}
_%>
```
- 使用 `<% ... %>` 嵌入 JavaScript 代码。

---

### 高级用法

#### 1. 自定义函数
你可以在模板中定义自己的 JavaScript 函数，并在多个模板中复用。例如：
```markdown
<%*
function formatDate(date) {
    return date.toLocaleDateString();
}
_%>

今天的日期是： <% tp.user.formatDate(new Date()) %>
```

#### 2. 文件操作
Templater 支持*创建*新文件或*读取*现有文件内容。例如：
```markdown
<%*
await tp.file.create_new("新笔记", "新笔记标题");
_%>
```
- 使用 `tp.file.create_new()` 创建新文件。

#### 3. 条件语句
以下模板根据用户输入显示不同的内容：
```markdown
<%*
let mood = await tp.system.suggester(["开心", "一般", "不开心"], ["😊", "😐", "😢"]);
if (mood === "😊") {
    tR += "今天心情很好！继续保持！";
} else if (mood === "😐") {
    tR += "今天心情一般，尝试调整一下状态吧！";
} else {
    tR += "今天心情不好，记得给自己一些放松的时间。";
}
_%>
```
- 使用 `tp.system.suggester()` 提供选项选择。

---

### 与其他插件的结合

1. **Dataview 插件**
   - 使用 Templater 自动生成带有元数据的笔记，方便 Dataview 查询和统计。

2. **Kanban 插件**
   - 使用 Templater 自动生成看板卡片或任务列表。

3. **Style Settings 插件**
   - 使用自定义 CSS 美化 Templater 生成的内容。

---

### 注意事项

1. **学习曲线**
   - Templater 的高级功能（如 JavaScript 脚本）需要一定的编程基础。

2. **性能问题**
   - 如果模板过于复杂或包含大量脚本，可能会导致性能下降。

3. **备份数据**
   - 在使用文件操作功能时，建议定期备份笔记数据，以防意外修改。

---

### 总结

Templater 插件是 Obsidian 中一款功能强大的模板工具，能够帮助用户快速生成动态内容，并支持复杂的逻辑和自动化操作。无论是日常记录、任务管理还是知识整理，Templater 都能显著提升效率。

**最终答案**：Templater 插件通过动态变量、条件语句、循环和 JavaScript 脚本支持，帮助用户高效创建和管理动态模板，适合任务跟踪、知识管理和自动化操作等场景。

### 相关资料
- [Obsidian 达人成长之路 #5：使用 Templater 减少重复工作，提高效率](https://juejin.cn/post/7381347654767804425)