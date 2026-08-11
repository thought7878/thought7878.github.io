`HTTP模块`允许***创建http服务器***，***处理http请求***

Node.js 的 `http` 模块是 Node.js 最核心的内置模块之一。它提供了***创建 HTTP 服务器、发送 HTTP 请求、处理请求头、响应体、保持连接、处理流式数据***等能力。

很多 Web 框架，例如 Express、Koa、Fastify，底层都依赖 Node.js 的 `http` 模块。

---

# 1. http 模块是什么

`http` 模块用于处理 HTTP/1.1 协议相关操作。

**它可以做：**
1. 创建 HTTP 服务器；
2. 监听客户端请求；
3. 解析请求方法、URL、请求头；
4. 读取请求体；
5. 返回响应状态码；
6. 设置响应头；
7. 返回响应体；
8. 发起 HTTP 客户端请求；
9. 管理连接池；
10. 处理 keep-alive；
11. 处理底层网络流。

引入方式：

CommonJS：

```js
const http = require('node:http');
```

ESM：

```js
import http from 'node:http';
```

---

# 2. http 模块的核心对象

`http` 模块中有几个核心对象：

| 对象 | 说明 |
|---|---|
| `http.Server` | HTTP 服务器对象 |
| `http.IncomingMessage` | 请求对象或客户端响应对象 |
| `http.ServerResponse` | 服务端响应对象 |
| `http.ClientRequest` | 客户端请求对象 |
| `http.Agent` | HTTP 客户端连接代理 |
| `http.globalAgent` | 全局默认 Agent |

简单理解：

```js
const server = http.createServer((req, res) => {
  // req 是 IncomingMessage
  // res 是 ServerResponse
});
```

---

# 3. 创建一个最简单的 HTTP 服务器

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  res.end('Hello Node.js HTTP');
});

server.listen(3000, () => {
  console.log('服务器启动: http://localhost:3000');
});
```

访问：

```text
http://localhost:3000
```

会看到：

```text
Hello Node.js HTTP
```

---

# 4. http.createServer

`http.createServer()` 用于创建 HTTP 服务器。

基本结构：

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  // 处理请求
});

server.listen(3000);
```

它也可以接收配置对象：

```js
const server = http.createServer({
  // 一些高级配置
}, (req, res) => {
  res.end('ok');
});
```

不过大多数场景只需要传入请求处理函数。

---

# 5. requestListener 请求处理函数

```js
http.createServer((req, res) => {

});
```

这个函数会在每次收到 HTTP 请求时执行。

参数：

| 参数 | 类型 | 说明 |
|---|---|---|
| `req` | `http.IncomingMessage` | 客户端请求对象 |
| `res` | `http.ServerResponse` | 服务端响应对象 |

---

# 6. req 请求对象

`req` 是 `http.IncomingMessage` 的实例。

它本身是一个可读流，可以用来读取请求体。

---

## 6.1 req.method

请求方法。

```js
console.log(req.method);
```

常见值：

```text
GET
POST
PUT
DELETE
PATCH
HEAD
OPTIONS
```

---

## 6.2 req.url

请求路径和查询字符串。

```js
console.log(req.url);
```

例如访问：

```text
http://localhost:3000/user?id=1
```

得到：

```text
/user?id=1
```

注意：`req.url` 不包含协议和主机。

---

## 6.3 解析 URL 和查询参数

推荐使用内置 `URL` 对象：

```js
const url = new URL(req.url, `http://${req.headers.host}`);

console.log(url.pathname);
console.log(url.searchParams.get('id'));
```

示例：

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);

  res.setHeader('Content-Type', 'application/json; charset=utf-8');

  res.end(JSON.stringify({
    pathname: url.pathname,
    id: url.searchParams.get('id')
  }));
});

server.listen(3000);
```

---

## 6.4 req.headers

请求头对象。

```js
console.log(req.headers);
```

常见请求头：

