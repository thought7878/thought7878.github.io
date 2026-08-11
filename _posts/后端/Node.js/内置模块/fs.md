下面主要详细总结 Node.js 文件处理核心模块：`fs`。

---

# 1. fs 模块是什么

`fs`（File System 文件系统） 是 Node.js 的**内置文件系统模块**，用于***增、删、改、查，文件、目录：***

- 读取文件；
- 写入文件；
- 追加文件；
- 删除文件；
- 复制文件；
- 移动文件；
- 创建目录；
- 删除目录；
- 读取目录；
- 获取文件信息；
- 修改权限；
- 创建符号链接；
- 监听文件变化；
- 使用文件流处理大文件；
- 操作文件描述符。

引入方式：

CommonJS：

```js
const fs = require('node:fs');
```

ESM：

```js
import fs from 'node:fs';
```

Promise 风格：

```js
const fs = require('node:fs/promises');
```

或：

```js
import fs from 'node:fs/promises';
```

---

# 2. fs 的三种 API 风格

Node.js 的 `fs` 模块提供三种使用风格：

| 风格 | 示例 | 特点 |
|---|---|---|
| 回调风格 | `fs.readFile()` | 异步，不阻塞事件循环 |
| 同步风格 | `fs.readFileSync()` | 阻塞主线程 |
| Promise 风格 | `fs.promises.readFile()` | 推荐现代写法 |

---

## 2.1 回调风格

```js
const fs = require('node:fs');

fs.readFile('./test.txt', 'utf8', (err, data) => {
  if (err) {
    console.error(err);
    return;
  }

  console.log(data);
});
```

---

## 2.2 同步风格

```js
const fs = require('node:fs');

try {
  const data = fs.readFileSync('./test.txt', 'utf8');
  console.log(data);
} catch (err) {
  console.error(err);
}
```

注意：同步 API 会阻塞 Node.js 主线程，不适合在高并发服务端逻辑中处理大文件或用户请求路径。

---

## 2.3 Promise 风格

推荐写法：

```js
const fs = require('node:fs/promises');

async function main() {
  try {
    const data = await fs.readFile('./test.txt', 'utf8');
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}

main();
```

ESM：

```js
import fs from 'node:fs/promises';

const data = await fs.readFile('./test.txt', 'utf8');
console.log(data);
```

---

# 3. 路径处理

文件操作几乎都离不开路径，通常配合 `path` 模块使用。

```js
const fs = require('node:fs');
const path = require('node:path');

const filePath = path.join(__dirname, 'data', 'test.txt');
```

CommonJS 中：

```js
const filePath = path.join(__dirname, 'test.txt');
```

ESM 中没有 `__dirname`，需要手动构造：

```js
import path from 'node:path';
import { fileURLToPath } from 'node:url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const filePath = path.join(__dirname, 'test.txt');
```

---

# 4. 读取文件

## 4.1 fs.readFile

一次性读取整个文件。

```js
const fs = require('node:fs/promises');

async function main() {
  const data = await fs.readFile('./test.txt', 'utf8');
  console.log(data);
}

main();
```

如果不指定编码，返回的是 `Buffer`：

```js
const data = await fs.readFile('./test.txt');
console.log(data);
```

指定编码：

```js
const data = await fs.readFile('./test.txt', { encoding: 'utf8' });
```

---

## 4.2 fs.readFileSync

```js
const fs = require('node:fs');

const data = fs.readFileSync('./test.txt', 'utf8');
console.log(data);
```

适合：

- 启动时读取配置；
- 脚本任务；
- 小文件读取。

不适合：

- 高并发接口；
- 大文件读取；
- 请求处理链路中的同步读取。

---

## 4.3 大文件读取

大文件不建议一次性读入内存，应使用流：

```js
const fs = require('node:fs');

const stream = fs.createReadStream('./big-file.txt', 'utf8');

stream.on('data', chunk => {
  console.log('读取到一段数据');
});

stream.on('end', () => {
  console.log('读取完成');
});

stream.on('error', err => {
  console.error(err);
});
```

