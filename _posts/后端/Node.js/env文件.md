在 Node.js 项目中，`.env` 文件通常用来存放**环境变量**，也就是那些会随运行环境变化、不应该硬编码到代码里的配置，例如：

- 数据库连接地址
- API Key / Secret
- 服务端口
- 第三方服务配置
- 当前运行环境：开发 / 测试 / 生产

它的核心作用是：**把配置从代码中分离出来，提高安全性和可维护性。**

---

# 1. `.env` 文件是什么？

`.env` 文件本质上是一个**纯文本文件**，通常位于项目根目录，用来定义一组键值对。

例如：

```env
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=123456
JWT_SECRET=abcdefg
```

这些内容可以被 Node.js 程序在启动时读取，并挂载到 `process.env` 上。

---

# 2. **为什么需要 `.env`？**

## （1）避免把敏感信息写死在代码中

错误示例：

```js
const password = "123456";
```

这样做的问题：

- 代码提交到 Git 后，密钥可能泄露
- 不同环境（开发 / 生产）需要不同配置
- 维护困难

使用 `.env` 后，代码中只写：

```js
const password = process.env.DB_PASSWORD;
```

---

## （2）区分不同环境

例如：

开发环境：

```env
DB_HOST=localhost
```

生产环境：

```env
DB_HOST=prod-db.example.com
```

这样无需修改代码，只需切换环境变量即可。

---

# 3. `.env` 文件的基本格式

`.env` 文件一般遵循这种格式：

```env
KEY=value
```

示例：

```env
PORT=3000
API_URL=https://api.example.com
SECRET_KEY=abc123
```

---

## 常见规则

### 1. 每行一个变量

```env
PORT=3000
HOST=127.0.0.1
```

---

### 2. 使用 `#` 添加注释

```env
# 服务端口
PORT=3000
```

---

### 3. 值可以带引号

```env
NAME="Hello World"
```

有些库会解析引号，有些不会，所以通常建议尽量避免值中出现空格或特殊字符。

---

### 4. 空行通常会被忽略

```env
PORT=3000

DB_HOST=localhost
```

---

# 4. Node.js 如何读取 `.env` 文件？

Node.js 本身默认**不会自动读取** `.env` 文件。

你需要使用工具来加载它。最常见的方式有两种：

1. 使用第三方库 `dotenv`
2. 使用 Node.js 内置能力（较新版本）

---

# 5. 使用 `dotenv` 读取 `.env`

这是最常见、最传统的方式。

## 安装

```bash
npm install dotenv
```

---

## 使用示例

假设项目根目录有 `.env` 文件：

```env
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=123456
```

然后在 `app.js` 中：

```js
require("dotenv").config();

console.log(process.env.PORT);       // 3000
console.log(process.env.DB_HOST);    // localhost
console.log(process.env.DB_USER);    // root
console.log(process.env.DB_PASSWORD);// 123456
```

---

## 在 Express 中使用

```js
require("dotenv").config();
const express = require("express");

const app = express();

app.get("/", (req, res) => {
  res.send("Hello World");
});

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

# 6. `dotenv` 的执行时机

非常重要的一点：

> `dotenv.config()` 应该尽早执行，最好放在程序入口文件最顶部。

例如：

```js
require("dotenv").config();