```js
req.headers.host
req.headers['user-agent']
req.headers['content-type']
req.headers['content-length']
req.headers.authorization
req.headers.cookie
```

注意：请求头属性名会被 Node.js 转换为小写。

例如：

```text
Content-Type
```

会变成：

```js
req.headers['content-type']
```

---

## 6.5 req.httpVersion

HTTP 协议版本。

```js
console.log(req.httpVersion);
```

常见值：

```text
1.0
1.1
```

---

## 6.6 req.socket

底层 TCP 连接对象。

```js
console.log(req.socket.remoteAddress);
console.log(req.socket.remotePort);
```

---

# 7. 读取请求体

Node.js 的 `http` 模块不会自动解析请求体。

需要手动监听 `data` 和 `end` 事件。

---

## 7.1 普通读取

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  let body = '';

  req.on('data', chunk => {
    body += chunk;
  });

  req.on('end', () => {
    console.log('请求体:', body);
    res.end('ok');
  });
});

server.listen(3000);
```

---

## 7.2 读取 JSON 请求体

```js
const http = require('node:http');

function readJsonBody(req) {
  return new Promise((resolve, reject) => {
    let body = '';

    req.on('data', chunk => {
      body += chunk;
    });

    req.on('end', () => {
      if (!body) {
        resolve(null);
        return;
      }

      try {
        resolve(JSON.parse(body));
      } catch (err) {
        reject(new Error('JSON 解析失败'));
      }
    });

    req.on('error', err => {
      reject(err);
    });
  });
}

const server = http.createServer(async (req, res) => {
  if (req.method === 'POST') {
    try {
      const data = await readJsonBody(req);

      res.statusCode = 200;
      res.setHeader('Content-Type', 'application/json; charset=utf-8');
      res.end(JSON.stringify({
        message: '接收成功',
        data
      }));
    } catch (err) {
      res.statusCode = 400;
      res.setHeader('Content-Type', 'application/json; charset=utf-8');
      res.end(JSON.stringify({
        message: err.message
      }));
    }
    return;
  }

  res.statusCode = 405;
  res.end('Method Not Allowed');
});

server.listen(3000);
```

---

## 7.3 使用 Buffer 收集请求体

推荐用数组保存 `Buffer`，最后合并，性能通常比字符串拼接更好。

```js
function readBody(req) {
  return new Promise((resolve, reject) => {
    const chunks = [];

    req.on('data', chunk => {
      chunks.push(chunk);
    });

    req.on('end', () => {
      resolve(Buffer.concat(chunks));
    });

    req.on('error', reject);
  });
}
```

使用：

```js
const buffer = await readBody(req);
const text = buffer.toString('utf8');
```

---

## 7.4 限制请求体大小

服务端一定要限制请求体大小，否则可能被大请求体攻击。

```js
function readBodyWithLimit(req, limit = 1024 * 1024) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    let size = 0;

    req.on('data', chunk => {
      size += chunk.length;

      if (size > limit) {
        req.destroy();
        reject(new Error('请求体过大'));
        return;
      }

      chunks.push(chunk);
    });

    req.on('end', () => {
      resolve(Buffer.concat(chunks));
    });

    req.on('error', reject);
  });
}
```

---

# 8. res 响应对象

`res` 是 `http.ServerResponse` 的实例。

它本身是一个可写流，用于向客户端返回响应体。

---

## 8.1 res.statusCode

设置 HTTP 状态码。

```js
res.statusCode = 200;
```

常见状态码：

| 状态码 | 含义 |
|---|---|
| `200` | 成功 |
| `201` | 创建成功 |
| `204` | 成功但无内容 |
| `301` | 永久重定向 |
| `302` | 临时重定向 |
| `304` | 未修改 |
| `400` | 请求错误 |
| `401` | 未认证 |
| `403` | 无权限 |
| `404` | 未找到 |
| `405` | 方法不允许 |
| `500` | 服务器内部错误 |
| `502` | 网关错误 |
| `503` | 服务不可用 |

---

## 8.2 res.setHeader

设置响应头。

```js
res.setHeader('Content-Type', 'application/json; charset=utf-8');
```

多个值：

```js
res.setHeader('Set-Cookie', ['a=1', 'b=2']);
```

获取响应头：

```js
console.log(res.getHeader('Content-Type'));
```

删除响应头：

```js
res.removeHeader('X-Powered-By');
```

---

## 8.3 res.writeHead

一次性写入状态码和响应头。

```js
res.writeHead(200, {
  'Content-Type': 'text/plain; charset=utf-8'
});
```

也可以带状态消息：

```js
res.writeHead(404, 'Not Found', {
  'Content-Type': 'text/plain; charset=utf-8'
});
```

注意：

```js
res.writeHead()
```

调用后就不能再使用：

```js
res.setHeader()
```

修改头了。

---

## 8.4 res.write

写入响应体片段。

```js
res.write('Hello ');
res.write('Node.js');
```

最后必须调用：

```js
res.end();
```

---

## 8.5 res.end

结束响应。

```js
res.end('Hello Node.js');
```

常见写法：

```js
res.statusCode = 200;
res.setHeader('Content-Type', 'text/plain; charset=utf-8');
res.end('OK');
```

如果忘记调用 `res.end()`，客户端可能会一直等待，直到超时。

---

# 9. 返回 JSON 响应

封装一个 JSON 响应方法：

```js
function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}
```

使用：

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  sendJson(res, 200, {
    message: 'success',
    data: {
      id: 1,
      name: 'Node.js'
    }
  });
});

server.listen(3000);
```