---

# 5. 写入文件

## 5.1 fs.writeFile

覆盖写入。

```js
const fs = require('node:fs/promises');

async function main() {
  await fs.writeFile('./test.txt', 'Hello Node.js', 'utf8');
}

main();
```

如果文件不存在，会创建文件。

如果文件存在，默认会覆盖原内容。

---

## 5.2 fs.writeFileSync

```js
const fs = require('node:fs');

fs.writeFileSync('./test.txt', 'Hello Node.js', 'utf8');
```

---

## 5.3 追加内容

使用 `appendFile`：

```js
const fs = require('node:fs/promises');

await fs.appendFile('./log.txt', '追加一行日志\n', 'utf8');
```

或者写入时指定 flag：

```js
await fs.writeFile('./log.txt', '追加内容\n', {
  encoding: 'utf8',
  flag: 'a'
});
```

---

## 5.4 常见写入 flags

| flag | 说明 |
|---|---|
| `r` | 只读 |
| `r+` | 读写 |
| `w` | 写入，文件不存在则创建，存在则清空 |
| `wx` | 写入，文件存在则失败 |
| `w+` | 读写，文件不存在则创建，存在则清空 |
| `a` | 追加，文件不存在则创建 |
| `ax` | 追加，文件存在则失败 |
| `a+` | 读取并追加 |

示例：

```js
await fs.writeFile('./test.txt', '内容', {
  flag: 'wx'
});
```

如果文件已存在，会报错：

```text
EEXIST
```

---

# 6. 复制文件

## fs.copyFile

```js
const fs = require('node:fs/promises');

await fs.copyFile('./source.txt', './target.txt');
```

常见 flags：

```js
const fs = require('node:fs');
const fsp = require('node:fs/promises');

await fsp.copyFile('./source.txt', './target.txt', fs.constants.COPYFILE_EXCL);
```

| 常量 | 说明 |
|---|---|
| `fs.constants.COPYFILE_EXCL` | 目标文件存在时失败 |
| `fs.constants.COPYFILE_FICLONE` | 尝试写时复制 |
| `fs.constants.COPYFILE_FICLONE_FORCE` | 强制使用写时复制 |

---

# 7. 移动和重命名

## fs.rename

```js
const fs = require('node:fs/promises');

await fs.rename('./old.txt', './new.txt');
```

也可以用于移动文件：

```js
await fs.rename('./data/old.txt', './backup/new.txt');
```

注意：目标目录必须存在，否则可能报错。

---

# 8. 删除文件

## fs.unlink

删除文件：

```js
const fs = require('node:fs/promises');

await fs.unlink('./test.txt');
```

如果文件不存在，会报错：

```text
ENOENT
```

---

# 9. 删除目录和文件

## fs.rm

现代 Node.js 更推荐使用 `fs.rm`。

删除文件：

```js
const fs = require('node:fs/promises');

await fs.rm('./test.txt');
```

递归删除目录：

```js
await fs.rm('./temp', { recursive: true });
```

强制删除，不存在也不报错：

```js
await fs.rm('./temp', { recursive: true, force: true });
```

常用配置：

| 参数 | 说明 |
|---|---|
| `recursive` | 递归删除目录内容 |
| `force` | 文件不存在时不报错 |

示例：

```js
await fs.rm('./dist', {
  recursive: true,
  force: true
});
```

---

# 10. 创建目录

## fs.mkdir

创建单层目录：

```js
const fs = require('node:fs/promises');

await fs.mkdir('./logs');
```

递归创建目录：

```js
await fs.mkdir('./logs/app/2026/07', { recursive: true });
```

如果目录已存在：

- 不加 `recursive` 会报错；
- 加 `recursive` 通常不会报错。

---

# 11. 创建临时目录

```js
const fs = require('node:fs/promises');
const os = require('node:os');
const path = require('node:path');

const tempDir = await fs.mkdtemp(path.join(os.tmpdir(), 'app-'));

console.log(tempDir);
```

适合生成唯一临时目录，避免文件名冲突。

---

# 12. 读取目录

