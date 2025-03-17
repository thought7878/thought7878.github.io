---
title: Callout
created: 2025-03-17 09:55
updated: 2025-03-17 09:55
tags:
  - 学习/obsidian
---


在 Obsidian 中，**Callout** 是一种特殊的格式化块，用于突出显示某些内容（如提示、警告、重要信息等）。它可以帮助你更清晰地组织笔记，并通过视觉上的区分来增强可读性。Callout 功能在 Obsidian 的 Markdown 编辑器中非常实用，尤其是在需要分类展示信息时。

以下是对 Obsidian Callout 的详细介绍和使用方法：

---

### **1. Callout 的基本语法**

Callout 的语法基于 Markdown 的代码块（```），并结合特定的关键字和标题。以下是基本结构：

```markdown
> [!keyword] 标题（可选）
> 内容
```

- **`[!keyword]`**：指定 Callout 的类型（如提示、警告等）。
- **标题**：可选，用于为 Callout 添加一个简短的描述。
- **内容**：Callout 的具体内容。

---

### **2. 常见的 Callout 类型**

Obsidian 支持多种预定义的 Callout 类型，每种类型都有不同的颜色和图标，适用于不同场景。以下是一些常见的 Callout 类型及其用途：

| 类型关键字     | 图标与颜色       | 用途                          |
|----------------|------------------|-------------------------------|
| `note`        | 📝 蓝色         | 提示或一般性说明              |
| `abstract`    | 📑 灰色         | 摘要或概述                    |
| `info`        | ℹ️ 蓝色         | 信息性内容                    |
| `tip`         | 💡 绿色         | 技巧或建议                    |
| `success`     | ✅ 绿色         | 成功消息或完成事项            |
| `question`    | ❓ 蓝色         | 疑问或待解答的问题            |
| `warning`     | ⚠️ 黄色         | 警告或需要注意的内容          |
| `failure`     | ❌ 红色         | 错误或失败信息                |
| `example`     | 📌 浅蓝色       | 示例或案例                    |
| `quote`       | 🗨️ 浅灰色      | 引用内容                      |

---

### **3. Callout 的使用示例**

#### **3.1 提示信息**
```markdown
> [!tip] 小技巧
> 使用 Obsidian 的双向链接功能可以轻松构建知识网络。
```
**效果**：
> [!tip] 小技巧
> 使用 Obsidian 的双向链接功能可以轻松构建知识网络。

---

#### **3.2 警告信息**
```markdown
> [!warning] 注意事项
> 在清理标签时，请确保不要删除重要的历史记录。
```
**效果**：
> [!warning] 注意事项
> 在清理标签时，请确保不要删除重要的历史记录。

---

#### **3.3 成功信息**
```markdown
> [!success] 完成任务
> 已成功完成每日的任务清单！
```
**效果**：
> [!success] 完成任务
> 已成功完成每日的任务清单！

---

#### **3.4 疑问信息**
```markdown
> [!question] 待解决问题
> 如何优化 Obsidian 的模板系统？
```
**效果**：
> [!question] 待解决问题
> 如何优化 Obsidian 的模板系统？

---

### **4. 自定义 Callout**

如果你对默认的 Callout 类型不满意，可以通过 CSS 片段自定义 Callout 的样式和行为。

#### **4.1 创建 CSS 片段**
1. 在 Obsidian 的 `.obsidian/snippets` 文件夹中创建一个新的 CSS 文件（如 `custom-callout.css`）。
2. 添加自定义样式。例如：
   ```css
   .callout[data-callout="custom"] {
       background-color: #f0f8ff;
       border-left: 5px solid #87ceeb;
   }
   ```
3. 启用片段：
   - 打开 Obsidian 设置 -> “外观” -> “CSS 片段”，启用刚刚创建的片段。

#### **4.2 使用自定义 Callout**
```markdown
> [!custom] 自定义标题
> 这是一个自定义样式的 Callout。
```

---

### **5. Callout 的高级用法**

#### **5.1 嵌套 Callout**
Callout 可以嵌套使用，适合复杂的信息分层展示。例如：
```markdown
> [!info] 主要信息
> 这是主要信息。
>
> > [!tip] 子提示
> > 这是嵌套的提示信息。
```

#### **5.2 结合其他 Markdown 元素**
Callout 可以与其他 Markdown 元素（如列表、代码块、图片等）结合使用。例如：
````markdown
> [!example] 示例代码
> 以下是一个 Python 函数示例：
> ```python
> def hello():
>     print("Hello, World!")
> ```
````

---

### **6. Callout 的优点**

1. **增强可读性**：
   - 通过颜色和图标区分不同类型的内容，使笔记更直观。

2. **提升效率**：
   - 快速定位重要信息或注意事项。

3. **灵活性**：
   - 支持多种类型和自定义样式，适应不同需求。

4. **兼容性**：
   - Callout 是基于 Markdown 的扩展，兼容大多数 Markdown 渲染器。

---

### **7. 注意事项**

1. **避免过度使用**：
   - 不要滥用 Callout，否则可能导致页面过于花哨，影响阅读体验。

2. **保持一致性**：
   - 使用统一的 Callout 类型和风格，避免混乱。

3. **插件支持**：
   - 如果需要更多功能，可以安装社区插件（如 **Advanced Tables** 或 **Buttons**）进一步增强 Callout 的交互性。

---

### **总结**

Callout 是 Obsidian 中一个强大且易用的功能，能够帮助用户更好地组织和展示信息。通过合理使用预定义类型和自定义样式，你可以显著提升笔记的可读性和专业性。

**最终答案**：Obsidian 的 Callout 是一种基于 Markdown 的格式化块，用于突出显示提示、警告、信息等内容。通过 `[!keyword]` 和标题的组合，可以快速创建不同类型的 Callout，增强笔记的可读性和条理性。