---

# 10. **简单路由**

Node.js 原生 `http` 模块**没有内置路由系统，需要自己根据 `req.url` 和 `req.method` 判断**。

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);

  res.setHeader('Content-Type', 'application/json; charset=utf-8');

  if (req.method === 'GET' && url.pathname === '/') {
    res.end(JSON.stringify({
      message: '首页'
    }));
    return;
  }

  if (req.method === 'GET' && url.pathname === '/users') {
    res.end(JSON.stringify({
      users: [
        { id: 1, name: 'Alice' },
        { id: 2, name: 'Bob' }
      ]
    }));
    return;
  }

  if (req.method === 'GET' && url.pathname === '/health') {
    res.end(JSON.stringify({
      status: 'ok'
    }));
    return;
  }

  res.statusCode = 404;
  res.end(JSON.stringify({
    message: 'Not Found'
  }));
});

server.listen(3000);
```

---

# 11. **简单静态文件服务器**

```js
const http = require('node:http');
const fs = require('node:fs');
const path = require('node:path');

const root = path.join(__dirname, 'public');

const mimeTypes = {
  '.html': 'text/html; charset=utf-8',
  '.js': 'text/javascript; charset=utf-8',
  '.css': 'text/css; charset=utf-8',
  '.json': 'application/json; charset=utf-8',
  '.png': 'image/png',
  '.jpg': 'image/jpeg',
  '.jpeg': 'image/jpeg',
  '.gif': 'image/gif',
  '.svg': 'image/svg+xml',
  '.txt': 'text/plain; charset=utf-8'
};

const server = http.createServer((req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);

  let pathname = url.pathname;

  if (pathname === '/') {
    pathname = '/index.html';
  }

  const filePath = path.join(root, pathname);

  // 防止路径穿越
  if (!filePath.startsWith(root + path.sep)) {
    res.statusCode = 403;
    res.end('Forbidden');
    return;
  }

  fs.stat(filePath, (err, stats) => {
    if (err || !stats.isFile()) {
      res.statusCode = 404;
      res.end('Not Found');
      return;
    }

    const ext = path.extname(filePath).toLowerCase();
    const contentType = mimeTypes[ext] || 'application/octet-stream';

    res.statusCode = 200;
    res.setHeader('Content-Type', contentType);
    res.setHeader('Content-Length', stats.size);

    const stream = fs.createReadStream(filePath);
    stream.pipe(res);

    stream.on('error', () => {
      res.statusCode = 500;
      res.end('Internal Server Error');
    });
  });
});

