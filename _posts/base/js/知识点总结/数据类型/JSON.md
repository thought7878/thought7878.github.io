**JSON**（JavaScript Object Notation）是一种轻量级的数据交换格式，具有*易于阅读和编写*的特点，同时*也易于机器解析和生成*。在 JavaScript 中，JSON 被广泛应用于*数据的存储、传输和交换*。

### 基本概念

JSON 是一种*基于文本*的**数据格式**，*它源于 JavaScript 的对象字面量语法*，但它是独立于编程语言的。JSON 数据*由键值对组成*，`键`必须是字符串，`值`可以是字符串、数字、布尔值、数组、对象或 `null`。JSON 有两种结构：对象和数组。

### JSON 语法

#### JSON 对象

JSON 对象是由花括号 `{}` 包围的键值对集合，键和值之间用冒号 `:` 分隔，键值对之间用逗号 `,` 分隔。

```json
{
  "name": "John",
  "age": 30,
  "isStudent": false,
  "hobbies": ["reading", "swimming"]
}
```

#### JSON 数组

JSON 数组是由方括号 `[]` 包围的值的有序列表，值之间用逗号 `,` 分隔。

```json
[
  {
    "name": "John",
    "age": 30
  },
  {
    "name": "Jane",
    "age": 25
  }
]
```

### JSON 与 JavaScript 对象的转换

#### `JSON.stringify()`

`JSON.stringify()` 方法用于*将 JavaScript 对象或数组*转换为 JSON 字符串。

```javascript
const person = {
  name: "John",
  age: 30,
  isStudent: false,
};

const jsonString = JSON.stringify(person);
console.log(jsonString);
// 输出: {"name":"John","age":30,"isStudent":false}
```

`JSON.stringify()` 还可以接受第二个参数（替换函数或数组）和第三个参数（缩进空格数）。

```javascript
const person = {
  name: "John",
  age: 30,
  isStudent: false,
};

// 只包含 'name' 和 'age' 属性
const jsonString = JSON.stringify(person, ["name", "age"], 2);
console.log(jsonString);
/* 输出:
{
  "name": "John",
  "age": 30
}
*/
```

#### `JSON.parse()`

`JSON.parse()` 方法用于*将 JSON 字符串*转换为 JavaScript 对象或数组。

```javascript
const jsonString = '{"name":"John","age":30,"isStudent":false}';
const person = JSON.parse(jsonString);
console.log(person.name); // 输出: John
```

### JSON 的使用场景

#### 数据传输

在前后端数据交互中，JSON 是一种常用的数据格式。例如，在使用 AJAX 或 Fetch API 进行数据请求和响应时，服务器通常会返回 JSON 格式的数据，前端可以使用 `JSON.parse()` 将其转换为 JavaScript 对象进行处理。

#### 数据存储

JSON 可以用于在本地存储数据，如使用 `localStorage` 或 `sessionStorage`。

```javascript
const person = {
  name: "John",
  age: 30,
};

// 将对象转换为 JSON 字符串并存储
localStorage.setItem("person", JSON.stringify(person));

// 从本地存储中获取 JSON 字符串并转换为对象
const storedPerson = JSON.parse(localStorage.getItem("person"));
console.log(storedPerson.name); // 输出: John
```

### 注意事项

- **JSON 键必须是字符串**：在 JSON 中，键必须用双引号括起来，这与 JavaScript 对象字面量不同，JavaScript 对象的键可以是标识符而不使用引号。
- **JSON 值的类型限制**：JSON 中的值只能是字符串、数字、布尔值、数组、对象或 `null`，*不支持函数、日期*等 JavaScript 特有的数据类型。如果对象中包含这些类型，`JSON.stringify()` 会忽略它们。

### 总结

JSON 是一种轻量级的数据交换格式，在 JavaScript 中使用非常广泛。通过 `JSON.stringify()` 和 `JSON.parse()` 方法，可以方便地在 JavaScript 对象和 JSON 字符串之间进行转换。JSON 在数据传输和存储等场景中发挥着重要作用。
