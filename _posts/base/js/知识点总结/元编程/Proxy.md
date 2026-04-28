[[_posts/base/js/知识点总结/ES6+ 新特性/Proxy|Proxy]]

`Proxy（代理）` 是 ES6 引入的**元编程（Metaprogramming）核心 API**。
它允许你**为对象创建一个“虚拟包装层”，拦截并自定义对象的基础操作**（*属性读写、函数调用、枚举、原型操作*等）。
*它是现代响应式框架（Vue 3）、沙箱环境、数据校验、AOP 切面编程的**底层基石***。

---

## 一、核心定义与基本语法

```js
const proxy = new Proxy(target, handler);
```
- `target`：*被代理的原始对象*（可以是*对象、数组、函数*等）
- `handler`：*配置对象，包含一个或多个 `拦截器（Traps）`，定义拦截行为*
- `proxy`：返回的*代理对象*。**原始对象不会被修改**，*所有操作应通过 `proxy` 进行*

```js
const user = { name: 'Alice', age: 20 };
const proxy = new Proxy(user, {
  get(target, prop, receiver) {
    console.log(`读取: ${prop}`);
    return Reflect.get(target, prop, receiver);
  },
  set(target, prop, value, receiver) {
    if (prop === 'age' && value < 0) throw new RangeError('年龄无效');
    console.log(`写入: ${prop} = ${value}`);
    return Reflect.set(target, prop, value, receiver);
  }
});

proxy.age = 25;  // 写入: age = 25
console.log(proxy.name); // 读取: name → 'Alice'
```

---

## 二、13 种拦截器（Traps）全景

*Proxy 可拦截 JS 引擎对对象的底层操作，共 13 种：*

| 分类 | Trap 方法 | 触发场景 | 返回值要求 |
|------|-----------|----------|------------|
| **属性读写** | `get(target, prop, receiver)` | `proxy.prop` / `proxy[prop]` | 任意值 |
|  | `set(target, prop, value, receiver)` | `proxy.prop = val` | `boolean`（严格模式失败抛错） |
|  | `has(target, prop)` | `prop in proxy` | `boolean` |
|  | `deleteProperty(target, prop)` | `delete proxy.prop` | `boolean` |
| **对象结构** | `ownKeys(target)` | `Object.keys()` / `for...in` / `Reflect.ownKeys()` | 可迭代对象（键数组） |
|  | `getOwnPropertyDescriptor(target, prop)` | `Object.getOwnPropertyDescriptor()` | 描述符对象或 `undefined` |
|  | `defineProperty(target, prop, desc)` | `Object.defineProperty()` | `boolean` |
| **扩展性/原型** | `preventExtensions(target)` | `Object.preventExtensions()` | `boolean` |
|  | `isExtensible(target)` | `Object.isExtensible()` | `boolean` |
|  | `getPrototypeOf(target)` | `Object.getPrototypeOf()` / `__proto__` | 对象或 `null` |
|  | `setPrototypeOf(target, proto)` | `Object.setPrototypeOf()` | `boolean` |
| **函数/构造** | `apply(target, thisArg, args)` | `proxy(...args)` / `proxy.call()` / `proxy.apply()` | 任意值 |
|  | `construct(target, args, newTarget)` | `new proxy(...args)` | 对象（必须返回对象） |

> ⚠️ **规范约束**：若拦截器返回值不符合规范（如 `set` 返回非布尔值），严格模式下会抛出 `TypeError`。

---

## 三、Proxy 与 Reflect 的黄金搭档
参考：[[_posts/base/js/知识点总结/元编程/Reflect|Reflect]]

`Reflect` 是 ES6 同步推出的内置对象，*每个 Proxy Trap 都有对应的 `Reflect` 方法*。**强烈建议在 Trap 中使用 `Reflect` 执行默认行为：**

### 为什么必须用 `Reflect`？
1. **保证默认语义**：手动实现容易遗漏边界条件（如原型链查找、描述符校验）
2. **正确处理 `receiver`**：`receiver` 是触发操作的原始代理对象。使用 `Reflect.get/set(target, prop, value, receiver)` 能确保 getter/setter 内的 `this` 指向代理对象，避免无限递归或状态错乱
3. **返回值规范**：`Reflect` 方法返回值严格符合 Trap 要求

```js
// ❌ 错误示范：this 指向丢失，可能触发原型链死循环
get(target, prop) {
  return target[prop]; 
}

// ✅ 正确示范：透传 receiver，保持语义一致
get(target, prop, receiver) {
  return Reflect.get(target, prop, receiver);
}
```

---

## 四、核心应用场景与实战

### 1. 响应式系统（Vue 3 核心原理）
```js
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      track(target, key); // 依赖收集
      const res = Reflect.get(target, key, receiver);
      // 懒代理：访问到嵌套对象时才创建新 Proxy，避免启动卡顿
      return typeof res === 'object' && res !== null ? reactive(res) : res;
    },
    set(target, key, value, receiver) {
      const oldValue = target[key];
      const res = Reflect.set(target, key, value, receiver);
      if (oldValue !== value) trigger(target, key); // 触发更新
      return res;
    }
  });
}
```

### 2. 数据校验与类型守卫
```js
const schema = { age: 'number', name: 'string' };
function validatedProxy(obj, schema) {
  return new Proxy(obj, {
    set(target, prop, value, receiver) {
      const expected = schema[prop];
      if (expected && typeof value !== expected) {
        throw new TypeError(`${prop} 必须是 ${expected}`);
      }
      return Reflect.set(target, prop, value, receiver);
    }
  });
}
```

