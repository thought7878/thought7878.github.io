下面是对 Node.js 内置 `path` 模块的详细总结。`path` 模块用于***处理和转换文件路径或目录路径***，是 Node.js 中做***路径拼接、解析、规范化、相对路径计算***时最常用的内置模块之一。

---

# 1. path 模块是什么

`path` 是 Node.js 的**内置模块**，提供了**一组用于处理文件路径的实用方法**。

它的特点是：

1. **只做字符串层面的路径处理**  
   `path` 不会访问文件系统，不会判断文件是否存在，也不会解析符号链接。

2. **跨平台处理路径**  
   Windows 和 POSIX 系统的路径规则不同，`path` 会根据当前运行平台自动使用对应实现。

3. **可以手动指定平台风格**  
   通过 `path.win32` 和 `path.posix`，可以强制使用 Windows 风格或 POSIX 风格路径处理。

---

# 2. 如何引入 path 模块

CommonJS：

```js
const path = require('node:path');
```

ESM：

```js
import path from 'node:path';
```

推荐写 `node:path`，这样可以明确表示使用的是 Node.js 内置模块，避免与用户项目中的同名模块混淆。

---

# 3. path 的平台差异

Node.js 的 `path` 模块会根据操作系统自动适配：

| 平台 | 路径分隔符 | 示例 |
|---|---|---|
| Windows | `\` | `C:\project\app.js` |
| Linux / macOS | `/` | `/home/user/app.js` |

例如：

```js
const path = require('node:path');

console.log(path.sep);
// Windows: "\\"
// POSIX: "/"

console.log(path.delimiter);
// Windows: ";"
// POSIX: ":"
```

---

# 4. path.sep 和 path.delimiter

## 4.1 path.sep

当前平台的文件路径分隔符。

```js
const path = require('node:path');

console.log(path.sep);
```

结果：

```text
Windows: \
POSIX: /
```

示例：

```js
const filePath = ['src', 'utils', 'index.js'].join(path.sep);

console.log(filePath);
// Windows: src\utils\index.js
// POSIX: src/utils/index.js
```

---

## 4.2 path.delimiter

当前平台的环境变量路径分隔符。

```js
const path = require('node:path');

console.log(path.delimiter);
```

结果：

```text
Windows: ;
POSIX: :
```

例如解析 `PATH` 环境变量：

```js
console.log(process.env.PATH.split(path.delimiter));
```

---

# 5. path.win32 和 path.posix

`path` 模块暴露了两个平台特定版本：

```js
const path = require('node:path');

console.log(path.win32.join('a', 'b', 'c'));
// a\b\c

console.log(path.posix.join('a', 'b', 'c'));
// a/b/c
```

说明：

| API | 说明 |
|---|---|
| `path.win32` | Windows 风格路径处理 |
| `path.posix` | POSIX 风格路径处理 |

在 Windows 上运行时，`path` 默认等价于 `path.win32`；在 Linux/macOS 上运行时，默认等价于 `path.posix`。

---

# 6. 核心方法总览

| 方法                        | 作用                 |
| ------------------------- | ------------------ |
| `path.join()`             | 拼接路径并规范化           |
| `path.resolve()`          | 解析为绝对路径            |
| `path.normalize()`        | 规范化路径              |
| `path.dirname()`          | 获取目录部分             |
| `path.basename()`         | 获取文件名部分            |
| `path.extname()`          | 获取扩展名              |
| `path.parse()`            | *将路径解析成对象*         |
| `path.format()`           | 将对象格式化成路径          |
| `path.isAbsolute()`       | 判断路径是否为绝对路径        |
| `path.relative()`         | 计算两个路径之间的相对路径      |
| `path.toNamespacedPath()` | 转换为 Windows 命名空间路径 |

---

# 7. path.join

## 7.1 基本用法

`path.join()` 用于**将多个路径片段拼接成一个路径，并自动进行规范化**。

```js
const path = require('node:path');