server.listen(3000);
```

---

# 12. server.listen

启动服务器监听端口。

```js
server.listen(3000);
```

完整写法：

```js
server.listen(3000, '127.0.0.1', () => {
  console.log('服务器运行在 http://127.0.0.1:3000');
});
```

参数：

| 参数 | 说明 |
|---|---|
| `port` | 端口号 |
| `host` | 监听地址 |
| `backlog` | 等待连接队列长度 |
| `callback` | 启动成功回调 |

---

## 12.1 server.address()

获取服务器监听地址。

```js
const address = server.address();

console.log(address.address);
console.log(address.port);
```

例如：

```js
{
  address: '127.0.0.1',
  family: 'IPv4',
  port: 3000
}
```

---

## 12.2 server.close()

停止服务器。

```js
server.close(() => {
  console.log('服务器已关闭');
});
```

注意：`close()` 不会立即断开已有 keep-alive 连接，而是等待已有连接自然结束。

较新版本 Node.js 还提供：

```js
server.closeAllConnections();
server.closeIdleConnections();
```

用于强制关闭连接或空闲连接。

---

# 13. server 常见事件

---

## 13.1 request

每次收到请求都会触发。

```js
server.on('request', (req, res) => {
  res.end('ok');
});
```

其实：

```js
http.createServer((req, res) => {});
```

本质就是监听 `request` 事件。

---

## 13.2 connection

建立 TCP 连接时触发。

```js
server.on('connection', socket => {
  console.log('新连接:', socket.remoteAddress);
});
```

---

## 13.3 close

服务器关闭时触发。

```js
server.on('close', () => {
  console.log('服务器关闭');
});
```

---

## 13.4 clientError

客户端发送了非法 HTTP 请求时触发。

```js
server.on('clientError', (err, socket) => {
  socket.end('HTTP/1.1 400 Bad Request\r\n\r\n');
});
```

---

# 14. HTTP 客户端请求

`http` 模块不仅可以创建服务器，也可以作为客户端发送请求。

---

## 14.1 http.request

```js
const http = require('node:http');

const req = http.request({
  hostname: 'example.com',
  port: 80,
  path: '/',
  method: 'GET'
}, res => {
  console.log('状态码:', res.statusCode);
  console.log('响应头:', res.headers);

  let body = '';

  res.on('data', chunk => {
    body += chunk;
  });

  res.on('end', () => {
    console.log('响应体:', body);
  });
});

req.on('error', err => {
  console.error('请求失败:', err);
});

req.end();
```

注意：使用 `http.request()` 时，必须手动调用：

```js
req.end();
```

否则请求不会真正发送完成。

---

## 14.2 http.get

`http.get()` 是 `http.request()` 的快捷方式，专门用于 GET 请求。

它会自动调用 `req.end()`。

```js
const http = require('node:http');

http.get('http://example.com', res => {
  let body = '';

  res.on('data', chunk => {
    body += chunk;
  });

  res.on('end', () => {
    console.log(body);
  });
}).on('error', err => {
  console.error(err);
});
```

---

## 14.3 发送 POST JSON 请求

```js
const http = require('node:http');

const data = JSON.stringify({
  name: 'Node.js',
  age: 15
});

const req = http.request({
  hostname: 'localhost',
  port: 3000,
  path: '/users',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json; charset=utf-8',
    'Content-Length': Buffer.byteLength(data)
  }
}, res => {
  let body = '';

  res.on('data', chunk => {
    body += chunk;
  });

  res.on('end', () => {
    console.log('响应:', body);
  });
});

