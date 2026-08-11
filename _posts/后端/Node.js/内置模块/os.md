下面详细总结 Node.js 内置 os 模块。

`os` 模块用于**获取操作系统相关信息，例如：**
- 操作系统类型；
- CPU 信息；
- 内存信息；
- 主机名；
- 用户主目录；
- 临时目录；
- 网络接口；
- 系统负载；
- 系统运行时间；
- 用户信息；
- 进程优先级；
- 操作系统相关常量。

---

# 1. 引入 os 模块

CommonJS：

```js
const os = require('node:os');
```

ESM：

```js
import os from 'node:os';
```

推荐写 `node:os`，**明确表示这是 Node.js 内置模块**。

---

# 2. os 模块的基本特点

`os` 模块有几个重要特点：

1. **只读取操作系统信息**  
   它不会修改系统核心配置，主要是查询型 API。

2. **大多数方法都是同步方法**  
   例如 `os.cpus()`、`os.freemem()`、`os.networkInterfaces()` 等都会立即返回结果。

3. **不同平台返回结果可能不同**  
   Windows、Linux、macOS 的行为和字段可能存在差异。

4. **部分 API 在某些平台不可用或返回固定值**  
   例如 `os.loadavg()` 在 Windows 上通常返回 `[0, 0, 0]`。

---

# 3. 常用方法总览

| 方法 / 属性 | 作用 |
|---|---|
| `os.platform()` | 获取操作系统平台 |
| `os.arch()` | 获取 CPU 架构 |
| `os.type()` | 获取操作系统类型 |
| `os.release()` | 获取操作系统发行版本号 |
| `os.version()` | 获取操作系统版本信息 |
| `os.hostname()` | 获取主机名 |
| `os.homedir()` | 获取当前用户主目录 |
| `os.tmpdir()` | 获取操作系统临时目录 |
| `os.uptime()` | 获取系统运行时间 |
| `os.totalmem()` | 获取系统总内存 |
| `os.freemem()` | 获取系统可用内存 |
| `os.cpus()` | 获取 CPU 核心信息 |
| `os.availableParallelism()` | 获取当前可用并行度 |
| `os.loadavg()` | 获取系统平均负载 |
| `os.networkInterfaces()` | 获取网络接口信息 |
| `os.userInfo()` | 获取当前用户信息 |
| `os.endianness()` | 获取 CPU 字节序 |
| `os.machine()` | 获取机器类型 |
| `os.getPriority()` | 获取进程优先级 |
| `os.setPriority()` | 设置进程优先级 |
| `os.EOL` | 当前平台换行符 |
| `os.devNull` | 当前平台空设备路径 |
| `os.constants` | 操作系统相关常量 |

---

# 4. 操作系统基本信息

---

## 4.1 os.platform()

返回 Node.js 进程运行的操作系统平台。

```js
const os = require('node:os');

console.log(os.platform());
```

常见返回值：

| 平台 | 返回值 |
|---|---|
| Windows | `'win32'` |
| macOS | `'darwin'` |
| Linux | `'linux'` |
| FreeBSD | `'freebsd'` |
| OpenBSD | `'openbsd'` |
| Solaris | `'sunos'` |
| AIX | `'aix'` |

示例：

```js
if (os.platform() === 'win32') {
  console.log('当前是 Windows 系统');
}

if (os.platform() === 'darwin') {
  console.log('当前是 macOS 系统');
}

if (os.platform() === 'linux') {
  console.log('当前是 Linux 系统');
}
```

注意：即使是在 64 位 Windows 上，Node.js 也返回：

```js
'win32'
```

这是历史命名，不代表一定是 32 位系统。

---

## 4.2 os.arch()

返回 Node.js 二进制文件编译时的 CPU 架构。

```js
const os = require('node:os');

console.log(os.arch());
```

常见返回值：

| 架构 | 返回值 |
|---|---|
| 64 位 x86 | `'x64'` |
| ARM 64 位 | `'arm64'` |
| 32 位 x86 | `'ia32'` |
| ARM 32 位 | `'arm'` |
| IBM S/390 | `'s390'` |
| IBM S/390 64 位 | `'s390x'` |
| PowerPC 64 位 | `'ppc64'` |
| RISC-V 64 位 | `'riscv64'` |