const p = path.join('src', 'utils', 'index.js');

console.log(p);
// Windows: src\utils\index.js
// POSIX: src/utils/index.js
```

---

## 7.2 自动处理多余分隔符

```js
const path = require('node:path');

console.log(path.join('/foo', 'bar//baz', 'asdf'));
```

POSIX 结果：

```text
/foo/bar/baz/asdf
```

Windows 结果：

```text
\foo\bar\baz\asdf
```

---

## 7.3 可以处理 `.` 和 `..`

```js
const path = require('node:path');

console.log(path.posix.join('/a', 'b', '../c'));
// /a/c
```

---

## 7.4 常见用途

拼接项目内文件路径：

```js
const path = require('node:path');

const configPath = path.join(__dirname, 'config', 'app.json');
```

注意：`__dirname` 只在 CommonJS 中可用，ESM 中需要自己构造。

---

# 8. path.resolve

## 8.1 基本用法

`path.resolve()` 用于将路径解析为绝对路径。

```js
const path = require('node:path');

console.log(path.resolve('src', 'index.js'));
```

如果当前目录是：

```text
/home/user/project
```

结果：

```text
/home/user/project/src/index.js
```

---

## 8.2 解析规则

`path.resolve()` 从右往左处理参数，直到解析出一个绝对路径。

如果所有参数都不是绝对路径，则会使用当前工作目录 `process.cwd()` 作为起点。

```js
path.resolve('a', 'b', 'c');
// 等价于 process.cwd() + '/a/b/c'
```

---

## 8.3 遇到绝对路径会重置当前路径

```js
const path = require('node:path');

console.log(path.posix.resolve('/foo', '/bar', 'baz'));
// /bar/baz
```

因为 `/bar` 是绝对路径，所以前面的 `/foo` 被忽略。

---

## 8.4 path.join 和 path.resolve 的区别

这是最容易混淆的两个方法。

## 区别一：是否返回绝对路径

```js
const path = require('node:path');

console.log(path.join('src', 'index.js'));
// src/index.js

console.log(path.resolve('src', 'index.js'));
// /当前工作目录/src/index.js
```

---

## 区别二：对绝对路径参数处理不同

```js
const path = require('node:path');

console.log(path.posix.join('/a', '/b'));
// /a/b

console.log(path.posix.resolve('/a', '/b'));
// /b
```

---

## 区别三：语义不同

| 方法 | 语义 |
|---|---|
| `path.join()` | 只是拼接路径片段 |
| `path.resolve()` | 模拟进入目录的过程，最终得到绝对路径 |

例如：

```js
path.join('/a', '../b');
// /b

path.resolve('/a', '../b');
// /b
```

看似一样，但当没有绝对路径参数时差异明显：

```js
path.join('a', 'b');
// a/b

path.resolve('a', 'b');
// /当前工作目录/a/b
```

---

# 9. path.normalize

`path.normalize()` 用于规范化路径。

它会处理：

1. 多余的分隔符；
2. `.`；
3. `..`；
4. 路径中的相对片段。

但它不会访问文件系统。

---

## 9.1 基本示例

```js
const path = require('node:path');

console.log(path.posix.normalize('/foo/bar//baz/asdf/quux/..'));
// /foo/bar/baz/asdf
```

---

## 9.2 不会解析符号链接

```js
const normalized = path.normalize('/foo/bar/../baz');
console.log(normalized);
// /baz
```

这只是字符串层面的处理。

如果需要真实路径，例如解析符号链接，需要使用：

```js
const fs = require('node:fs');

fs.realpath('/some/path', (err, resolvedPath) => {
  console.log(resolvedPath);
});
```

---

# 10. path.dirname

`path.dirname()` 用于获取路径中的目录部分。

```js
const path = require('node:path');

