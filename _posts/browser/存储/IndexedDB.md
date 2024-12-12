### IndexedDB 是什么？

IndexedDB 是一种在浏览器中**存储大量结构化数据**的低级 API。它是一个完整的、事务性的数据库系统，允许网页和 Web 应用程序以异步方式存储和检索大量的**键值对数据**，包括文件和二进制对象。IndexedDB 并*不是基于 SQL 的关系型*数据库；相反，它是一种对象存储库，其中的对象（即 JavaScript 对象）可以被索引并高效地查询。

### 主要特点

1. **大容量存储**：
   - IndexedDB 提供了比 LocalStorage 和 SessionStorage 更大的存储空间，通常为数百 MB 到几 GB，具体*取决于用户的磁盘空间和浏览器实现*。

2. **异步操作**：
   - 所有与 IndexedDB 的交互都是异步进行的，这意味着它们不会阻塞主线程，从而保证了网页的响应性和性能。

3. **事务支持**：
   - 每次访问 IndexedDB 都是通过事务完成的。事务确保了一组操作要么全部成功，要么全部失败，维护了数据的一致性。

4. **索引功能**：
   - 可以为存储的对象创建多个索引，以便快速查找数据。这使得即使没有直接键的情况下也能高效地搜索记录。

5. **版本控制**：
   - 数据库结构（如对象存储和索引）可以通过版本号来管理，允许开发者安全地更新数据库模式而不影响现有数据。

6. **事件驱动模型**：
   - 与许多其他 Web API 类似，IndexedDB 使用事件驱动的方式处理请求的结果。例如，当一个读取操作完成时，会触发一个 `onsuccess` 事件。

7. **跨窗口通信**：
   - 同源下的不同页面或标签页可以通过共享同一个 IndexedDB 实例来进行简单的跨窗口通信。

8. **离线支持**：
   - IndexedDB 可用于缓存数据，使得应用程序可以在用户断开网络连接后仍然正常工作，这对于渐进式 Web 应用 (PWA) 尤其重要。

### 基本使用示例

下面是一个简单的例子，演示如何打开一个 IndexedDB 数据库，创建一个对象存储，并添加一条记录：

```javascript
// 请求打开数据库
let request = indexedDB.open("MyTestDatabase", 1);

request.onerror = function(event) {
  console.error("为什么不能打开数据库?");
};

request.onsuccess = function(event) {
  let db = event.target.result;
  console.log("数据库已打开");
  
  // 使用数据库...
};

// 如果需要升级数据库版本，此事件将被触发
request.onupgradeneeded = function(event) {
  let db = event.target.result;

  // 创建一个对象存储（类似表）
  let objectStore = db.createObjectStore("names", { keyPath: "id", autoIncrement: true });

  // 定义索引
  objectStore.createIndex("nameIndex", "name", { unique: false });

  console.log("数据库设置完毕");
};

// 添加数据到对象存储
function addData(db, data) {
  let transaction = db.transaction(["names"], "readwrite");
  let objectStore = transaction.objectStore("names");

  // 
  let request = objectStore.add(data);
  
  request.onsuccess = function(event) {
    console.log("数据已添加");
  };

  transaction.oncomplete = function() {
    console.log("事务已完成");
  };

  transaction.onerror = function(event) {
    console.error("事务未完成");
  };
}

// 示例：向数据库添加一条记录
let db; // 全局变量保存数据库引用
request.onsuccess = function(event) {
  db = event.target.result;
  addData(db, { name: "张三" });
};
```

### 注意事项

- **兼容性**：尽管大多数现代浏览器都支持 IndexedDB，但在使用前最好检查一下目标浏览器的支持情况。
- **安全性**：IndexedDB 数据受同源策略限制，只能由相同源的脚本访问。
- **用户体验**：由于 IndexedDB 不适合存储敏感信息（除非加密），因此在设计应用时要考虑这一点。

综上所述，IndexedDB 是一种强大且灵活的客户端存储解决方案，特别适合那些需要处理复杂数据结构或大量数据的应用程序。