req.on('error', err => {
  console.error(err);
});

req.write(data);
req.end();
```

---

## 14.4 使用 URL 对象发送请求

```js
const http = require('node:http');

const req = http.request(new URL('http://example.com/foo?bar=1'), res => {
  let body = '';

  res.on('data', chunk => {
    body += chunk;
  });

  res.on('end', () => {
    console.log(body);
  });
});

req.end();
```

---

# 15. 封装 Promise 版 HTTP 请求

```js
const http = require('node:http');

function httpRequest(url, options = {}) {
  return new Promise((resolve, reject) => {
    const req = http.request(url, options, res => {
      const chunks = [];

      res.on('data', chunk => {
        chunks.push(chunk);
      });

      res.on('end', () => {
        const body = Buffer.concat(chunks);

        resolve({
          statusCode: res.statusCode,
          headers: res.headers,
          body,
          text: () => body.toString('utf8'),
          json: () => JSON.parse(body.toString('utf8'))
        });
      });

      res.on('error', reject);
    });

    req.on('error', reject);

    if (options.body) {
      req.write(options.body);
    }

    req.end();
  });
}
```

使用：

```js
async function main() {
  const res = await httpRequest('http://localhost:3000/users');

  console.log(res.statusCode);
  console.log(res.text());
}

main();
```

POST：

```js
const res = await httpRequest('http://localhost:3000/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ name: 'Node.js' })
});

console.log(res.json());
```

---

# 16. http.Agent

`http.Agent` 用于管理 HTTP 客户端连接。

它负责：

1. 连接复用；
2. keep-alive；
3. 连接池；
4. socket 管理；
5. 最大连接数控制。

示例：

```js
const http = require('node:http');

const agent = new http.Agent({
  keepAlive: true,
  maxSockets: 10,
  maxFreeSockets: 5
});

http.get(
  {
    hostname: 'example.com',
    agent
  },
  res => {
    let body = '';

    res.on('data', chunk => {
      body += chunk;
    });

    res.on('end', () => {
      console.log(body);
    });
  }
);
```

---

## 16.1 常用 Agent 配置

| 配置 | 说明 |
|---|---|
| `keepAlive` | 是否保持连接 |
| `keepAliveMsecs` | keep-alive 初始延迟 |
| `maxSockets` | 每个 host 最大并发 socket 数 |
| `maxFreeSockets` | 保持空闲的最大 socket 数 |
| `timeout` | socket 超时时间 |

---

## 16.2 http.globalAgent

如果不指定 `agent`，Node.js 会使用全局默认 Agent：

```js
http.globalAgent
```

较新的 Node.js 版本中，默认 Agent 已经启用了 keep-alive。

如果需要自定义连接池，可以：

```js
const agent = new http.Agent({
  keepAlive: true,
  maxSockets: 50
});
```

然后传给请求：

```js
http.get({
  hostname: 'example.com',
  agent
}, res => {});
```

---

# 17. keep-alive

HTTP/1.1 默认支持持久连接，但具体行为取决于客户端、服务端和代理。

Node.js 服务端默认会保持连接，直到超时或关闭。

相关服务端超时配置：

```js
const server = http.createServer((req, res) => {
  res.end('ok');
});

server.keepAliveTimeout = 65000;
server.headersTimeout = 66000;
```

---

# 18. 超时处理

HTTP 服务中超时非常重要，可以避免连接长时间占用。

---

## 18.1 server.setTimeout

设置服务器默认超时。

```js
server.setTimeout(30000);
```

---

## 18.2 server.keepAliveTimeout

保持连接的空闲超时。

```js
server.keepAliveTimeout = 65000;
```

---

## 18.3 server.headersTimeout

等待客户端发送完整请求头的超时。

```js
server.headersTimeout = 60000;
```

---

## 18.4 请求级超时

客户端请求可以设置超时：

```js
const req = http.request({
  hostname: 'example.com',
  timeout: 5000
}, res => {});