console.log(path.dirname('/home/user/project/index.js'));
// /home/user/project
```

Windows 示例：

```js
console.log(path.win32.dirname('C:\\project\\app.js'));
// C:\project
```

常见用途：

```js
const currentDir = path.dirname(__filename);
```

---

# 11. path.basename

`path.basename()` 用于获取路径中的最后一部分，通常是文件名。

## 11.1 基本用法

```js
const path = require('node:path');

console.log(path.basename('/home/user/project/index.js'));
// index.js
```

---

## 11.2 移除扩展名

第二个参数可以指定要移除的后缀：

```js
const path = require('node:path');

console.log(path.basename('/home/user/project/index.js', '.js'));
// index
```

注意：第二个参数必须精确匹配结尾部分。

```js
console.log(path.basename('/home/user/project/index.js', '.ts'));
// index.js
```

---

# 12. path.extname

`path.extname()` 用于获取路径中最后一个点的扩展名部分。

```js
const path = require('node:path');

console.log(path.extname('/home/user/project/index.js'));
// .js
```

---

## 12.1 多段点号

```js
console.log(path.extname('archive.tar.gz'));
// .gz
```

---

## 12.2 没有扩展名

```js
console.log(path.extname('README'));
// ''
```

---

## 12.3 隐藏文件

```js
console.log(path.extname('.env'));
// ''

console.log(path.extname('.env.local'));
// .local
```

---

# 13. path.parse

`path.parse()` *将路径字符串解析为一个对象*。

返回对象包含：

| 属性     | 含义         |
| ------ | ---------- |
| `root` | 根路径        |
| `dir`  | 目录路径       |
| `base` | 文件名加扩展名    |
| `ext`  | 扩展名        |
| `name` | 文件名，不包含扩展名 |

---

## 13.1 POSIX 示例

```js
const path = require('node:path');

console.log(path.posix.parse('/home/user/project/index.js'));
```

结果：

```js
{
  root: '/',
  dir: '/home/user/project',
  base: 'index.js',
  ext: '.js',
  name: 'index'
}
```

---

## 13.2 Windows 示例

```js
const path = require('node:path');

console.log(path.win32.parse('C:\\project\\src\\index.js'));
```

结果：

```js
{
  root: 'C:\\',
  dir: 'C:\\project\\src',
  base: 'index.js',
  ext: '.js',
  name: 'index'
}
```

---

# 14. path.format

`path.format()` 是 `path.parse()` 的反向操作。

它接收一个对象，返回路径字符串。

```js
const path = require('node:path');

const p = path.posix.format({
  dir: '/home/user/project',
  base: 'index.js'
});

console.log(p);
// /home/user/project/index.js
```

---

## 14.1 优先级规则

`path.format()` 有如下优先级：

1. 如果提供了 `dir`，则 `root` 会被忽略；
2. 如果提供了 `base`，则 `name` 和 `ext` 会被忽略；
3. 如果没有 `base`，则使用 `name` 和 `ext` 拼接。

示例：

```js
const path = require('node:path');

console.log(path.posix.format({
  root: '/',
  name: 'index',
  ext: '.js'
}));
// /index.js

console.log(path.posix.format({
  dir: '/home/user/project',
  name: 'ignored',
  ext: '.ignored',
  base: 'index.js'
}));
// /home/user/project/index.js
```

---

# 15. path.isAbsolute

`path.isAbsolute()` 用于判断路径是否是绝对路径。

## POSIX 示例

```js
const path = require('node:path');

console.log(path.posix.isAbsolute('/home/user'));
// true

console.log(path.posix.isAbsolute('./home/user'));
// false

console.log(path.posix.isAbsolute('home/user'));
// false
```

---

## Windows 示例

```js
const path = require('node:path');

console.log(path.win32.isAbsolute('C:\\project'));
// true

console.log(path.win32.isAbsolute('\\\\server\\share'));
// true

console.log(path.win32.isAbsolute('project\\app.js'));
// false
```

---

# 16. path.relative

`path.relative(from, to)` 用于计算从 `from` 到 `to` 的相对路径。

```js
const path = require('node:path');

