---
layout: post
title: ES Modules
subtitle:
categories: engineering
tags: [engineering, module]
# top: 2
---

## ES Modules 特性

- ESM 自动采用严格模式，忽略 'use strict'
- 每个 ESM 都是运行在单独的私有作用域中
- ESM 是通过 CORS 的方式请求外部 JS 模块的
- ESM 的 script 标签会延迟执行脚本 类似 defer

## 导入/导出

```javascript
//1.导出变量/函数/类
export var name = "foo module";

export function hello() {
  console.log("hello");
}

export class Person {}

//2.默认导出/默认导入
// 默认导出
export default name;
// 默认导入
import { default as name } from "";
import name from "";

// 3.取别名导入/导出
// 取别名导入
import name as fooName from ''
import {hello as fooHello} from ''
// 取别名导出
export {
  name as fooName, // 可以取其它名
  hello as fooHello,
};

// 4.导入全部导出的成员
// mod为对象，导出的全部成员为对象mod的属性
import * as mod from './module.js'
console.log(mod.name)

// 5.动态的导入
import('./module.js').then(function (module) {
  console.log(module)
})
```

## 同时使用导入/导出

```javascript
export { default as Button } from "./button.js";
export { Avatar } from "./avatar.js";

// 等同于
import { Button } from "./button.js";
import { Avatar } from "./avatar.js";
export { Button, Avatar };
```

- 使用场景：用来在 index 中整理/管理多个模块的导出

## 导入导出的注意事项

- export 导出的不是一个字面量对象，是语法
- export 实际上是把 引用关系给到了外部，且是只读的