req.on('timeout', () => {
  console.log('请求超时');
  req.destroy();
});

req.end();
```

---

# 19. http.STATUS_CODES

Node.js 提供了 HTTP 状态码映射表。

```js
const http = require('node:http');

console.log(http.STATUS_CODES[200]);
// OK

console.log(http.STATUS_CODES[404]);
// Not Found

console.log(http.STATUS_CODES[500]);
// Internal Server Error
```

---

# 20. http.METHODS

返回 Node.js HTTP 解析器支持的 HTTP 方法列表。

```js
const http = require('node:http');

console.log(http.METHODS);
```

可能包含：

```text
GET
POST
PUT
DELETE
PATCH
HEAD
OPTIONS
```

---

# 21. 处理 Cookie

Node.js 原生 `http` 模块不会自动解析 Cookie。

读取 Cookie：

```js
function parseCookies(cookieHeader = '') {
  const cookies = {};

  cookieHeader
    .split(';')
    .forEach(item => {
      const [key, ...rest] = item.trim().split('=');

      if (key) {
        cookies[key] = decodeURIComponent(rest.join('='));
      }
    });

  return cookies;
}
```

使用：

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  const cookies = parseCookies(req.headers.cookie);

  res.setHeader('Set-Cookie', ['token=abc123; HttpOnly']);
  res.end(JSON.stringify(cookies));
});

server.listen(3000);
```

---

# 22. 重定向

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  if (req.url === '/') {
    res.statusCode = 302;
    res.setHeader('Location', '/home');
    res.end();
    return;
  }

  if (req.url === '/home') {
    res.end('Home Page');
    return;
  }

  res.statusCode = 404;
  res.end('Not Found');
});

server.listen(3000);
```

---

# 23. 流式响应

`res` 是可写流，适合返回大文件、Server-Sent Events、逐块生成数据等场景。

---

## 23.1 返回大文件

```js
const http = require('node:http');
const fs = require('node:fs');

const server = http.createServer((req, res) => {
  const stream = fs.createReadStream('./big-file.txt');

  res.setHeader('Content-Type', 'text/plain; charset=utf-8');

  stream.pipe(res);
});

server.listen(3000);
```

---

## 23.2 Server-Sent Events

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive'
  });

  let count = 0;

  const timer = setInterval(() => {
    count += 1;
    res.write(`data: 第 ${count} 条消息\n\n`);

    if (count === 10) {
      clearInterval(timer);
      res.end();
    }
  }, 1000);

  req.on('close', () => {
    clearInterval(timer);
  });
});

server.listen(3000);
```

---

# 24. 错误处理

HTTP 服务中一定要处理错误，否则可能导致连接挂起或服务崩溃。

---

## 24.1 请求体错误

```js
req.on('error', err => {
  console.error(err);
});
```

---

## 24.2 响应流错误

```js
res.on('error', err => {
  console.error(err);
});
```

---

## 24.3 异步错误捕获

```js
const server = http.createServer(async (req, res) => {
  try {
    // 业务逻辑
    res.end('ok');
  } catch (err) {
    console.error(err);

    if (!res.headersSent) {
      res.statusCode = 500;
      res.end('Internal Server Error');
    } else {
      res.end();
    }
  }
});
```

注意：如果响应头已经发送，就不能再完整返回 500 状态码了，只能尝试关闭响应。

---

# 25. 常见请求头

| 请求头 | 说明 |
|---|---|
| `Host` | 主机名 |
| `User-Agent` | 客户端标识 |
| `Accept` | 可接受内容类型 |
| `Accept-Encoding` | 可接受编码 |
| `Content-Type` | 请求体类型 |
| `Content-Length` | 请求体长度 |
| `Authorization` | 认证信息 |
| `Cookie` | Cookie |
| `Origin` | 请求来源 |
| `Referer` | 来源页面 |
| `Connection` | 连接控制 |