示例：

```js
console.log(os.arch());
// 可能是 x64、arm64 等
```

注意：`os.arch()` 返回的是当前 Node.js 进程对应的架构，不一定是底层硬件真实架构。

例如在 Apple Silicon Mac 上运行 x64 版本 Node.js，可能返回：

```text
x64
```

而不是：

```text
arm64
```

---

## 4.3 os.type()

返回操作系统类型。

```js
const os = require('node:os');

console.log(os.type());
```

常见返回值：

| 系统 | 返回值 |
|---|---|
| Linux | `'Linux'` |
| macOS | `'Darwin'` |
| Windows | `'Windows_NT'` |

示例：

```js
console.log(os.type());
// Linux / Darwin / Windows_NT
```

---

## 4.4 os.release()

返回操作系统发行版本号。

```js
const os = require('node:os');

console.log(os.release());
```

示例输出可能为：

```text
Windows: 10.0.22631
Linux: 6.5.0-14-generic
macOS: 23.1.0
```

这个值更接近内核版本或系统构建版本。

---

## 4.5 os.version()

返回更详细的操作系统版本信息。

```js
const os = require('node:os');

console.log(os.version());
```

不同平台返回格式不同。

例如可能返回：

```text
Darwin Kernel Version ...
Linux version ...
Windows 11 Pro ...
```

相比 `os.release()`，`os.version()` 通常包含更多可读信息。

---

## 4.6 os.hostname()

返回操作系统主机名。

```js
const os = require('node:os');

console.log(os.hostname());
```

示例：

```text
my-computer
```

注意：它返回的是操作系统层面的主机名，不一定等于完整 DNS 域名。

---

## 4.7 os.machine()

返回机器类型信息，较新的 Node.js 版本提供。

```js
const os = require('node:os');

console.log(os.machine());
```

可能返回：

```text
x86_64
arm64
aarch64
```

它和 `os.arch()` 有一定重叠，但更偏向底层机器类型。

---

# 5. 系统运行时间

## os.uptime()

返回操作系统已经运行的时间，单位是秒。

```js
const os = require('node:os');

const seconds = os.uptime();

console.log(seconds);
```

可以转换成更可读的格式：

```js
const os = require('node:os');

function formatUptime(seconds) {
  const days = Math.floor(seconds / 86400);
  const hours = Math.floor((seconds % 86400) / 3600);
  const minutes = Math.floor((seconds % 3600) / 60);
  const secs = Math.floor(seconds % 60);

  return `${days}天 ${hours}小时 ${minutes}分钟 ${secs}秒`;
}

console.log(formatUptime(os.uptime()));
```

注意区分：

| API | 含义 |
|---|---|
| `os.uptime()` | 操作系统运行时间 |
| `process.uptime()` | 当前 Node.js 进程运行时间 |

---

# 6. 内存信息

---

## 6.1 os.totalmem()

返回系统总内存，单位是字节。

```js
const os = require('node:os');

console.log(os.totalmem());
```

示例：

```text
17179869184
```

转换成 GB：

```js
const os = require('node:os');

function bytesToGB(bytes) {
  return `${(bytes / 1024 ** 3).toFixed(2)} GB`;
}

console.log(bytesToGB(os.totalmem()));
// 16.00 GB
```

---

## 6.2 os.freemem()

返回系统当前可用内存，单位是字节。

```js
const os = require('node:os');

console.log(os.freemem());
```

示例：

```js
const os = require('node:os');

function bytesToGB(bytes) {
  return `${(bytes / 1024 ** 3).toFixed(2)} GB`;
}

console.log(`总内存: ${bytesToGB(os.totalmem())}`);
console.log(`可用内存: ${bytesToGB(os.freemem())}`);
```

注意：

1. `os.freemem()` 返回的是操作系统层面的空闲内存；
2. 它不等于当前 Node.js 进程可用内存；
3. 在容器、虚拟机、cgroup 限制环境下，结果可能不代表真实可分配资源；
4. 如果要查看当前 Node.js 进程内存，应使用：

```js
console.log(process.memoryUsage());
```

---

# 7. CPU 信息

---

## 7.1 os.cpus()

