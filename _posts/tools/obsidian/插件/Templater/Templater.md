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

   - 支持创建新文件、读取现有文件内容、修改文件路径。

5. **实时更新**
   - 模板生成的内容可以根据需要实时更新。

---

### 1. Templater 的基本概念

- **模板文件**：模板文件是存储在特定文件夹中的 Markdown 文件，包含静态文本和动态脚本。
- **动态脚本**：模板文件中可以嵌入 JavaScript 脚本，这些脚本会在插入模板时执行，生成动态内容。
- **触发方式**：可以通过命令面板或快捷键触发模板插入。

---

### 2. 安装和配置 Templater 插件

#### 1 安装 Templater

1. 打开 Obsidian 的设置页面。
2. 导航到 **Community plugins**（社区插件）。
3. 点击 **Browse**，搜索 **Templater**。
4. 安装并启用插件。

#### 2 配置模板文件夹

1. 在 Templater 设置中，指定模板文件的存储路径（例如：`Templates` 文件夹）。
2. 创建该文件夹，并将模板文件存储在此处。

---

### 3. 模板的基本结构

一个典型的 Templater 模板文件可能如下所示：

```markdown
---
title: <% tp.date.now("YYYY-MM-DD") %>
tags: [daily]
---

# Daily Note for <% tp.date.now("dddd, MMMM D, YYYY") %>

## Tasks

- [ ] Task 1
- [ ] Task 2

## Notes

<% tp.file.cursor() %>
```

- `<% ... %>` 是 Templater 的脚本标签，用于**嵌入动态内容**。
- `tp` 是 Templater 提供的上下文对象，用于*访问各种模板功能*。

---

### 4. 核心 API 和功能

#### 1 日期和时间

- **`tp.date.now(format)`**

  - 返回当前日期和时间，格式化为指定格式。
  - 示例：
    ```markdown
    Today is <% tp.date.now("YYYY-MM-DD") %>.
    ```

- **`tp.date.yesterday(format)`**

  - 返回昨天的日期。
  - 示例：
    ```markdown
    Yesterday was <% tp.date.yesterday("YYYY-MM-DD") %>.
    ```

- **自定义日期偏移**
  - 使用 `tp.date.now().add()` 或 `tp.date.now().subtract()` 来调整日期。
  - 示例：
    ```markdown
    Next week is <% tp.date.now().add(7, "days").format("YYYY-MM-DD") %>.
    ```

#### 2 文件操作

- **`tp.file.create(path, content)`**

  - 创建一个新文件。
  - 示例：
    ```javascript
    await tp.file.create(
      "Projects/new-project.md",
      "# New Project\n\nThis is a new project."
    );
    ```

- **`tp.file.rename(newName)`**

  - 重命名当前文件。
  - 示例：
    ```javascript
    await tp.file.rename("New Title");
    ```

- **`tp.file.move(newPath)`**
  - 移动当前文件到新的路径。
  - 示例：
    ```javascript
    await tp.file.move("Archive/old-note.md");
    ```

#### 3 用户输入

- **`tp.system.prompt(message, defaultValue)`**

  - 弹出一个输入框，提示用户输入内容。
  - 示例：
    ```javascript
    const projectName = await tp.system.prompt(
      "Enter project name:",
      "Default Project"
    );
    ```

- **`tp.system.suggester(options, values)`**
  - 提供一个下拉选择框，让用户从选项中选择。
  - 示例：
    ```javascript
    const priority = await tp.system.suggester(
      ["High", "Medium", "Low"],
      ["high", "medium", "low"]
    );
    ```

#### 4 光标位置

- **`tp.file.cursor()`**
  - 在模板中插入一个占位符，表示光标的位置。
  - 示例：
    ```markdown
    Write your notes here: <% tp.file.cursor() %>
    ```

#### 5 随机数和字符串

- **`tp.user.random(min, max)`**

  - 生成一个随机整数。
  - 示例：
    ```javascript
    const randomNum = tp.user.random(1, 100);
    ```

- **`tp.user.randomString(length)`**
  - 生成一个随机字符串。
  - 示例：
    ```javascript
    const randomStr = tp.user.randomString(8);
    ```

#### 6 条件判断和循环

- **条件判断**

  - 可以使用普通的 JavaScript 条件语句。
  - 示例：
    ```javascript
    const timeOfDay = tp.date.now("H") < 12 ? "Morning" : "Afternoon";
    ```

- **循环**
  - 使用 `for` 或 `while` 循环。
  - 示例：
    ```javascript
    const items = ["Apple", "Banana", "Cherry"];
    for (const item of items) {
      tR += `- ${item}\n`;
    }
    ```

---

### 5. 高级用法示例

#### 1 每日笔记模板

以下是一个典型的每日笔记模板，包含日期、任务列表和动态内容：

```markdown
---
title: <% tp.date.now("YYYY-MM-DD") %>
tags: [daily]
---

# Daily Note for <% tp.date.now("dddd, MMMM D, YYYY") %>

## Tasks

- [ ] Task 1
- [ ] Task 2

## Notes

<% tp.file.cursor() %>
```

#### 2 项目初始化模板

以下模板会根据用户输入创建一个新的项目文件，并填充初始内容：

```markdown
<%
const projectName = await tp.system.prompt("Enter project name:");
const filePath = `Projects/${projectName}.md`;
await tp.file.create(filePath, `# ${projectName}\n\n- Start Date: ${tp.date.now("YYYY-MM-DD")}\n- Status: Not Started`);
%>

Project file created: [[<%= filePath %>]]
```

#### 3 动态任务生成

以下模板会根据用户输入的任务数量生成多个任务条目：

```markdown
<%
const taskCount = parseInt(await tp.system.prompt("How many tasks?", "3"));
let tasks = "";
for (let i = 1; i <= taskCount; i++) {
tasks += `- [ ] Task ${i}\n`;
}
%>

## Tasks

<%= tasks %>
```

---

### 6. 注意事项

- **脚本错误**：如果模板中的脚本有语法错误或逻辑问题，可能会导致插入失败。建议在简单的环境中测试脚本。
- **性能优化**：避免在模板中使用过多的复杂逻辑，尤其是涉及大量文件操作时。
- **安全性**：由于 Templater 允许运行 JavaScript，务必小心处理外部数据，避免引入潜在的安全风险。

---


### 相关资料

- [Obsidian 达人成长之路 #5：使用 Templater 减少重复工作，提高效率](https://juejin.cn/post/7381347654767804425)
- [Templater 官方文档](https://silentvoid13.github.io/Templater/)
- Templater 社区论坛和插件示例库