### 3. 私有属性模拟与访问控制
```js
const PRIVATE = Symbol('private');
function withPrivateAccess(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {
      if (typeof prop === 'string' && prop.startsWith('_')) {
        throw new Error('禁止访问私有属性');
      }
      return Reflect.get(target, prop, receiver);
    },
    ownKeys(target) {
      return Reflect.ownKeys(target).filter(k => 
        typeof k !== 'string' || !k.startsWith('_')
      );
    }
  });
}
```

### 4. 函数拦截与缓存（Memoization）
```js
function memoize(fn) {
  const cache = new Map();
  return new Proxy(fn, {
    apply(target, thisArg, args) {
      const key = JSON.stringify(args);
      if (cache.has(key)) return cache.get(key);
      const result = Reflect.apply(target, thisArg, args);
      cache.set(key, result);
      return result;
    }
  });
}
```

---

## 五、Proxy vs Object.defineProperty

| 维度 | `Proxy` | `Object.defineProperty` |
|------|---------|--------------------------|
| **拦截范围** | 13 种底层操作（读写、删除、枚举、函数调用等） | 仅 `get` / `set` |
| **数组支持** | 原生拦截 `push`/`splice`/索引赋值 | 需重写数组方法，无法拦截索引直接赋值 |
| **新增属性** | 自动拦截 | 需手动 `defineProperty`（Vue 2 `$set` 痛点） |
| **性能** | 按需代理 + 懒代理，启动快，运行时略有开销 | 递归遍历所有属性，初始化慢，内存占用高 |
| **返回值** | 返回新代理对象，原对象不变 | 直接修改原对象 |
| **兼容性** | ES6+（现代浏览器/Node.js） | ES5（兼容 IE9+） |
| **现代定位** | ✅ 响应式/元编程标准方案 | ⚠️ 仅用于兼容旧环境或简单拦截 |

> 📌 Vue 3 全面转向 `Proxy` 正是为了解决 Vue 2 的数组监听、新增属性、性能瓶颈三大痛点。

---

## 六、注意事项、限制与性能

### 1. `this` 指向陷阱（高频踩坑）
代理对象的方法被调用时，方法内部的 `this` 指向 **Proxy 而非 Target**。若方法依赖内部槽位（如 `Date.getTime()`、`Map.get()`），会抛出 `TypeError`。
```js
const date = new Date();
const proxy = new Proxy(date, {});
proxy.getDate(); // TypeError: this is not a Date object
// 解决：方法调用时 bind 回原对象，或特殊处理内置对象
```

### 2. 不可代理的对象
部分内置对象（`Date`、`Promise`、`RegExp`、部分 DOM 节点）内部使用 `[[IsHTMLDDA]]` 或严格槽位检查，直接代理会报错。需包装或避开。

### 3. 相等性断裂
`proxy !== target`，且 `Map`/`Set` 的键匹配、`WeakMap` 查找均基于引用。若将 `target` 存入集合，用 `proxy` 无法命中。

### 4. 性能开销
- 拦截器执行有额外调用栈，直接属性访问比 Proxy 快约 10%~30%
- V8 对 Proxy 优化持续进步，**业务开发无感**；极高频同步循环（如物理引擎/Canvas 渲染）建议绕过 Proxy
- 采用**懒代理**（访问嵌套对象时才创建新 Proxy）可大幅降低初始化成本

### 5. 可撤销代理（安全沙箱必备）
```js
const { proxy, revoke } = Proxy.revocable(target, handler);
proxy.foo = 1;
revoke(); // 撤销后所有操作抛 TypeError
proxy.foo; // TypeError: Cannot perform 'get' on a proxy that has been revoked
```
适用于：插件沙箱、临时权限控制、防内存泄漏。

---

## 七、最佳实践与工程建议

| 场景 | 建议 |
|------|------|
| **始终使用 `Reflect`** | 除非明确要覆盖默认行为，否则 Trap 内第一选择是 `Reflect.xxx` |
| **避免代理内置对象** | `Date`/`Map`/`Promise` 等改用包装类或组合模式 |
| **响应式用懒代理** | 嵌套对象在 `get` 时按需代理，避免全量递归卡顿 |
| **类型安全** | TS 中使用 `ProxyHandler<T>` 约束 handler，避免键名拼写错误 |
| **生命周期管理** | 沙箱/插件场景使用 `Proxy.revocable()`，用完及时 `revoke()` |
| **调试技巧** | `console.log(proxy)` 可能显示为空，使用 `console.dir(proxy)` 或访问具体属性 |

---

## 八、总结

| 维度       | 核心结论                                                      |
| -------- | --------------------------------------------------------- |
| **本质**   | 对象操作的“拦截器+转发器”，实现元编程与行为定制                                 |
| **核心机制** | `new Proxy(target, handler)` + 13 种 Trap + `Reflect` 默认行为 |
| **优势**   | 全量拦截、支持数组/新增属性、懒代理、不污染原对象                                 |
| **痛点**   | `this` 指向变化、内置对象限制、相等性断裂、轻微性能开销                           |
| **现代定位** | 响应式框架、沙箱、AOP、数据校验、Mock 测试的标准底座                            |
| **工程守则** | 配合 `Reflect`、懒代理、避开内置对象、用 `revocable` 管生命周期               |

> 📌 一句话记忆：  
> **Proxy 是对象的“中间件”。它不修改原对象，而是返回一个带拦截层的包装器；13 种 Trap 覆盖所有底层操作，配合 `Reflect` 保证语义安全；现代响应式与沙箱架构已全面拥抱 Proxy，掌握它等于掌握 JS 元编程的钥匙。**

如需深入某个方向（如：Vue 3 `reactive` 源码级实现、如何用 Proxy 构建安全 JS 沙箱、TS 中泛型 Proxy 的类型推导技巧、或 Proxy 性能压测数据），可提供具体场景，我将给出可落地的架构方案与代码实现。