返回一个数组，每个元素代表一个逻辑 CPU 核心的信息。

```js
const os = require('node:os');

console.log(os.cpus());
```

返回结构类似：

```js
[
  {
    model: 'Intel(R) Core(TM) i7-9750H CPU @ 2.60GHz',
    speed: 2592,
    times: {
      user: 123456,
      nice: 0,
      sys: 65432,
      idle: 987654,
      irq: 123
    }
  },
  // 其他 CPU 核心
]
```

字段说明：

| 字段 | 含义 |
|---|---|
| `model` | CPU 型号 |
| `speed` | CPU 当前频率，单位 MHz |
| `times.user` | 用户态耗时 |
| `times.nice` | nice 优先级进程耗时，Windows 上通常为 0 |
| `times.sys` | 系统态耗时 |
| `times.idle` | 空闲耗时 |
| `times.irq` | 中断处理耗时 |

`times` 中的单位通常是毫秒，表示自系统启动以来 CPU 在各类任务上花费的时间。

---

## 7.2 获取 CPU 核心数

```js
const os = require('node:os');

console.log(os.cpus().length);
```

但不推荐简单用 `os.cpus().length` 作为并发数。

更推荐使用：

```js
const os = require('node:os');

console.log(os.availableParallelism());
```

---

## 7.3 os.availableParallelism()

返回当前 Node.js 进程可用的并行度。

```js
const os = require('node:os');

console.log(os.availableParallelism());
```

它会考虑：

1. CPU 核心数；
2. 容器限制；
3. cgroup 限制；
4. 当前进程可使用的处理器资源。

适合用来设置：

- Worker Threads 数量；
- Cluster 数量；
- 并发任务上限；
- 线程池大小。

示例：

```js
const os = require('node:os');

const workerCount = os.availableParallelism();

console.log(`建议开启 ${workerCount} 个 worker`);
```

如果没有可用信息，通常会返回一个保守值，例如 `1`。

---

## 7.4 os.loadavg()

返回系统平均负载。

```js
const os = require('node:os');

console.log(os.loadavg());
```

返回一个数组：

```js
[1, 5, 15]
```

含义分别是：

| 索引 | 含义 |
|---|---|
| `0` | 最近 1 分钟平均负载 |
| `1` | 最近 5 分钟平均负载 |
| `2` | 最近 15 分钟平均负载 |

示例：

```js
const os = require('node:os');

const [one, five, fifteen] = os.loadavg();

console.log(`1分钟平均负载: ${one}`);
console.log(`5分钟平均负载: ${five}`);
console.log(`15分钟平均负载: ${fifteen}`);
```

注意：

1. `os.loadavg()` 在 Linux / macOS 上比较有意义；
2. 在 Windows 上通常返回 `[0, 0, 0]`；
3. 平均负载不是 CPU 使用率，它更接近正在运行或等待运行的进程数。

---

# 8. 目录信息

---

## 8.1 os.homedir()

返回当前用户的主目录。

```js
const os = require('node:os');

console.log(os.homedir());
```

常见结果：

```text
Linux/macOS: /home/username
Windows: C:\Users\username
```

示例：

```js
const os = require('node:os');
const path = require('node:path');

const sshDir = path.join(os.homedir(), '.ssh');

console.log(sshDir);
```

---

## 8.2 os.tmpdir()

返回操作系统的临时文件目录。

```js
const os = require('node:os');

console.log(os.tmpdir());
```

常见结果：

```text
Linux: /tmp
macOS: /var/folders/...
Windows: C:\Users\username\AppData\Local\Temp
```

示例：

```js
const os = require('node:os');
const path = require('node:path');

const tempFile = path.join(os.tmpdir(), 'example.txt');

console.log(tempFile);
```

如果需要创建唯一的临时目录，推荐配合 `fs.mkdtemp()`：

```js
const os = require('node:os');
const path = require('node:path');
const fs = require('node:fs');

const tempDir = fs.mkdtempSync(path.join(os.tmpdir(), 'app-'));

console.log(tempDir);
```

注意：

1. `os.tmpdir()` 只返回目录，不保证文件名唯一；
2. 临时目录中的文件可能被系统清理；
3. 不要把重要数据长期保存在临时目录。