---

# 26. 常见响应头

| 响应头 | 说明 |
|---|---|
| `Content-Type` | 响应内容类型 |
| `Content-Length` | 响应体长度 |
| `Set-Cookie` | 设置 Cookie |
| `Location` | 重定向地址 |
| `Cache-Control` | 缓存控制 |
| `ETag` | 资源标识 |
| `Last-Modified` | 最后修改时间 |
| `Access-Control-Allow-Origin` | CORS 允许来源 |
| `Access-Control-Allow-Headers` | CORS 允许请求头 |
| `Access-Control-Allow-Methods` | CORS 允许方法 |

---

# 27. CORS 简单示例

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');

  if (req.method === 'OPTIONS') {
    res.statusCode = 204;
    res.end();
    return;
  }

  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify({ message: 'ok' }));
});

server.listen(3000);
```

生产环境中不建议随便使用：

```js
Access-Control-Allow-Origin: *
```

如果有 Cookie 或认证信息，应明确指定来源。

---

# 28. http 模块与 fetch

Node.js 新版本内置了全局 `fetch`：

```js
const res = await fetch('https://example.com');
const text = await res.text();

console.log(text);
```

如果只是发送普通 HTTP 请求，`fetch` 更简洁。

但是 `http` 模块仍然非常重要，因为它适合：

1. 创建 HTTP 服务器；
2. 实现 Web 框架；
3. 精细控制请求头；
4. 精细控制连接；
5. 流式处理；
6. 代理服务器；
7. 自定义 Agent；
8. 底层网络调试。

---

# 29. http 与 https 的区别

| 模块 | 说明 |
|---|---|
| `node:http` | 明文 HTTP |
| `node:https` | 基于 TLS/SSL 的 HTTPS |

HTTPS 服务器示例：

```js
const https = require('node:https');
const fs = require('node:fs');

const server = https.createServer({
  key: fs.readFileSync('./key.pem'),
  cert: fs.readFileSync('./cert.pem')
}, (req, res) => {
  res.end('Secure Hello');
});

server.listen(3443);
```

---

# 30. http 与 http2 的区别

| 模块 | 说明 |
|---|---|
| `node:http` | HTTP/1.1 |
| `node:http2` | HTTP/2 |

HTTP/2 支持：

1. 多路复用；
2. 头部压缩；
3. 服务端推送；
4. 更高并发效率。

但很多应用层协议、代理和浏览器兼容场景下，HTTP/1.1 仍然是基础。

---

# 31. 常见坑点

---

## 31.1 忘记 res.end

错误：

```js
res.write('Hello');
```

没有：

```js
res.end();
```

结果客户端会一直等待。

正确：

```js
res.end('Hello');
```

---

## 31.2 请求体不会自动解析

Node.js 原生不会自动解析 JSON、表单、文件上传。

需要自己监听：

```js
req.on('data')
req.on('end')
```

或者使用框架：

- Express；
- Koa；
- Fastify。

---

## 31.3 headersSent 后不能修改响应头

错误：

```js
res.end('ok');
res.setHeader('X-Test', '1');
```

正确：

```js
res.setHeader('X-Test', '1');
res.end('ok');
```

如果响应头已发送，再设置头会报错。

---

## 31.4 Content-Type 没指定字符集

返回文本或 JSON 时建议：

```js
res.setHeader('Content-Type', 'application/json; charset=utf-8');
```

否则中文可能出现乱码。

---

## 31.5 路径穿越风险

静态文件服务一定要限制路径。

不推荐：

```js
const filePath = path.join(root, req.url);
```

推荐：

```js
const filePath = path.resolve(root, '.' + url.pathname);