## fs.readdir

```js
const fs = require('node:fs/promises');

const files = await fs.readdir('./src');

console.log(files);
```

默认返回文件名数组：

```js
['index.js', 'utils.js', 'components']
```

如果希望获取更详细信息，可以使用：

```js
const entries = await fs.readdir('./src', { withFileTypes: true });

for (const entry of entries) {
  console.log(entry.name);

  if (entry.isFile()) {
    console.log('这是文件');
  }

  if (entry.isDirectory()) {
    console.log('这是目录');
  }

  if (entry.isSymbolicLink()) {
    console.log('这是符号链接');
  }
}
```

---

# 13. 递归读取目录

Node.js 较新版本支持：

```js
const fs = require('node:fs/promises');

const files = await fs.readdir('./src', { recursive: true });

console.log(files);
```

如果需要兼容旧版本，可以手动递归：

```js
const fs = require('node:fs/promises');
const path = require('node:path');

async function walk(dir) {
  const entries = await fs.readdir(dir, { withFileTypes: true });

  const files = [];

  for (const entry of entries) {
    const fullPath = path.join(dir, entry.name);

    if (entry.isDirectory()) {
      const nested = await walk(fullPath);
      files.push(...nested);
    } else {
      files.push(fullPath);
    }
  }

  return files;
}

const allFiles = await walk('./src');
console.log(allFiles);
```

---

# 14. 获取文件信息

## fs.stat

```js
const fs = require('node:fs/promises');

const stats = await fs.stat('./test.txt');

console.log(stats);
```

常用属性和方法：

| 属性 / 方法 | 说明 |
|---|---|
| `stats.isFile()` | 是否是文件 |
| `stats.isDirectory()` | 是否是目录 |
| `stats.isSymbolicLink()` | 是否是符号链接 |
| `stats.size` | 文件大小，单位字节 |
| `stats.atime` | 最后访问时间 |
| `stats.mtime` | 最后修改时间 |
| `stats.ctime` | 状态改变时间 |
| `stats.birthtime` | 创建时间，部分平台支持 |

示例：

```js
const fs = require('node:fs/promises');

async function printFileInfo(filePath) {
  const stats = await fs.stat(filePath);

  console.log('是否文件:', stats.isFile());
  console.log('是否目录:', stats.isDirectory());
  console.log('文件大小:', stats.size);
  console.log('最后修改时间:', stats.mtime);
}

printFileInfo('./test.txt');
```

---

## fs.lstat

`fs.lstat` 和 `fs.stat` 类似，但不会跟随符号链接。

```js
const stats = await fs.lstat('./link');
```

如果路径是符号链接：

- `fs.stat()` 返回链接指向的目标；
- `fs.lstat()` 返回链接本身。

---

# 15. 判断文件或目录是否存在

## 方式一：fs.access

```js
const fs = require('node:fs/promises');

async function exists(filePath) {
  try {
    await fs.access(filePath);
    return true;
  } catch {
    return false;
  }
}

console.log(await exists('./test.txt'));
```

---

## 方式二：fs.stat

```js
const fs = require('node:fs/promises');

async function exists(filePath) {
  try {
    await fs.stat(filePath);
    return true;
  } catch {
    return false;
  }
}
```

---

## 不推荐先判断再操作

例如：

```js
if (await exists('./test.txt')) {
  await fs.readFile('./test.txt');
}
```

这种写法存在竞态问题：判断之后、读取之前，文件可能被删除。

更推荐直接操作并捕获错误：

```js
try {
  const data = await fs.readFile('./test.txt', 'utf8');
} catch (err) {
  if (err.code === 'ENOENT') {
    console.log('文件不存在');
  } else {
    throw err;
  }
}
```

---

# 16. 文件权限检查

## fs.access

```js
const fs = require('node:fs/promises');
const fsBase = require('node:fs');

await fs.access('./test.txt', fsBase.constants.R_OK);
```

常用常量：