const from = '/data/orandea/test/aaa';
const to = '/data/orandea/impl/bbb';

console.log(path.posix.relative(from, to));
// ../../impl/bbb
```

解释：

```text
从 /data/orandea/test/aaa
到 /data/orandea/impl/bbb
```

需要先向上返回两级：

```text
../../
```

然后进入：

```text
impl/bbb
```

---

## 16.1 Windows 示例

```js
const path = require('node:path');

const from = 'C:\\orandea\\test\\aaa';
const to = 'C:\\orandea\\impl\\bbb';

console.log(path.win32.relative(from, to));
// ..\..\impl\bbb
```

---

## 16.2 Windows 不同盘符

在 Windows 上，如果两个路径位于不同盘符，通常无法计算相对路径：

```js
const path = require('node:path');

console.log(path.win32.relative('C:\\foo', 'D:\\bar'));
// ''
```

返回空字符串。

---

# 17. path.toNamespacedPath

`path.toNamespacedPath()` 主要用于 Windows 平台，将路径转换为 Windows 命名空间路径。

```js
const path = require('node:path');

console.log(path.win32.toNamespacedPath('C:\\folder'));
// \\?\C:\folder

console.log(path.win32.toNamespacedPath('\\\\server\\share\\file.txt'));
// \\?\UNC\server\share\file.txt
```

在 POSIX 平台上，该方法通常原样返回路径：

```js
const path = require('node:path');

console.log(path.posix.toNamespacedPath('/home/user'));
// /home/user
```

这个方法主要用于 Windows 长路径、UNC 路径等特殊场景，普通业务代码中较少直接使用。

---

# 18. 常用方法对比

## 18.1 path.join vs path.resolve

```js
const path = require('node:path');

console.log(path.posix.join('/a', 'b', 'c'));
// /a/b/c

console.log(path.posix.resolve('/a', 'b', 'c'));
// /a/b/c

console.log(path.posix.join('/a', '/b'));
// /a/b

console.log(path.posix.resolve('/a', '/b'));
// /b
```

| 方法 | 主要用途 | 是否返回绝对路径 | 是否受 cwd 影响 |
|---|---|---|---|
| `path.join()` | 简单拼接路径 | 不一定 | 通常不受影响 |
| `path.resolve()` | 解析绝对路径 | 是 | 如果没有绝对路径，会使用 cwd |

---

## 18.2 path.parse vs path.format

```js
const path = require('node:path');

const obj = path.posix.parse('/home/user/index.js');
console.log(obj);

const str = path.posix.format(obj);
console.log(str);
```

| 方法 | 作用 |
|---|---|
| `path.parse()` | 字符串转路径对象 |
| `path.format()` | 路径对象转字符串 |

---

## 18.3 path.dirname vs path.basename vs path.extname

```js
const filePath = '/home/user/project/index.js';

console.log(path.dirname(filePath));
// /home/user/project

console.log(path.basename(filePath));
// index.js

console.log(path.extname(filePath));
// .js
```

| 方法 | 获取内容 |
|---|---|
| `path.dirname()` | 目录 |
| `path.basename()` | 文件名 |
| `path.extname()` | 扩展名 |

---

# 19. 常见使用场景

## 19.1 拼接当前模块所在目录下的文件路径

CommonJS：

```js
const path = require('node:path');

const configPath = path.join(__dirname, 'config', 'app.json');
```

---

## 19.2 基于当前工作目录解析绝对路径

```js
const path = require('node:path');

const outputPath = path.resolve('dist', 'index.html');
```

---

## 19.3 判断文件类型

```js
const path = require('node:path');

function isJavaScriptFile(filePath) {
  return path.extname(filePath) === '.js';
}

console.log(isJavaScriptFile('/src/index.js'));
// true
```

---

## 19.4 获取文件名

```js
const path = require('node:path');

const file = '/home/user/project/package.json';