---

# 9. 网络信息

---

## 9.1 os.networkInterfaces()

返回当前机器的网络接口信息。

```js
const os = require('node:os');

console.log(os.networkInterfaces());
```

返回结构类似：

```js
{
  lo: [
    {
      address: '127.0.0.1',
      netmask: '255.0.0.0',
      family: 'IPv4',
      mac: '00:00:00:00:00:00',
      internal: true,
      cidr: '127.0.0.1/8'
    },
    {
      address: '::1',
      netmask: 'ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff',
      family: 'IPv6',
      mac: '00:00:00:00:00:00',
      internal: true,
      cidr: '::1/128',
      scopeid: 0
    }
  ],
  eth0: [
    {
      address: '192.168.1.100',
      netmask: '255.255.255.0',
      family: 'IPv4',
      mac: 'aa:bb:cc:dd:ee:ff',
      internal: false,
      cidr: '192.168.1.100/24'
    }
  ]
}
```

字段说明：

| 字段 | 含义 |
|---|---|
| `address` | IP 地址 |
| `netmask` | 子网掩码 |
| `family` | 地址族，例如 IPv4 / IPv6 |
| `mac` | MAC 地址 |
| `internal` | 是否是内部回环接口 |
| `cidr` | CIDR 表示 |
| `scopeid` | IPv6 link-local 地址的作用域 ID |

注意：不同 Node.js 版本中，`family` 可能是字符串：

```js
'IPv4'
'IPv6'
```

也可能是数字：

```js
4
6
```

所以判断时最好兼容：

```js
if (item.family === 'IPv4' || item.family === 4) {
  // IPv4 地址
}
```

---

## 9.2 获取本机 IPv4 地址

```js
const os = require('node:os');

function getLocalIPv4() {
  const interfaces = os.networkInterfaces();

  for (const addresses of Object.values(interfaces)) {
    for (const addr of addresses) {
      const isIPv4 = addr.family === 'IPv4' || addr.family === 4;

      if (isIPv4 && !addr.internal) {
        return addr.address;
      }
    }
  }

  return null;
}

console.log(getLocalIPv4());
```

---

## 9.3 获取所有非内部 IPv4 地址

```js
const os = require('node:os');

function getAllIPv4Addresses() {
  const result = [];
  const interfaces = os.networkInterfaces();

  for (const [name, addresses] of Object.entries(interfaces)) {
    for (const addr of addresses) {
      const isIPv4 = addr.family === 'IPv4' || addr.family === 4;

      if (isIPv4 && !addr.internal) {
        result.push({
          interface: name,
          address: addr.address,
          mac: addr.mac
        });
      }
    }
  }

  return result;
}

console.log(getAllIPv4Addresses());
```

---

# 10. 用户信息

---

## 10.1 os.userInfo()

返回当前用户的信息。

```js
const os = require('node:os');

console.log(os.userInfo());
```

POSIX 系统下可能返回：

```js
{
  username: 'user',
  uid: 1000,
  gid: 1000,
  shell: '/bin/zsh',
  homedir: '/home/user'
}
```

Windows 系统下可能返回：

```js
{
  username: 'username',
  uid: -1,
  gid: -1,
  shell: null,
  homedir: 'C:\\Users\\username'
}
```

字段说明：

| 字段 | 含义 |
|---|---|
| `username` | 用户名 |
| `uid` | 用户 ID，Windows 上通常为 `-1` |
| `gid` | 用户组 ID，Windows 上通常为 `-1` |
| `shell` | 默认 shell，Windows 上通常为 `null` |
| `homedir` | 用户主目录 |

---

## 10.2 获取用户名

```js
const os = require('node:os');

console.log(os.userInfo().username);
```

---

## 10.3 获取用户主目录

```js
const os = require('node:os');

console.log(os.userInfo().homedir);
```

也可以用：

```js
console.log(os.homedir());
```

二者通常结果一致，但来源和细节可能略有差异。

---

# 11. CPU 字节序

## os.endianness()

返回 CPU 的字节序。

```js
const os = require('node:os');

console.log(os.endianness());
```

返回值：

| 返回值 | 含义 |
|---|---|
| `'LE'` | 小端字节序 |
| `'BE'` | 大端字节序 |