| 常量 | 说明 |
|---|---|
| `fs.constants.F_OK` | 文件是否存在 |
| `fs.constants.R_OK` | 是否可读 |
| `fs.constants.W_OK` | 是否可写 |
| `fs.constants.X_OK` | 是否可执行 |

示例：

```js
const fs = require('node:fs');
const fsp = require('node:fs/promises');

async function checkWritable(filePath) {
  try {
    await fsp.access(filePath, fs.constants.W_OK);
    return true;
  } catch {
    return false;
  }
}
```

---

# 17. 修改权限和所有者

## fs.chmod

```js
await fs.chmod('./test.txt', 0o644);
```

常见权限：

| 权限 | 含义 |
|---|---|
| `0o644` | 所有者可读写，其他人只读 |
| `0o755` | 所有者可读写执行，其他人读和执行 |
| `0o600` | 只有所有者可读写 |

---

## fs.chown

```js
await fs.chown('./test.txt', uid, gid);
```

Windows 上部分权限和所有者 API 支持有限。

---

# 18. 修改文件时间

## fs.utimes

```js
const fs = require('node:fs/promises');

await fs.utimes('./test.txt', new Date(), new Date());
```

参数分别是：

```js
fs.utimes(path, atime, mtime);
```

- `atime`：访问时间；
- `mtime`：修改时间。

---

# 19. 符号链接

## fs.symlink

创建符号链接：

```js
const fs = require('node:fs/promises');

await fs.symlink('./target.txt', './link.txt');
```

Windows 上创建符号链接可能需要权限。

---

## fs.readlink

读取符号链接指向：

```js
const target = await fs.readlink('./link.txt');
console.log(target);
```

---

## fs.realpath

将路径解析为真实路径：

```js
const realPath = await fs.realpath('./link.txt');
console.log(realPath);
```

它会解析符号链接。

---

# 20. 文件流

处理大文件时，流非常重要。

---

## 20.1 读取流

```js
const fs = require('node:fs');

const readStream = fs.createReadStream('./big.txt', {
  encoding: 'utf8',
  highWaterMark: 64 * 1024
});

readStream.on('data', chunk => {
  console.log('读取到', chunk.length, '字节');
});

readStream.on('end', () => {
  console.log('读取结束');
});

readStream.on('error', err => {
  console.error(err);
});
```

---

## 20.2 写入流

```js
const fs = require('node:fs');

const writeStream = fs.createWriteStream('./output.txt', 'utf8');

writeStream.write('第一行\n');
writeStream.write('第二行\n');

writeStream.end('最后一行\n');

writeStream.on('finish', () => {
  console.log('写入完成');
});

writeStream.on('error', err => {
  console.error(err);
});
```

---

## 20.3 管道 pipe

```js
const fs = require('node:fs');

const readStream = fs.createReadStream('./source.txt');
const writeStream = fs.createWriteStream('./target.txt');

readStream.pipe(writeStream);
```

---

## 20.4 推荐使用 pipeline

`pipeline` 可以更好地处理错误和流关闭。

```js
const fs = require('node:fs');
const { pipeline } = require('node:stream/promises');

async function copyFile() {
  await pipeline(
    fs.createReadStream('./source.txt'),
    fs.createWriteStream('./target.txt')
  );

  console.log('复制完成');
}

copyFile();
```

---

## 20.5 背压 backpressure

写入流时，如果 `write()` 返回 `false`，说明内部缓冲区已满，应等待 `drain` 事件。

```js
const fs = require('node:fs');

const stream = fs.createWriteStream('./big.txt');

for (let i = 0; i < 100000; i++) {
  const ok = stream.write('line\n');

  if (!ok) {
    await new Promise(resolve => stream.once('drain', resolve));
  }
}

stream.end();
```

---

# 21. 文件描述符

Node.js 可以打开文件描述符进行更底层操作。

## fs.open

Promise 风格会返回 `FileHandle`：

```js
const fs = require('node:fs/promises');

const handle = await fs.open('./test.txt', 'r');

try {
  const stats = await handle.stat();
  console.log(stats.size);

  const buffer = Buffer.alloc(stats.size);
  await handle.read(buffer, 0, buffer.length, 0);

  console.log(buffer.toString('utf8'));
} finally {
  await handle.close();
}
```