const express = require("express");
const db = require("./db");
```

如果你先加载了某个模块，而那个模块内部已经读取了环境变量，那么之后再执行 `dotenv.config()` 可能就来不及了。

---

# 7. Node.js 原生读取 `.env`（Node 20+）

从 **Node.js 20.6.0** 开始，Node 提供了内置的 `--env-file` 参数，可以不依赖 `dotenv` 直接读取 `.env` 文件。

## 使用方式

假设你有 `.env` 文件：

```env
PORT=3000
```

启动命令：

```bash
node --env-file=.env app.js
```

然后在代码中：

```js
console.log(process.env.PORT);
```

---

## 优点

- 无需安装第三方库
- 更简单
- 适合轻量项目

---

## 注意事项

- 需要较新版本的 Node.js
- 某些旧项目仍广泛使用 `dotenv`
- 多环境配置时，加载多个文件的灵活性可能不如 `dotenv` 生态方便

---

# 8. `.env` 文件与 `process.env`

在 Node.js 中，所有环境变量最终都会通过：

```js
process.env
```

访问。

例如：

```env
PORT=3000
```

对应：

```js
process.env.PORT
```

---

## `process.env` 的值类型

需要特别注意：

> `process.env` 里的值都是字符串。

例如：

```env
PORT=3000
DEBUG=true
```

读取时：

```js
console.log(typeof process.env.PORT);  // "string"
console.log(typeof process.env.DEBUG); // "string"
```

如果你想要数字或布尔值，需要手动转换。

---

### 转数字

```js
const port = Number(process.env.PORT) || 3000;
```

---

### 转布尔值

```js
const debug = process.env.DEBUG === "true";
```

---

# 9. `.env`、`.env.example`、`.env.local` 的区别

在真实项目中，经常会看到多个类似文件。

---

## `.env`

实际使用的本地环境变量文件，通常包含敏感信息。

```env
DB_PASSWORD=123456
```

一般**不提交到 Git**。

---

## `.env.example`

*示例文件，用来告诉其他开发者需要哪些环境变量，但不包含真实值*。

```env
PORT=3000
DB_HOST=
DB_USER=
DB_PASSWORD=
```

这个文件通常会*提交到 Git*。

---

## `.env.local`

有些框架会用它表示本地覆盖配置，比如某些前端框架或脚手架。

在纯 Node.js 中，这个不是标准，只是约定。

---

## `.env.development` / `.env.production`

用于区分环境：

- `.env.development`
- `.env.test`
- `.env.production`

是否支持取决于你使用的工具或框架。

例如某些框架会自动根据 `NODE_ENV` 加载对应文件。

---

# 10. `.env` 文件一定要加入 `.gitignore`

这是非常重要的安全实践。

因为 `.env` 经常包含敏感信息，如密码、密钥、Token。

在项目根目录的 `.gitignore` 中添加：

```gitignore
.env
```

如果还需要提供模板，可以提交：

```gitignore
.env
!.env.example
```

这样：

- `.env` 不上传
- `.env.example` 可以上传

---

# 11. 多环境配置示例

很多项目会区分开发环境和生产环境。

---

## 方式一：多个 `.env` 文件

比如：

```bash
.env.development
.env.production
```

然后使用不同的启动命令。

例如借助 `dotenv-cli`：

```bash
npm install -D dotenv-cli
```

`package.json`：

```json
{
  "scripts": {
    "start:dev": "dotenv -e .env.development -- node app.js",
    "start:prod": "dotenv -e .env.production -- node app.js"
  }
}
```

---

## 方式二：使用 `NODE_ENV`

```env
NODE_ENV=development
```

代码中：

```js
if (process.env.NODE_ENV === "production") {
  console.log("生产环境");
}
```

---

# 12. 在 ES Module 中使用 `.env`

如果你的项目使用 ES Module（`import` 语法），写法会略有不同。

## `package.json`

```json
{
  "type": "module"
}
```

## 使用 `dotenv`

```js
import dotenv from "dotenv";
dotenv.config();

console.log(process.env.PORT);
```

或者在 Node 20+ 中直接：

```bash
node --env-file=.env app.js
```

---

# 13. TypeScript 项目中的 `.env`

在 TypeScript 中，本质上也是运行时读取环境变量，但为了更好的类型提示，可以做类型声明。

## 安装

```bash
npm install dotenv
npm install -D @types/node
```

## 使用

```ts
import dotenv from "dotenv";
dotenv.config();

const port = Number(process.env.PORT) || 3000;