大多数现代个人电脑是：

```text
LE
```

---

# 12. 换行符和空设备

---

## 12.1 os.EOL

当前操作系统的换行符。

```js
const os = require('node:os');

console.log(JSON.stringify(os.EOL));
```

结果：

| 平台 | 值 |
|---|---|
| Windows | `"\r\n"` |
| Linux / macOS | `"\n"` |

示例：

```js
const os = require('node:os');

const content = ['line1', 'line2', 'line3'].join(os.EOL);

console.log(content);
```

注意：不要假设所有文件都使用当前系统换行符。项目中可能存在 CRLF、LF 混用的情况。

---

## 12.2 os.devNull

当前平台空设备路径。

```js
const os = require('node:os');

console.log(os.devNull);
```

结果：

| 平台 | 值 |
|---|---|
| Windows | `'\\\\.\\nul'` |
| POSIX | `'/dev/null'` |

示例：

```js
const { spawn } = require('node:child_process');
const os = require('node:os');

const child = spawn('node', ['-v'], {
  stdio: ['ignore', 'pipe', 'pipe']
});
```

`os.devNull` 在需要跨平台丢弃输出时可能有用，但更常见的是直接使用 `ignore`。

---

# 13. 进程优先级

Node.js 提供了一组用于获取和设置进程优先级的方法。

---

## 13.1 os.getPriority()

获取进程调度优先级。

```js
const os = require('node:os');

console.log(os.getPriority());
```

默认获取当前 Node.js 进程的优先级。

也可以指定进程 ID：

```js
console.log(os.getPriority(process.pid));
```

---

## 13.2 os.setPriority()

设置进程优先级。

```js
const os = require('node:os');

os.setPriority(os.constants.priority.PRIORITY_LOW);
```

也可以指定 PID：

```js
os.setPriority(process.pid, os.constants.priority.PRIORITY_LOW);
```

优先级数值范围通常是：

```text
-20 到 19
```

其中：

| 数值 | 含义 |
|---|---|
| `-20` | 最高优先级 |
| `19` | 最低优先级 |

不过跨平台下建议使用常量，而不是裸数字。

---

## 13.3 优先级常量

```js
const os = require('node:os');

console.log(os.constants.priority);
```

常见常量：

```js
os.constants.priority.PRIORITY_LOW
os.constants.priority.PRIORITY_BELOW_NORMAL
os.constants.priority.PRIORITY_NORMAL
os.constants.priority.PRIORITY_ABOVE_NORMAL
os.constants.priority.PRIORITY_HIGH
os.constants.priority.PRIORITY_HIGHEST
```

示例：

```js
const os = require('node:os');

try {
  os.setPriority(process.pid, os.constants.priority.PRIORITY_LOW);
  console.log('当前进程优先级已降低');
} catch (err) {
  console.error('设置优先级失败:', err);
}
```

注意：

1. 设置高优先级可能需要管理员权限；
2. Windows 和 POSIX 的优先级模型不完全一样；
3. 普通业务代码很少需要手动修改进程优先级。

---

# 14. os.constants

`os.constants` 包含一些操作系统相关常量。

```js
const os = require('node:os');

console.log(os.constants);
```

主要包括：

| 分类 | 说明 |
|---|---|
| `os.constants.errno` | 系统错误码 |
| `os.constants.signals` | 信号常量 |
| `os.constants.priority` | 进程优先级常量 |
| `os.constants.uv` | libuv 相关常量 |

---

## 14.1 错误码常量

```js
const os = require('node:os');

console.log(os.constants.errno.EADDRINUSE);
console.log(os.constants.errno.ENOENT);
console.log(os.constants.errno.EACCES);
```

可以这样判断错误：

```js
const net = require('node:net');
const os = require('node:os');

const server = net.createServer();

server.on('error', err => {
  if (err.errno === os.constants.errno.EADDRINUSE) {
    console.error('端口被占用');
  } else {
    console.error(err);
  }
});
```

不过实际开发中更常见的是判断 `err.code`：

```js
if (err.code === 'EADDRINUSE') {
  console.error('端口被占用');
}
```

---

## 14.2 信号常量