---

# 22. 监听文件变化

## fs.watch

```js
const fs = require('node:fs');

const watcher = fs.watch('./test.txt', (eventType, filename) => {
  console.log(eventType, filename);
});

watcher.on('error', err => {
  console.error(err);
});
```

常见事件：

| eventType | 说明 |
|---|---|
| `change` | 文件内容变化 |
| `rename` | 文件重命名或删除 |

注意：`fs.watch` 在不同平台行为不完全一致。

---

## 监听目录

```js
const fs = require('node:fs');

const watcher = fs.watch('./logs', (eventType, filename) => {
  console.log(eventType, filename);
});
```

递归监听：

```js
const watcher = fs.watch('./src', { recursive: true }, (eventType, filename) => {
  console.log(eventType, filename);
});
```

注意：`recursive` 在不同平台支持情况不同。

---

## fs.watchFile

基于轮询，兼容性更好，但性能较低。

```js
const fs = require('node:fs');

fs.watchFile('./test.txt', (curr, prev) => {
  console.log('文件大小:', curr.size);
});
```

停止监听：

```js
fs.unwatchFile('./test.txt');
```

---

# 23. 文件复制目录

Node.js 较新版本支持 `fs.cp`。

复制文件：

```js
const fs = require('node:fs/promises');

await fs.cp('./source.txt', './target.txt');
```

递归复制目录：

```js
await fs.cp('./source-dir', './target-dir', {
  recursive: true
});
```

常用选项：

```js
await fs.cp('./source-dir', './target-dir', {
  recursive: true,
  force: true,
  preserveTimestamps: true
});
```

---

# 24. 常见错误码

| 错误码 | 含义 |
|---|---|
| `ENOENT` | 文件或目录不存在 |
| `EACCES` | 权限不足 |
| `EPERM` | 操作不允许 |
| `EEXIST` | 文件或目录已存在 |
| `EISDIR` | 目标是目录，不是文件 |
| `ENOTDIR` | 目标不是目录 |
| `ENOTEMPTY` | 目录不为空 |
| `EBUSY` | 资源忙 |
| `EMFILE` | 打开文件句柄过多 |

示例：

```js
try {
  await fs.readFile('./no.txt');
} catch (err) {
  if (err.code === 'ENOENT') {
    console.log('文件不存在');
  } else {
    throw err;
  }
}
```

---

# 25. 实战示例

---

## 25.1 读取 JSON 配置文件

```js
const fs = require('node:fs/promises');
const path = require('node:path');

async function loadConfig(filePath) {
  const content = await fs.readFile(filePath, 'utf8');
  return JSON.parse(content);
}

const config = await loadConfig(path.join(__dirname, 'config.json'));
console.log(config);
```

---

## 25.2 写入 JSON 配置文件

```js
const fs = require('node:fs/promises');

async function saveConfig(filePath, data) {
  const json = JSON.stringify(data, null, 2);
  await fs.writeFile(filePath, json, 'utf8');
}

await saveConfig('./config.json', {
  port: 3000
});
```

---

## 25.3 安全写入文件

先写临时文件，再重命名，可减少写入中途失败导致文件损坏的问题。

```js
const fs = require('node:fs/promises');
const path = require('node:path');

async function safeWriteFile(filePath, data) {
  const tempPath = `${filePath}.tmp`;

  await fs.writeFile(tempPath, data, 'utf8');
  await fs.rename(tempPath, filePath);
}

await safeWriteFile('./config.json', JSON.stringify({ a: 1 }, null, 2));
```

---

## 25.4 递归删除目录

```js
const fs = require('node:fs/promises');

await fs.rm('./dist', {
  recursive: true,
  force: true
});
```

---

## 25.5 递归创建目录并写日志