console.log(port);
```

---

## 为环境变量增加类型提示

可以创建 `types.d.ts` 或 `env.d.ts`：

```ts
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      PORT?: string;
      DB_HOST?: string;
      DB_USER?: string;
      DB_PASSWORD?: string;
    }
  }
}

export {};
```

这样写 `process.env.PORT` 时会有更好的提示。

---

# 14. `.env` 文件常见使用场景

---

## 场景 1：Web 服务端口

```env
PORT=4000
```

```js
const port = process.env.PORT || 3000;
```

---

## 场景 2：数据库连接

```env
DB_HOST=localhost
DB_PORT=5432
DB_NAME=mydb
DB_USER=admin
DB_PASSWORD=secret
```

```js
const dbConfig = {
  host: process.env.DB_HOST,
  port: Number(process.env.DB_PORT),
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
};
```

---

## 场景 3：第三方 API Key

```env
OPENAI_API_KEY=xxxx
STRIPE_SECRET_KEY=yyyy
```

```js
const apiKey = process.env.OPENAI_API_KEY;
```

---

## 场景 4：邮件服务配置

```env
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=no-reply@example.com
SMTP_PASSWORD=password123
```

---

# 15. `.env` 的最佳实践

---

## 1. 不要把 `.env` 提交到仓库

尤其是包含密钥、密码时。

---

## 2. 提供 `.env.example`

方便团队成员知道需要哪些变量：

```env
PORT=3000
DB_HOST=
DB_USER=
DB_PASSWORD=
```

---

## 3. 为变量设置默认值

例如：

```js
const port = process.env.PORT || 3000;
const host = process.env.HOST || "localhost";
```

这样即使缺少某些环境变量，程序也能正常运行。

---

## 4. 对必要变量做校验

比如：

```js
if (!process.env.DB_PASSWORD) {
  throw new Error("DB_PASSWORD is required");
}
```

这样可以尽早发现配置问题。

---

## 5. 统一读取配置

不建议在项目中到处直接写 `process.env.XXX`，最好封装一个配置模块。

例如 `config.js`：

```js
require("dotenv").config();

module.exports = {
  port: Number(process.env.PORT) || 3000,
  db: {
    host: process.env.DB_HOST || "localhost",
    user: process.env.DB_USER || "root",
    password: process.env.DB_PASSWORD || "",
  },
  jwtSecret: process.env.JWT_SECRET || "default_secret",
};
```

使用：

```js
const config = require("./config");