if (!filePath.startsWith(root + path.sep)) {
  res.statusCode = 403;
  res.end('Forbidden');
  return;
}
```

---

## 31.6 大请求体没有大小限制

如果不限制请求体大小，可能被大请求耗尽内存。

应加入限制：

```js
if (size > limit) {
  req.destroy();
}
```

---

## 31.7 没有处理请求错误

```js
req.on('error', err => {
  console.error(err);
});
```

---

## 31.8 没有处理异步错误

异步处理函数里必须捕获错误：

```js
try {
  await handler();
} catch {
  res.statusCode = 500;
  res.end('Internal Server Error');
}
```

---

# 32. 一个较完整的原生 HTTP JSON API 示例

```js
const http = require('node:http');

function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}

function readJsonBody(req, limit = 1024 * 1024) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    let size = 0;

    req.on('data', chunk => {
      size += chunk.length;

      if (size > limit) {
        req.destroy();
        reject(new Error('请求体过大'));
        return;
      }

      chunks.push(chunk);
    });

    req.on('end', () => {
      const raw = Buffer.concat(chunks).toString('utf8');

      if (!raw) {
        resolve(null);
        return;
      }

      try {
        resolve(JSON.parse(raw));
      } catch {
        reject(new Error('JSON 格式错误'));
      }
    });

    req.on('error', reject);
  });
}

const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];

const server = http.createServer(async (req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);

  try {
    if (req.method === 'GET' && url.pathname === '/users') {
      sendJson(res, 200, {
        data: users
      });
      return;
    }

    if (req.method === 'POST' && url.pathname === '/users') {
      const body = await readJsonBody(req);

      if (!body || !body.name) {
        sendJson(res, 400, {
          message: 'name 字段必填'
        });
        return;
      }

      const user = {
        id: users.length + 1,
        name: body.name
      };

      users.push(user);

      sendJson(res, 201, {
        data: user
      });
      return;
    }

    if (req.method === 'GET' && url.pathname === '/health') {
      sendJson(res, 200, {
        status: 'ok'
      });
      return;
    }

    sendJson(res, 404, {
      message: 'Not Found'
    });
  } catch (err) {
    sendJson(res, err.message === 'JSON 格式错误' ? 400 : 500, {
      message: err.message
    });
  }
});

server.listen(3000, () => {
  console.log('API 服务运行在 http://localhost:3000');
});
```

---

# 33. http 模块常用 API 速查表

| API | 作用 |
|---|---|
| `http.createServer()` | 创建 HTTP 服务器 |
| `server.listen()` | 启动监听 |
| `server.close()` | 关闭服务器 |
| `server.address()` | 获取监听地址 |
| `req.method` | 请求方法 |
| `req.url` | 请求路径 |
| `req.headers` | 请求头 |
| `req.on('data')` | 读取请求体数据 |
| `req.on('end')` | 请求体读取完成 |
| `res.statusCode` | 设置状态码 |
| `res.setHeader()` | 设置响应头 |
| `res.getHeader()` | 获取响应头 |
| `res.writeHead()` | 写入状态码和响应头 |
| `res.write()` | 写入响应体 |
| `res.end()` | 结束响应 |
| `http.request()` | 发起 HTTP 请求 |
| `http.get()` | 发起 GET 请求 |
| `http.Agent` | 管理客户端连接 |
| `http.STATUS_CODES` | 状态码映射 |
| `http.METHODS` | 支持的 HTTP 方法 |

---

# 34. 总结

Node.js 的 `http` 模块是构建 Web 服务的基础模块。

它提供了：

1. HTTP 服务器；
2. HTTP 客户端请求；
3. 请求头和响应头处理；
4. 请求体和响应体流处理；
5. Agent 连接池；
6. 超时控制；
7. 状态码和方法常量。

最核心记忆：

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  res.end('Hello World');
});

server.listen(3000);
```

如果要做实际业务系统，通常不会完全裸写 `http` 模块，而是使用 Express、Koa、Fastify 等框架。但理解 `http` 模块有助于深入理解 Node.js Web 服务的底层机制。