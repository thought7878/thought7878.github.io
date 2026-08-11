下面详细总结 Node.js 中的 `__dirname` 和 `__filename`。

---

# 1. 基本概念

在 CommonJS 模块中，Node.js 会提供两个常用变量：

| 变量 | 含义 |
|---|---|
| `__dirname` | 当前模块所在目录的绝对路径 |
| `__filename` | 当前模块文件的绝对路径 |

例如文件路径为：

```text
/home/user/project/src/index.js
```

在 `index.js` 中打印：

```js
console.log(__dirname);
console.log(__filename);
```

输出类似：

```text
/home/user/project/src
/home/user/project/src/index.js
```

在 Windows 中可能是：

```text
C:\project\src
C:\project\src\index.js
```

---

# 2. 它们不是真正的全局变量

虽然 `__dirname` 和 `__filename` 看起来像全局变量，但它们并不是 `global` 上的属性。

也就是说：

```js
console.log(global.__dirname);
console.log(global.__filename);
```

通常是：

```text
undefined
undefined
```

它们实际上是 CommonJS 模块包装函数注入的参数。

CommonJS 模块在执行时，大致会被包装成类似这样：

```js
(function (exports, require, module, __filename, __dirname) {
  // 你写的模块代码
});
```

所以：

| 变量 | 是否真正全局 | 作用范围 |
|---|---|---|
| `global` | 是 | 全局对象 |
| `__dirname` | 否 | 当前 CommonJS 模块内 |
| `__filename` | 否 | 当前 CommonJS 模块内 |

---

# 3. __dirname 和 __filename 的关系

`__dirname` 就是 `__filename` 的目录部分。

也就是说：

```js
const path = require('node:path');

console.log(__dirname === path.dirname(__filename));
// true
```

示例：

```js
const path = require('node:path');

console.log(__filename);
// /home/user/project/src/index.js

console.log(__dirname);
// /home/user/project/src

console.log(path.dirname(__filename));
// /home/user/project/src
```

---

# 4. 主要用途

`__dirname` 和 `__filename` 最常见的用途是：**基于当前文件位置定位资源**。

这比使用 `process.cwd()` 更稳定，因为 `process.cwd()` 取决于启动命令所在目录，而 `__dirname` 取决于当前文件本身。

---

## 4.1 读取当前目录下的文件

假设目录结构：

```text
project/
  config/
    app.json
  src/
    index.js
```

在 `src/index.js` 中读取同级或相对文件：

```js
const path = require('node:path');
const fs = require('node:fs');

const configPath = path.join(__dirname, '..', 'config', 'app.json');

const content = fs.readFileSync(configPath, 'utf8');

console.log(content);
```

这里：

```js
path.join(__dirname, '..', 'config', 'app.json')
```

表示从当前文件所在目录向上一级，再进入 `config/app.json`。

---

## 4.2 拼接当前模块目录下的资源

```js
const path = require('node:path');

const templatePath = path.join(__dirname, 'templates', 'index.html');
```

假设当前文件是：

```text
/home/user/project/src/render.js
```

那么：

```js
templatePath
```

就是：

```text
/home/user/project/src/templates/index.html
```

---

## 4.3 判断文件扩展名

```js
const path = require('node:path');

console.log(path.extname(__filename));
// .js
```

---

## 4.4 获取当前文件名

```js
const path = require('node:path');

console.log(path.basename(__filename));
// index.js
```

不想要扩展名：

```js
console.log(path.basename(__filename, '.js'));
// index
```

---

# 5. __dirname 和 process.cwd() 的区别

这是非常重要的一组对比。

| 对比项 | `__dirname` | `process.cwd()` |
|---|---|---|
| 含义 | 当前模块文件所在目录 | 当前 Node.js 进程启动目录 |
| 是否随文件变化 | 每个文件不同 | 整个进程内通常一致 |
| 是否受启动命令影响 | 不受 | 受 |
| 是否适合定位模块资源 | 非常适合 | 不一定适合 |

---

## 示例

假设目录结构：

```text
/home/user/project/
  src/
    index.js
```

在 `/home/user/project/src/index.js` 中写：

```js
console.log(__dirname);
console.log(process.cwd());
```

如果执行：

```bash
cd /home/user/project
node src/index.js
```

输出可能是：

```text
/home/user/project/src
/home/user/project
```

如果执行：

```bash
cd /home/user/project/src
node index.js
```

输出可能是：

```text
/home/user/project/src
/home/user/project/src
```

可以看到：

- `__dirname` 始终是当前文件所在目录；
- `process.cwd()` 会随着启动目录变化。

---

# 6. 什么时候用 __dirname，什么时候用 process.cwd()

## 6.1 使用 `__dirname` 的场景

适合读取和当前模块相关的文件。

例如：

```js
const path = require('node:path');
const fs = require('node:fs');

const configPath = path.join(__dirname, 'config.json');
```

适合：

1. 当前模块自带的配置文件；
2. 当前模块目录下的模板；
3. 当前模块目录下的静态资源；
4. 工具函数依赖的本地文件；
5. 不希望受启动目录影响的场景。

---