console.log(path.basename(file));
// package.json

console.log(path.basename(file, '.json'));
// package
```

---

## 19.5 构造跨平台路径

```js
const path = require('node:path');

const filePath = ['src', 'components', 'Button.js'].join(path.sep);
```

不过更推荐直接使用 `path.join()`：

```js
const filePath = path.join('src', 'components', 'Button.js');
```

---

# 20. ESM 中如何获取 \_\_dirname 和 \_\_filename

在 CommonJS 中可以直接使用：

```js
console.log(__dirname);
console.log(__filename);
```

但在 ES Module 中，`__dirname` 和 `__filename` 不可用。

可以这样构造：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

console.log(__filename);
console.log(__dirname);
```

示例：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const configPath = path.join(__dirname, 'config.json');
```

---

# 21. path 与 URL 的关系

浏览器或 Node.js 中常见的 URL 路径和文件系统路径并不完全相同。

例如：

```text
file:///home/user/project/index.js
```

这是文件 URL。

要转成本地路径，可以使用：

```js
import { fileURLToPath } from 'node:url';

const filePath = fileURLToPath(new URL('./index.js', import.meta.url));
```

反过来，可以使用：

```js
import { pathToFileURL } from 'node:url';

const url = pathToFileURL('/home/user/project/index.js');

console.log(url.href);
```

---

# 22. path 模块不会做的事情

这是非常重要的一个认知。

`path` 模块只处理路径字符串，它不会：

1. 检查文件是否存在；
2. 检查目录是否存在；
3. 解析符号链接；
4. 访问文件系统；
5. 处理权限；
6. 判断路径最终是文件还是目录。

例如：

```js
const path = require('node:path');

const p = path.join(__dirname, 'not-exists.txt');

console.log(p);
```

即使文件不存在，`path.join()` 仍然可以正常返回路径字符串。

如果要判断文件是否存在，需要使用 `fs`：

```js
const fs = require('node:fs');

fs.existsSync(p);
```

如果需要解析真实路径：

```js
fs.realpathSync(p);
```

---

# 23. 常见坑和注意事项

## 23.1 不要手动拼接路径

不推荐：

```js
const p = dir + '/' + filename;
```

推荐：

```js
const p = path.join(dir, filename);
```

原因：

1. Windows 和 POSIX 分隔符不同；
2. 可能出现重复斜杠；
3. 可能出现 `.`、`..`；
4. 手动拼接容易出错。

---

## 23.2 path.join 不保证返回绝对路径

```js
path.join('src', 'index.js');
// src/index.js
```

如果需要绝对路径：

```js
path.resolve('src', 'index.js');
```

---

## 23.3 path.resolve 会使用 process.cwd()

```js
path.resolve('config.json');
```

等价于基于当前工作目录解析，而不是基于当前模块文件所在目录。

如果需要基于当前模块目录，应使用：

CommonJS：

```js
path.join(__dirname, 'config.json');
```

ESM：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const configPath = path.join(__dirname, 'config.json');
```

---

## 23.4 扩展名判断要注意点开头文件

```js
path.extname('.env');
// ''

path.extname('.env.local');
// .local
```

所以不能简单认为所有带点的文件都有扩展名。

---

## 23.5 path 是平台敏感的

例如 Windows 上的路径：

```text
C:\project\app.js
```

在 POSIX 中会被认为是一个普通文件名，而不是目录结构。

如果希望强制使用 POSIX 风格，可以使用：

```js
path.posix.join('a', 'b');
// a/b
```

如果希望强制使用 Windows 风格：

```js
path.win32.join('a', 'b');
// a\b
```

---

## 23.6 URL 路径不是文件路径

例如：

```js
'/foo/bar'
```

可能是 URL pathname。

但文件路径在 Windows 上可能是：

```text
C:\foo\bar
```

处理本地文件路径时，不要直接拿 `URL.pathname` 当文件路径使用，尤其是 Windows 下。