console.log(config.port);
```

这样做的好处：

- 统一转换类型
- 统一设置默认值
- 便于测试和维护

---

# 16. `.env` 文件不是万能的

虽然 `.env` 很方便，但它并不适合所有场景。

---

## 不适合存储复杂配置

比如数组、对象、嵌套结构，`.env` 并不天然擅长。

如果你要存：

```env
LIST=[1,2,3]
```

它本质上还是字符串，需要自己解析。

---

## 不适合频繁变更的运行时配置

`.env` 通常在**进程启动时读取**。如果运行中修改 `.env`，程序一般不会自动感知。

除非你额外实现热加载机制。

---

# 17. `.env` 文件会不会自动重新加载？

通常不会。

例如你已经启动了 Node 服务，之后修改 `.env`，正在运行的进程通常不会自动读取新值。

你需要重启服务。

---

# 18. 常见错误与排查

---

## 错误 1：变量读取为 `undefined`

例如：

```js
console.log(process.env.PORT); // undefined
```

可能原因：

- 没有安装或引入 `dotenv`
- `.env` 不在当前工作目录
- 文件名写错
- 没有在读取前调用 `dotenv.config()`

---

## 错误 2：变量类型问题

```env
DEBUG=true
```

```js
if (process.env.DEBUG) {
  // 即使是字符串 "false"，这里也是 true
}
```

正确方式：

```js
const debug = process.env.DEBUG === "true";
```

---

## 错误 3：`.env` 被提交到 Git

这是很严重的安全隐患。

解决办法：

1. 加入 `.gitignore`
2. 如果已经提交过，考虑撤销历史或更换密钥
3. 使用 `.env.example` 提供模板

---

## 错误 4：路径问题

`dotenv` 默认从当前工作目录查找 `.env`。

如果你的启动目录不同，可能读不到文件。

可以显式指定路径：

```js
const path = require("path");
require("dotenv").config({ path: path.resolve(__dirname, ".env") });
```

---

# 19. 不同框架中的 `.env`

虽然你问的是 Node.js，但实际开发中很多框架都会自动处理 `.env`。

---

## Express / Koa / Fastify

这些后端框架本身通常不会自动读取 `.env`，你需要：

- `dotenv`
- 或 Node 20+ 的 `--env-file`

---

## Next.js、Nuxt、Vite、Create React App

这些框架通常内置了对 `.env` 的支持，但行为可能不同，例如：

- 是否自动加载
- 是否区分 `NODE_ENV`
- 哪些变量会暴露给浏览器端

所以在使用框架时，要看该框架的文档。

---

# 20. 生产环境还要用 `.env` 文件吗？

可以，但不是唯一方式。

在生产环境中，很多团队会直接通过服务器或容器平台设置环境变量，而不是放置 `.env` 文件。

例如：

## Linux 服务器

```bash
export PORT=8080
export DB_PASSWORD=xxx
node app.js
```

---

## Docker

```dockerfile
ENV PORT=8080
```

或者运行：

```bash
docker run -e DB_PASSWORD=secret myapp
```

---

## Kubernetes

通过 ConfigMap / Secret 注入环境变量。

---

## 云服务

例如：

- Vercel
- Railway
- Render
- Heroku
- Fly.io

它们通常提供图形界面来配置环境变量。

---

# 21. `.env` 和安全性

`.env` 常涉及敏感数据，因此要注意：

---

## 不要明文上传到仓库

尤其是包含：

- 数据库密码
- JWT Secret
- 云服务密钥
- 支付密钥

---

## 本地开发可以用 `.env`

这没问题，但别提交。

---

## 生产环境建议用更安全的方式

例如：

- 平台环境变量管理
- Secret Manager
- Vault
- KMS

---

# 22. 一个完整示例

## 项目结构

```bash
my-app/
  .env
  .gitignore
  app.js
  package.json
```

---

## `.env`

```env
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=123456
```

---

## `.gitignore`

```gitignore
node_modules/
.env
```

---

## `app.js`

```js
require("dotenv").config();

const port = Number(process.env.PORT) || 3000;
const dbHost = process.env.DB_HOST || "localhost";
const dbUser = process.env.DB_USER || "root";
const dbPassword = process.env.DB_PASSWORD || "";

console.log("Server starting...");
console.log("Port:", port);
console.log("DB Host:", dbHost);
console.log("DB User:", dbUser);
```

---

## 启动

```bash
node app.js
```

---

# 23. 总结

`.env` 文件在 Node.js 中主要用于：

- 存放环境变量
- 管理配置
- 隔离敏感信息
- 区分开发 / 测试 / 生产环境

常见使用方式：

- 使用 `dotenv` 加载
- 或使用 Node 20+ 的 `--env-file`

最重要的几个原则：

1. `.env` 不要提交到 Git  
2. 敏感信息不要硬编码到代码里  
3. `process.env` 的值都是字符串  
4. 尽量封装统一的配置模块  
5. 生产环境可以使用平台级环境变量或密钥管理服务  

如果你愿意，我还可以继续给你补充以下任意一种内容：

1. **`dotenv` 源码级原理**
2. **Express + MySQL + `.env` 实战示例**
3. **Node.js 多环境配置完整方案**
4. **前端项目和后端项目中 `.env` 的区别**