```js
const os = require('node:os');

console.log(os.constants.signals.SIGINT);
console.log(os.constants.signals.SIGTERM);
console.log(os.constants.signals.SIGKILL);
```

示例：

```js
process.on('SIGINT', () => {
  console.log('收到 SIGINT 信号');
  process.exit(0);
});
```

---

# 15. 常见实战示例

---

## 15.1 打印当前系统信息

```js
const os = require('node:os');

function bytesToGB(bytes) {
  return `${(bytes / 1024 ** 3).toFixed(2)} GB`;
}

const info = {
  主机名: os.hostname(),
  平台: os.platform(),
  架构: os.arch(),
  系统类型: os.type(),
  发行版本: os.release(),
  版本: os.version(),
  CPU核心数: os.cpus().length,
  可用并行度: os.availableParallelism(),
  总内存: bytesToGB(os.totalmem()),
  可用内存: bytesToGB(os.freemem()),
  系统运行时间: `${Math.floor(os.uptime() / 3600)} 小时`,
  临时目录: os.tmpdir(),
  用户目录: os.homedir()
};

console.table(info);
```

---

## 15.2 获取本机 IP

```js
const os = require('node:os');

function getLocalIPv4() {
  const interfaces = os.networkInterfaces();

  for (const addresses of Object.values(interfaces)) {
    for (const addr of addresses) {
      const isIPv4 = addr.family === 'IPv4' || addr.family === 4;

      if (isIPv4 && !addr.internal) {
        return addr.address;
      }
    }
  }

  return '127.0.0.1';
}

console.log(getLocalIPv4());
```

---

## 15.3 创建临时文件目录

```js
const os = require('node:os');
const path = require('node:path');
const fs = require('node:fs');

const tempDir = fs.mkdtempSync(path.join(os.tmpdir(), 'my-app-'));

console.log(tempDir);
```

---

## 15.4 决定 Worker 数量

```js
const os = require('node:os');

const workerCount = os.availableParallelism();

console.log(`当前建议 worker 数量: ${workerCount}`);
```

如果使用 Worker Threads：

```js
const os = require('node:os');
const { Worker } = require('node:worker_threads');

const workerCount = os.availableParallelism();

for (let i = 0; i < workerCount; i++) {
  // new Worker(...)
}
```

---

## 15.5 简单内存监控

```js
const os = require('node:os');

function memoryUsage() {
  const total = os.totalmem();
  const free = os.freemem();
  const used = total - free;
  const percent = ((used / total) * 100).toFixed(2);

  return {
    totalGB: (total / 1024 ** 3).toFixed(2),
    usedGB: (used / 1024 ** 3).toFixed(2),
    freeGB: (free / 1024 ** 3).toFixed(2),
    usedPercent: `${percent}%`
  };
}

console.log(memoryUsage());
```

---

## 15.6 简单 CPU 使用率采样

`os.cpus()` 返回的是累计时间，不能直接得到瞬时 CPU 使用率。需要间隔采样后计算差值。

```js
const os = require('node:os');

function getCpuTimes() {
  return os.cpus().map(cpu => cpu.times);
}

function calcCpuUsage(prevTimes, nextTimes) {
  let idleDiff = 0;
  let totalDiff = 0;

  for (let i = 0; i < prevTimes.length; i++) {
    const prev = prevTimes[i];
    const next = nextTimes[i];

    const idle = next.idle - prev.idle;

    const total =
      (next.user - prev.user) +
      (next.nice - prev.nice) +
      (next.sys - prev.sys) +
      (next.idle - prev.idle) +
      (next.irq - prev.irq);

    idleDiff += idle;
    totalDiff += total;
  }

  return 1 - idleDiff / totalDiff;
}

const first = getCpuTimes();

setTimeout(() => {
  const second = getCpuTimes();
  const usage = calcCpuUsage(first, second);

  console.log(`CPU 使用率: ${(usage * 100).toFixed(2)}%`);
}, 1000);
```

---

# 16. 常见坑点

---

## 16.1 os.cpus().length 不一定等于可用 CPU 资源

在容器或受限环境中，宿主机可能有很多 CPU，但当前进程只能使用一部分。

不推荐：

```js
const count = os.cpus().length;
```