## 6.2 使用 `process.cwd()` 的场景

适合读取和当前执行命令相关的项目根目录资源。

例如：

```js
const path = require('node:path');

const configPath = path.resolve(process.cwd(), 'config/app.json');
```

适合：

1. CLI 工具读取用户当前项目目录；
2. 构建工具读取项目根目录；
3. 运行时读取项目根目录配置；
4. 启动脚本依赖当前工作目录。

---

# 7. __filename 和模块入口

`__filename` 可以用于判断当前文件是否被直接运行。

不过更常见的 CommonJS 写法是：

```js
if (require.main === module) {
  console.log('当前文件被直接执行');
} else {
  console.log('当前文件被其他模块 require');
}
```

也可以通过 `process.argv[1]` 和 `__filename` 对比，但需要注意符号链接、路径解析等问题。

例如：

```js
const path = require('node:path');
const fs = require('node:fs');

const entry = process.argv[1];

if (entry && fs.realpathSync(entry) === __filename) {
  console.log('当前文件被直接执行');
}
```

不过一般推荐：

```js
if (require.main === module) {
  // 直接执行
}
```

---

# 8. CommonJS 中的 __dirname 和 __filename

`__dirname` 和 `__filename` 只在 CommonJS 模块中可用。

例如：

```js
// index.js
console.log(__dirname);
console.log(__filename);
```

运行：

```bash
node index.js
```

可以正常输出。

---

# 9. ESM 中没有 __dirname 和 __filename

在 ES Module 中，也就是使用：

```js
import xxx from 'xxx';
export default xxx;
```

这种模块格式时，`__dirname` 和 `__filename` 默认不可用。

例如在 `.mjs` 文件或者 `package.json` 中设置了：

```json
{
  "type": "module"
}
```

之后写：

```js
console.log(__dirname);
console.log(__filename);
```

会报错：

```text
__dirname is not defined
```

或：

```text
__filename is not defined
```

---

# 10. ESM 中如何模拟 __dirname 和 __filename

在 ESM 中，可以使用 `import.meta.url` 来获取当前模块的 URL。

然后通过 `node:url` 模块的 `fileURLToPath()` 转成文件路径。

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

console.log(__filename);
console.log(__dirname);
```

这是 ESM 中最常见的写法。

---

## 示例

假设文件为：

```text
/home/user/project/src/index.mjs
```

代码：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

console.log(__filename);
console.log(__dirname);
```

输出类似：

```text
/home/user/project/src/index.mjs
/home/user/project/src
```

Windows 下可能是：

```text
C:\project\src\index.mjs
C:\project\src
```

---

# 11. ESM 中读取当前模块目录下的文件

示例：

```js
import path from 'node:path';
import fs from 'node:fs';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const configPath = path.join(__dirname, 'config.json');

const content = fs.readFileSync(configPath, 'utf8');

console.log(content);
```

这段代码在 ESM 中相当于 CommonJS 的：

```js
const path = require('node:path');
const fs = require('node:fs');

const configPath = path.join(__dirname, 'config.json');

const content = fs.readFileSync(configPath, 'utf8');

console.log(content);
```

---

# 12. 在 TypeScript 中的情况

TypeScript 本身不决定是否有 `__dirname`，关键看最终编译成什么模块格式。

## 12.1 编译为 CommonJS

如果 `tsconfig.json` 中类似：

```json
{
  "compilerOptions": {
    "module": "CommonJS"
  }
}
```

那么通常可以使用：

```ts
console.log(__dirname);
console.log(__filename);
```

不过可能需要安装 Node.js 类型：

```bash
npm install -D @types/node
```

---

## 12.2 编译为 ESM

如果最终输出为 ES Module，例如：

```json
{
  "compilerOptions": {
    "module": "NodeNext"
  }
}
```

并且使用 ESM 规范，那么 `__dirname` 和 `__filename` 不可用，需要自己构造：

```ts
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

console.log(__dirname);
console.log(__filename);
```

---

# 13. 和 path 模块配合使用

`__dirname` 和 `__filename` 最常见的搭配是 `path` 模块。

---

## 13.1 拼接路径

```js
const path = require('node:path');

const filePath = path.join(__dirname, 'data', 'users.json');
```

---

## 13.2 解析绝对路径

```js
const path = require('node:path');

const outputPath = path.resolve(__dirname, '../dist/index.html');
```

---

## 13.3 获取目录名

```js
const path = require('node:path');

console.log(path.dirname(__filename));
console.log(__dirname);
```

两者通常相等。

---

## 13.4 获取文件名

```js
const path = require('node:path');

console.log(path.basename(__filename));
```

---

## 13.5 获取扩展名

```js
const path = require('node:path');

console.log(path.extname(__filename));
```

---

# 14. 常见坑点

---

## 14.1 ESM 中直接使用会报错

错误写法：

```js
import fs from 'node:fs';

fs.readFileSync(__dirname + '/config.json');
```

在 ESM 中会报：

```text
__dirname is not defined
```

正确写法：

```js
import path from 'node:path';
import fs from 'node:fs';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const configPath = path.join(__dirname, 'config.json');
```

---

