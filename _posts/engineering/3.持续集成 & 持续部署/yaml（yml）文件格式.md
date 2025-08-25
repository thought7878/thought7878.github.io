### **YML 文件格式详解（YAML）**

**YML（YAML Ain't Markup Language）** 是一种**人类可读的数据序列化格式**，广泛用于配置文件（如 Docker Compose、Kubernetes、Ansible 等）。它强调**简洁性、可读性和易写性**，用缩进和符号替代复杂的括号结构。

---

### **核心设计原则**
1. **人类优先**  
   - 语法接近自然语言，比 JSON/XML 更易读写。
2. **缩进即结构**  
   - 用**空格缩进**表示层级（**禁止使用 Tab**）。
3. **最小化符号**  
   - 无花括号 `{}`、分号 `;`，用 `:` 和 `-` 定义关系。
4. **兼容 JSON**  
   - 有效的 JSON 也是有效的 YAML（但 YAML 功能更丰富）。

> 💡 **YAML = YAML Ain't Markup Language**（递归缩写，强调它不是标记语言）

---

### **基础语法规则**
#### 1. **键值对（Map/字典）**
```yaml
# 键: 值（冒号后必须有空格！）
name: "Alice"
age: 30
is_student: false
```
- **字符串**：引号可选（`"Alice"` 或 `Alice`），但特殊字符需加引号（如 `:`, `{`）。
- **布尔值**：`true`/`false`（小写）。
- **数字**：直接写（`30`）。

#### 2. **列表（Sequence/数组）**
```yaml
fruits:
  - Apple
  - Banana
  - "Orange"  # 特殊字符需引号
```
- 每项以 `- ` 开头（短横线 + 空格）。
- 缩进对齐表示同级列表。

#### 3. **嵌套结构**
```yaml
person:
  name: Bob
  age: 25
  hobbies:  # 嵌套列表
    - Reading
    - Hiking
  address:  # 嵌套字典
    city: "New York"
    zip: 10001
```
- **缩进决定层级**（通常用 2 空格）。

#### 4. **多行字符串**
```yaml
bio: |
  Hello, I'm a developer.
  I love coding and writing documentation.
  YAML is awesome!

summary: >
  This is a folded line.
  It will be read as a single line.
```
- `|`：保留换行（字面块）。
- `>`：折叠换行（单行显示，用空格连接）。

#### 5. **注释**
```yaml
# 这是单行注释
key: value  # 行内注释
```
- 用 `#` 开头。

---

### **高级特性**
#### 1. **锚点 & 引用（避免重复）**
```yaml
defaults: &defaults  # 定义锚点（&）
  adapter: postgres
  host: localhost

development:
  <<: *defaults  # 引用锚点（*）
  database: dev_db

test:
  <<: *defaults
  database: test_db
```
- `&defaults`：创建锚点别名。
- `*defaults`：引用锚点内容。
- `<<`：合并映射（类似继承）。

#### 2. **多文档分隔**
```yaml
# 文档1
name: Doc1

---
# 文档2
version: 1.0
```
- 用 `---` 分隔多个独立文档。

#### 3. **数据类型显式声明**
```yaml
timestamp: !!timestamp 2023-10-05T12:30:00Z
binary: !!binary "R0lGODlhAQABAIAAAAQCBQAAACwAAAAAAQABAAACAkQBADs="
```
- `!!` 强制指定类型（如时间戳、二进制）。

---

### **常见错误 & 陷阱**
| 错误示例 | 正确写法 | 原因 |
|----------|----------|------|
| `key:value` | `key: value` | **冒号后必须有空格** |
| `key: value #注释` | `key: value  # 注释` | 注释前至少两个空格 |
| 缩进用 Tab | 仅用空格 | **YAML 禁止 Tab** |
| `list: - item1` | `list:\n  - item1` | 列表需换行缩进 |
| `key: {a:1, b:2}` | `key:\n  a: 1\n  b: 2` | 避免内联 JSON 风格 |

> ⚠️ **重要**：YAML 对缩进极其敏感！推荐用 **2 空格缩进**（社区惯例）。

---

### **典型应用场景**
| 工具 | 用途 | 示例片段 |
|------|------|----------|
| **Docker Compose** | 容器编排 | ```yaml<br>services:<br>  web:<br>    image: nginx<br>    ports:<br>      - "80:80"``` |
| **Kubernetes** | 集群配置 | ```yaml<br>apiVersion: v1<br>kind: Pod<br>metadata:<br>  name: my-pod``` |
| **Ansible** | 自动化运维 | ```yaml<br>- hosts: all<br>  tasks:<br>    - name: Install nginx<br>      apt:<br>        name: nginx``` |
| **Spring Boot** | 应用配置 | ```yaml<br>server:<br>  port: 8080<br>spring:<br>  datasource:<br>    url: jdbc:mysql://...``` |

---

### **YAML vs JSON vs XML**
| 特性 | YAML | JSON | XML |
|------|------|------|-----|
| **可读性** | ⭐⭐⭐⭐ | ⭐⭐ | ⭐ |
| **写配置速度** | ⭐⭐⭐ | ⭐ | ⭐ |
| **数据类型支持** | 丰富 | 基础 | 有限 |
| **注释支持** | ✅ | ❌ | ✅ |
| **解析性能** | 慢 | 快 | 慢 |
| **典型用途** | 配置文件 | API 数据交换 | 企业级数据交换 |

> ✅ **YAML 优势**：配置文件首选（人写人读）  
> ✅ **JSON 优势**：机器生成/解析（API 传输）

---

### **验证与调试工具**
1. **在线验证器**  
   - [YAML Linter](https://www.yamllint.com/)：实时检查语法错误。
2. **VS Code 插件**  
   - **YAML**（Red Hat 官方插件）：自动补全、错误提示、格式化。
3. **命令行验证**  
   ```bash
   # 安装 PyYAML
   pip install pyyaml
   # 验证文件
   python -c 'import yaml, sys; print(yaml.safe_load(sys.stdin))' < config.yml
   ```

---

### **最佳实践**
1. **统一缩进**：始终用 **2 空格**（避免 4 空格或混合空格）。
2. **字符串引号**：  
   - 普通字符串不加引号（`name: Alice`）。  
   - 含特殊字符时用双引号（`error: "Invalid: value"`）。
3. **避免复杂结构**：  
   - 不要嵌套超过 4 层，拆分大型文件。
4. **使用锚点复用**：  
   - 减少重复配置（如数据库连接参数）。
5. **注释关键配置**：  
   - 用 `#` 说明非常规设置。

---

### **示例：完整 Docker Compose 文件**
```yaml
version: "3.8"  # Compose 文件版本

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: "secure!pass"  # 特殊字符需引号
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:  # 声明命名卷
```

---

### **总结**
- **YML = 人类友好的配置语言**，核心是 **缩进 + 冒号 + 短横线**。
- **黄金法则**：冒号后加空格、禁用 Tab、统一 2 空格缩进。
- **适用场景**：配置文件（Docker/K8s/Ansible）、数据交换（当可读性优先时）。
- **避坑要点**：用在线工具验证、避免过度嵌套、善用锚点复用。

> 📌 **记住**：  
> **"YAML is a superset of JSON, but JSON is not YAML."**  
> （YAML 包含 JSON 的能力，但 JSON 无法实现 YAML 的简洁语法）