```js
const fs = require('node:fs/promises');
const path = require('node:path');

async function writeLog(logDir, message) {
  await fs.mkdir(logDir, { recursive: true });

  const date = new Date().toISOString().slice(0, 10);
  const logFile = path.join(logDir, `${date}.log`);

  await fs.appendFile(logFile, `${new Date().toISOString()} ${message}\n`, 'utf8');
}

await writeLog('./logs', '系统启动');
```

---

## 25.6 批量读取目录中的图片文件

```js
const fs = require('node:fs/promises');
const path = require('node:path');

async function findImages(dir) {
  const entries = await fs.readdir(dir, { withFileTypes: true });

  const images = [];

  for (const entry of entries) {
    const fullPath = path.join(dir, entry.name);

    if (entry.isDirectory()) {
      images.push(...await findImages(fullPath));
      continue;
    }

    const ext = path.extname(entry.name).toLowerCase();

    if (['.jpg', '.jpeg', '.png', '.gif', '.webp', '.svg'].includes(ext)) {
      images.push(fullPath);
    }
  }

  return images;
}

const images = await findImages('./assets');
console.log(images);
```

---

# 26. Web File / Blob 在 Node.js 中的情况

如果你说的 “file” 是浏览器里的 `File` 对象，Node.js 新版本也支持全局 `File` 和 `Blob`。

例如：

```js
const file = new File(['hello'], 'hello.txt', {
  type: 'text/plain'
});

console.log(file.name);
console.log(file.type);
console.log(file.size);
```

配合 `FormData`：

```js
const formData = new FormData();

const file = new File(['hello'], 'hello.txt', {
  type: 'text/plain'
});

formData.append('file', file);
```

配合 `fetch` 上传：

```js
const formData = new FormData();
const file = new File(['hello'], 'hello.txt', { type: 'text/plain' });

formData.append('file', file);

await fetch('https://example.com/upload', {
  method: 'POST',
  body: formData
});
```

但要注意：

- `File` 不是文件系统操作模块；
- 真正的服务器文件读写仍然使用 `fs`；
- `File` / `Blob` 更多用于 HTTP 上传、二进制数据、表单数据等场景。

---

# 27. 常见坑点

---

## 27.1 Node.js 没有内置 `node:file`

错误引入：

```js
const file = require('node:file');
```

会失败。

正确写法：

```js
const fs = require('node:fs');
const fsp = require('node:fs/promises');
```

---

## 27.2 readFile 会占用大量内存

```js
await fs.readFile('./huge-file.txt');
```

如果文件很大，会一次性读入内存。

大文件应使用：

```js
fs.createReadStream();
```

---

## 27.3 writeFileSync 会阻塞主线程

```js
fs.writeFileSync('./big.txt', data);
```

在服务端应避免大文件同步写入。

推荐：

```js
await fs.writeFile('./big.txt', data);
```

或：

```js
fs.createWriteStream();
```

---

## 27.4 路径拼接要小心

不推荐：

```js
const p = dir + '/' + filename;
```

推荐：

```js
const p = path.join(dir, filename);
```

或：

```js
const p = path.resolve(dir, filename);
```

---

## 27.5 用户输入路径要防路径穿越

例如：

```js
const filePath = path.join(uploadDir, userInput);
```

如果 `userInput` 是：

```text
../../etc/passwd
```

就可能访问到上传目录之外的文件。

应做限制：

```js
const fs = require('node:fs');
const path = require('node:path');

function safeResolve(baseDir, userPath) {
  const resolvedBase = path.resolve(baseDir);
  const resolvedPath = path.resolve(resolvedBase, userPath);

  if (!resolvedPath.startsWith(resolvedBase + path.sep)) {
    throw new Error('非法路径');
  }

  return resolvedPath;
}
```

---

## 27.6 fs.watch 平台差异较大

`fs.watch` 在 Linux、macOS、Windows 上的行为可能不同。

对于构建工具、文件同步工具等复杂场景，通常使用更成熟的库，例如：

- `chokidar`
- `watchman`
- `nsfw`

---

## 27.7 删除目录推荐 fs.rm，不推荐旧 rmdir

旧写法：

```js
fs.rmdir(dir, { recursive: true });
```

现在更推荐：