## 14.2 不要手动拼接路径

不推荐：

```js
const filePath = __dirname + '/config.json';
```

Windows 下可能产生：

```text
C:\project/config.json
```

虽然 Node.js 很多时候能处理，但不够规范。

推荐：

```js
const path = require('node:path');

const filePath = path.join(__dirname, 'config.json');
```

或者：

```js
const filePath = path.resolve(__dirname, 'config.json');
```

---

## 14.3 __dirname 不等于项目根目录

`__dirname` 是当前文件所在目录，不一定是项目根目录。

例如：

```text
project/
  src/
    utils/
      helper.js
```

在 `helper.js` 中：

```js
console.log(__dirname);
```

输出：

```text
/project/src/utils
```

而不是：

```text
/project
```

如果想获取项目根目录，需要自己逐级向上找，或者结合配置文件、`package.json`、环境变量等方式处理。

---

## 14.4 __dirname 和启动目录无关

执行命令时所在目录不同，`process.cwd()` 会变，但 `__dirname` 不变。

例如文件：

```text
/home/user/project/src/index.js
```

无论从哪个目录启动：

```bash
cd /home/user/project
node src/index.js
```

或：

```bash
cd /home/user/project/src
node index.js
```

`__dirname` 都是：

```text
/home/user/project/src
```

---

## 14.5 符号链接会被解析

Node.js 中的 `__filename` 和 `__dirname` 通常指向解析符号链接后的真实文件路径。

如果你需要手动获取真实路径，可以使用：

```js
const fs = require('node:fs');

console.log(fs.realpathSync(__filename));
```

---

## 14.6 打包工具中行为可能不同

在 Webpack、Rollup、esbuild、Vite 等打包环境中，`__dirname` 和 `__filename` 的行为可能被模拟、替换或不可用。

例如 Webpack 可以配置 `node.__dirname` 和 `node.__filename` 的 mock 行为。

所以在浏览器端代码或打包后的 bundle 中，不应盲目依赖它们。

---

## 14.7 REPL 和 `node -e` 中不一定可用

在普通文件中：

```js
console.log(__dirname);
console.log(__filename);
```

可用。

但在 Node.js REPL 或 `node -e` 中，它们可能不可用或行为不同。

例如：

```bash
node -e "console.log(__dirname)"
```

可能输出：

```text
undefined
```

或者报错，取决于运行环境。

---

# 15. 实际项目中的常见写法

## 15.1 CommonJS 标准写法

```js
const path = require('node:path');
const fs = require('node:fs');

const configPath = path.join(__dirname, 'config.json');

if (fs.existsSync(configPath)) {
  const config = JSON.parse(fs.readFileSync(configPath, 'utf8'));
  console.log(config);
}
```

---

## 15.2 ESM 标准写法

```js
import path from 'node:path';
import fs from 'node:fs';
import { fileURLToPath } from 'node:url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const configPath = path.join(__dirname, 'config.json');

if (fs.existsSync(configPath)) {
  const config = JSON.parse(fs.readFileSync(configPath, 'utf8'));
  console.log(config);
}
```

---

## 15.3 封装 ESM 的 __dirname 工具

可以新建一个文件：

```js
// paths.mjs
import path from 'node:path';
import { fileURLToPath } from 'node:url';

export const __filename = fileURLToPath(import.meta.url);
export const __dirname = path.dirname(__filename);
```

不过注意：这样导出的 `__dirname` 是 `paths.mjs` 所在目录，不是引用它的文件所在目录。

如果希望每个模块都能得到自己的目录，应在每个模块内部写：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));
```

---

# 16. 对比总结表

| 对比项 | `__dirname` | `__filename` |
|---|---|---|
| 类型 | 字符串 | 字符串 |
| 含义 | 当前模块目录绝对路径 | 当前模块文件绝对路径 |
| 是否全局变量 | 否 | 否 |
| 是否 CommonJS 可用 | 是 | 是 |
| 是否 ESM 可用 | 否 | 否 |
| ESM 替代方式 | `path.dirname(fileURLToPath(import.meta.url))` | `fileURLToPath(import.meta.url)` |
| 是否受启动目录影响 | 否 | 否 |
| 是否受 `process.chdir()` 影响 | 否 | 否 |
| 常配合模块 | `path`、`fs` | `path`、`fs` |

---

# 17. 和 process.cwd() 的对比表

| 对比项 | `__dirname` | `process.cwd()` |
|---|---|---|
| 表示 | 当前文件所在目录 | 当前进程启动目录 |
| 是否随文件变化 | 是 | 否 |
| 是否随启动目录变化 | 否 | 是 |
| 是否受 `process.chdir()` 影响 | 否 | 是 |
| 适合场景 | 读取当前模块相关文件 | 读取当前项目或命令执行目录 |

---

# 18. 一句话总结

`__dirname` 和 `__filename` 是 CommonJS 模块中的两个局部变量：

```js
__dirname  // 当前文件所在目录
__filename // 当前文件完整绝对路径
```

它们最适合用来读取当前模块附近的文件：

```js
path.join(__dirname, 'config.json');
```

但在 ESM 中不可用，需要用：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
```