### 定义与背景

**Conventional Commits** 规范是一种用于**规范 Git 提交信息格式**的约定，旨在让提交信息*更加清晰、一致且具有可读性*。这种规范可以帮助开发者*更好地理解每次代码变更的意图*，便于项目的维护、版本管理和自动化工具的使用。它最早在开源社区中被广泛倡导和应用，如今已成为前端开发乃至整个软件开发领域中流行的提交信息规范。

### 提交信息格式

Conventional Commits 规范的提交信息由三个主要部分组成：*标题（Header）、正文（Body）和页脚（Footer）*，其中**标题是必需的**，正文和页脚是可选的。

#### 标题（Header）

标题是*提交信息的核心部分*，格式为：

```plaintext
<类型>[可选的作用域]: <描述>
```

##### 类型（Type）
**描述提交的主要目的**，常见的类型包括：

  - `feat`：表示*新增功能*，对应语义化版本中的小版本更新（Minor Version）。例如，在一个电商项目中，添加购物车功能的提交可以使用 `feat: add shopping cart feature`。
  - `fix`：表示*修复 bug*，对应语义化版本中的补丁版本更新（Patch Version）。比如，修复登录页面验证错误的提交可以写成 `fix: fix login validation error`。
  - `docs`：表示*对文档进行的更新*，如更新 README 文件、注释等。示例：`docs: update README.md`。
  - `style`：表示*对代码样式的调整*，不影响代码逻辑，如代码格式化、空格调整等。例如：`style: format code with Prettier`。
  - `refactor`：表示*对代码进行重构*，既不是新增功能也不是修复 bug。比如对某个函数进行性能优化或代码结构调整：`refactor: optimize user profile function`。
  - `test`：表示*添加或修改测试代码*。例如：`test: add unit tests for shopping cart`。
  - `chore`：表示*对构建过程或辅助工具等的修改*，不涉及生产代码的变更。如更新依赖包、配置文件等：`chore: update npm packages`。

##### 作用域（Scope）
*可选*部分，用于**指定提交影响的范围**，通常是一个*模块、功能或文件的名称*。例如：`feat(cart): add quantity input in shopping cart`，这里的 `cart` 就是作用域，*表示该提交影响的是购物车模块*。

##### 描述（Description）
**对提交内容的简要说明**，应该*使用祈使句、现在时，且首字母小写，结尾不要加标点*。例如：`fix: improve search performance`。


#### 正文（Body）

正文是对提交内容的详细描述，可以解释提交的动机、与之前实现的对比等。正文与标题之间需要空一行，且每行长度建议不超过 72 个字符。例如：

```plaintext
feat: add user registration feature

This commit adds a new user registration feature to the application.
It includes a registration form with email and password fields,
as well as validation and error handling.
```

#### 页脚（Footer）

页脚用于包含与提交相关的元数据，如关联的 issue 编号、重大变更说明等。常见的用法是关闭相关的 issue，格式为 `Closes #<issue-number>`。例如：

```plaintext
fix: fix product listing display issue

This commit resolves the problem where product images were not
displaying correctly on the listing page.

Closes #123
```

### 优势

- **清晰的历史记录**：遵循 Conventional Commits 规范可以使项目的提交历史更加清晰、易读，开发者可以快速了解每次提交的主要内容和目的，便于代码审查、问题追踪和版本回溯。
- **自动化版本管理**：借助规范的提交信息，自动化工具可以根据提交类型（如 `feat`、`fix`）自动生成版本号，实现语义化版本管理。例如，使用 `semantic - release` 工具可以根据提交信息自动确定是进行小版本更新还是补丁版本更新。
- **团队协作效率提升**：统一的提交信息格式有助于团队成员之间更好地沟通和协作，减少因提交信息不规范而产生的误解和沟通成本。

### 实践建议

- **培训与宣传**：在团队中引入 Conventional Commits 规范时，需要对团队成员进行培训，让大家了解规范的内容和重要性。可以提供示例和文档，帮助成员快速掌握规范的使用方法。
- **工具辅助**：使用 Commitlint 等工具对提交信息进行检查，确保每次提交都符合规范。将 Commitlint 集成到 Husky 的 `commit - msg` 钩子中，在提交代码时自动验证提交信息的格式。