---

# 24. 实战示例

## 24.1 根据文件名读取同目录配置

CommonJS：

```js
const path = require('node:path');
const fs = require('node:fs');

const configPath = path.join(__dirname, 'config.json');

const content = fs.readFileSync(configPath, 'utf8');
```

---

## 24.2 ESM 中读取同级文件

```js
import path from 'node:path';
import fs from 'node:fs';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const configPath = path.join(__dirname, 'config.json');

const content = fs.readFileSync(configPath, 'utf8');
```

---

## 24.3 批量处理文件路径

```js
const path = require('node:path');

const files = ['a.js', 'b.css', 'c.png'];

const distFiles = files.map(file => {
  return path.join('dist', 'assets', file);
});

console.log(distFiles);
```

POSIX 结果类似：

```js
[
  'dist/assets/a.js',
  'dist/assets/b.css',
  'dist/assets/c.png'
]
```

---

## 24.4 根据扩展名分类文件

```js
const path = require('node:path');

const files = [
  '/project/src/index.js',
  '/project/src/style.css',
  '/project/assets/logo.png',
  '/project/docs/README'
];

const result = {};

for (const file of files) {
  const ext = path.extname(file) || '(no ext)';
  result[ext] ??= [];
  result[ext].push(file);
}

console.log(result);
```

输出类似：

```js
{
  '.js': ['/project/src/index.js'],
  '.css': ['/project/src/style.css'],
  '.png': ['/project/assets/logo.png'],
  '(no ext)': ['/project/docs/README']
}
```

---

## 24.5 构造输出目录和输出文件

```js
const path = require('node:path');

const root = process.cwd();

const outputDir = path.resolve(root, 'dist');
const outputFile = path.join(outputDir, 'index.html');

console.log(outputDir);
console.log(outputFile);
```

---

# 25. 常用速查表

## 25.1 路径拼接

```js
path.join('src', 'index.js');
```

---

## 25.2 解析绝对路径

```js
path.resolve('src', 'index.js');
```

---

## 25.3 获取目录

```js
path.dirname('/home/user/index.js');
// /home/user
```

---

## 25.4 获取文件名

```js
path.basename('/home/user/index.js');
// index.js
```

---

## 25.5 获取不带扩展名的文件名

```js
path.basename('/home/user/index.js', '.js');
// index
```

---

## 25.6 获取扩展名

```js
path.extname('/home/user/index.js');
// .js
```

---

## 25.7 判断绝对路径

```js
path.isAbsolute('/home/user');
```

---

## 25.8 计算相对路径

```js
path.relative('/a/b', '/a/c');
// ../c
```

---

## 25.9 解析路径对象

```js
path.parse('/home/user/index.js');
```

---

## 25.10 格式化路径对象

```js
path.format({
  dir: '/home/user',
  base: 'index.js'
});
```

---

# 26. 总结

`path` 模块是 Node.js 中处理文件路径的基础工具，核心可以概括为：

| 需求 | 推荐方法 |
|---|---|
| 拼接路径 | `path.join()` |
| 解析绝对路径 | `path.resolve()` |
| 规范化路径 | `path.normalize()` |
| 获取目录 | `path.dirname()` |
| 获取文件名 | `path.basename()` |
| 获取扩展名 | `path.extname()` |
| 路径转对象 | `path.parse()` |
| 对象转路径 | `path.format()` |
| 判断绝对路径 | `path.isAbsolute()` |
| 计算相对路径 | `path.relative()` |
| 跨平台分隔符 | `path.sep` |
| 环境变量分隔符 | `path.delimiter` |
| Windows/POSIX 强制处理 | `path.win32` / `path.posix` |

实际开发中最常用的组合是：

```js
path.join(__dirname, 'xxx');
```

以及：

```js
path.resolve(process.cwd(), 'xxx');
```

前者适合基于当前模块文件定位资源，后者适合基于当前工作目录定位资源。