```js
await fs.rm(dir, { recursive: true, force: true });
```

---

## 27.8 Windows 权限和符号链接限制

Windows 上：

- `chmod` 支持有限；
- `chown` 支持有限；
- 创建符号链接可能需要权限；
- 文件路径分隔符是 `\`；
- 部分 Linux 文件语义不可用。

跨平台代码要谨慎。

---

# 28. fs 常用方法速查表

| 方法 | 作用 |
|---|---|
| `fs.readFile()` | 读取文件 |
| `fs.writeFile()` | 写入文件 |
| `fs.appendFile()` | 追加文件 |
| `fs.copyFile()` | 复制文件 |
| `fs.rename()` | 重命名或移动 |
| `fs.unlink()` | 删除文件 |
| `fs.rm()` | 删除文件或目录 |
| `fs.mkdir()` | 创建目录 |
| `fs.readdir()` | 读取目录 |
| `fs.mkdtemp()` | 创建临时目录 |
| `fs.stat()` | 获取文件信息 |
| `fs.lstat()` | 获取符号链接本身信息 |
| `fs.access()` | 检查访问权限 |
| `fs.chmod()` | 修改权限 |
| `fs.chown()` | 修改所有者 |
| `fs.utimes()` | 修改时间 |
| `fs.symlink()` | 创建符号链接 |
| `fs.readlink()` | 读取符号链接 |
| `fs.realpath()` | 解析真实路径 |
| `fs.createReadStream()` | 创建读取流 |
| `fs.createWriteStream()` | 创建写入流 |
| `fs.watch()` | 监听文件变化 |
| `fs.watchFile()` | 轮询监听文件变化 |
| `fs.cp()` | 复制文件或目录 |

---

# 29. 推荐实践

## 29.1 优先使用 fs/promises

推荐：

```js
import fs from 'node:fs/promises';
```

而不是大量回调：

```js
fs.readFile(path, (err, data) => {});
```

---

## 29.2 小配置用同步，服务请求用异步

启动阶段：

```js
const config = JSON.parse(fs.readFileSync('./config.json', 'utf8'));
```

请求处理阶段：

```js
const config = await fs.readFile('./config.json', 'utf8');
```

---

## 29.3 大文件用流

```js
import { createReadStream, createWriteStream } from 'node:fs';
import { pipeline } from 'node:stream/promises';

await pipeline(
  createReadStream('./source.mp4'),
  createWriteStream('./target.mp4')
);
```

---

## 29.4 目录操作加 recursive

```js
await fs.mkdir(dir, { recursive: true });
await fs.rm(dir, { recursive: true, force: true });
```

---

## 29.5 写入重要文件使用临时文件 + rename

```js
await fs.writeFile(tmpPath, data);
await fs.rename(tmpPath, targetPath);
```

---

# 30. 最终总结

Node.js 中没有内置 `file` 模块。处理文件应使用：

```js
node:fs
```

或者：

```js
node:fs/promises
```

核心记忆：

| 需求 | 推荐 API |
|---|---|
| 读文件 | `fs.readFile()` |
| 写文件 | `fs.writeFile()` |
| 追加文件 | `fs.appendFile()` |
| 复制文件 | `fs.copyFile()` |
| 复制目录 | `fs.cp()` |
| 移动文件 | `fs.rename()` |
| 删除文件 | `fs.unlink()` |
| 删除目录 | `fs.rm()` |
| 创建目录 | `fs.mkdir()` |
| 读取目录 | `fs.readdir()` |
| 文件信息 | `fs.stat()` |
| 大文件 | `fs.createReadStream()` / `fs.createWriteStream()` |
| 文件监听 | `fs.watch()` |
| 临时目录 | `fs.mkdtemp()` |
| 权限检查 | `fs.access()` |
| 符号链接 | `fs.symlink()` / `fs.readlink()` / `fs.realpath()` |

如果你说的 “file” 是浏览器风格的 `File` 对象，那么 Node.js 支持全局 `File`，但它主要用于 `FormData`、`fetch`、上传等 Web API 场景，不是文件系统模块。