更推荐：

```js
const count = os.availableParallelism();
```

---

## 16.2 os.freemem() 不等于 Node.js 可用内存

```js
os.freemem();
```

表示系统空闲内存。

如果要看当前 Node.js 进程内存：

```js
process.memoryUsage();
```

例如：

```js
console.log(process.memoryUsage());
```

---

## 16.3 os.uptime() 不是 process.uptime()

```js
os.uptime();
```

是系统运行时间。

```js
process.uptime();
```

是当前 Node.js 进程运行时间。

---

## 16.4 os.tmpdir() 不保证文件唯一

错误理解：

```js
const file = path.join(os.tmpdir(), 'app.log');
```

这只是一个普通路径，如果多个进程同时使用可能冲突。

推荐创建唯一临时目录：

```js
const dir = fs.mkdtempSync(path.join(os.tmpdir(), 'app-'));
```

---

## 16.5 os.networkInterfaces() 包含回环地址

```js
127.0.0.1
::1
```

这些地址的 `internal` 为 `true`。

如果要获取对外地址，需要过滤：

```js
if (!addr.internal) {
  // 非回环地址
}
```

---

## 16.6 os.loadavg() 在 Windows 上意义有限

在 Windows 上：

```js
os.loadavg();
```

通常返回：

```js
[0, 0, 0]
```

所以做监控时需要做平台兼容。

---

## 16.7 os.arch() 不一定代表硬件架构

如果运行的是 x64 版本 Node.js，即使机器是 ARM64，也可能返回：

```text
x64
```

因为 `os.arch()` 更接近 Node.js 进程架构。

---

## 16.8 EOL 只表示当前系统默认换行符

```js
os.EOL
```

不代表所有文本文件都使用该换行符。

读取或生成跨平台文本文件时，需要明确换行策略。

---

# 17. os 模块与 process 对象对比

有些信息既可以从 `os` 获取，也可以从 `process` 获取。

| 信息 | os 模块 | process 对象 |
|---|---|---|
| 平台 | `os.platform()` | `process.platform` |
| 架构 | `os.arch()` | `process.arch` |
| PID | 无 | `process.pid` |
| 进程运行时间 | 无 | `process.uptime()` |
| 系统运行时间 | `os.uptime()` | 无 |
| 进程内存 | 无 | `process.memoryUsage()` |
| 系统内存 | `os.totalmem()` / `os.freemem()` | 无 |
| CPU | `os.cpus()` | 无 |
| 环境变量 | 无 | `process.env` |
| 当前工作目录 | 无 | `process.cwd()` |
| 用户主目录 | `os.homedir()` | 无 |
| 临时目录 | `os.tmpdir()` | 无 |

示例：

```js
const os = require('node:os');

console.log(os.platform());
console.log(process.platform);

console.log(os.arch());
console.log(process.arch);
```

---

# 18. 总结

`os` 模块主要用于获取操作系统信息，常用场景包括：

| 场景 | 推荐 API |
|---|---|
| 判断平台 | `os.platform()` |
| 判断架构 | `os.arch()` |
| 获取主机名 | `os.hostname()` |
| 获取用户目录 | `os.homedir()` |
| 获取临时目录 | `os.tmpdir()` |
| 获取内存 | `os.totalmem()` / `os.freemem()` |
| 获取 CPU 信息 | `os.cpus()` |
| 获取可用并发数 | `os.availableParallelism()` |
| 获取本机 IP | `os.networkInterfaces()` |
| 获取系统负载 | `os.loadavg()` |
| 获取系统运行时间 | `os.uptime()` |
| 获取当前用户 | `os.userInfo()` |
| 获取换行符 | `os.EOL` |
| 获取系统常量 | `os.constants` |

最常用的是：

```js
const os = require('node:os');

os.platform();
os.arch();
os.homedir();
os.tmpdir();
os.cpus();
os.availableParallelism();
os.networkInterfaces();
os.totalmem();
os.freemem();
```

如果需要写跨平台 Node.js 程序，`os` 模块通常要和 `path` 模块一起使用：

```js
const os = require('node:os');
const path = require('node:path');

const configPath = path.join(os.homedir(), '.config', 'app', 'config.json');
```