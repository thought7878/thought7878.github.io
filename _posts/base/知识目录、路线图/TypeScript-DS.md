> 生成TypeScript的所有知识点。以markdown格式，只要知识点，不要介绍

- **基础类型**
  - 原始类型：`string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`
  - 数组：`T[]` / `Array<T>`
  - 元组：`[T1, T2, ...]`
  - 枚举：数值枚举 / 字符串枚举
  - 特殊类型：`any`, `unknown`, `void`, `never`
  - 字面量类型：字符串/数字/布尔字面量

- **变量声明**
  - `let`, `const`, `var` 的作用域与类型推断
  - 解构赋值（对象/数组）
  - 类型注解与自动类型推断

- **接口（Interfaces）**
  - 对象结构定义
  - 可选属性（`?`）、只读属性（`readonly`）
  - 函数类型定义
  - 索引签名（`[key: T]: U`）
  - 接口继承（`extends`）
  - 声明合并（同名接口合并）

- **类（Classes）**
  - 继承与 `super` 调用
  - 访问修饰符：`public`, `private`, `protected`
  - 抽象类与抽象方法（`abstract`）
  - 静态成员（`static`）
  - 存取器（`get`/`set`）
  - 参数属性（构造函数简写）
  - 类实现接口（`implements`）

- **泛型（Generics）**
  - 泛型函数/接口/类
  - 泛型约束（`T extends U`）
  - `keyof` 与索引类型查询
  - 条件类型（`T extends U ? X : Y`）参考 [[条件类型（Conditional Types）]]
  - 映射类型（`{ [K in keyof T]: ... }`）
  - 默认泛型参数（`T = DefaultType`）
  - 类型推断上下文（`infer` 关键字）

- **函数**
  - 可选参数 / 默认参数 / 剩余参数
  - 函数重载（多个声明 + 单一实现）
  - `this` 参数类型定义
  - 箭头函数与上下文类型绑定
  - 函数类型表达式（`(arg: T) => U`）

- **高级类型**
  - 联合类型（`|`）、交叉类型（`&`）
  - 类型别名（`type`）
  - 可辨识联合（Discriminated Unions）
  - 类型守卫：`typeof`, `instanceof`, `in`, 自定义守卫（类型谓词）
  - 类型断言（`as` 语法 / `<T>value`）
  - 非空断言（`!`）、常量断言（`as const`）
  - 工具类型：`Partial`, `Required`, `Readonly`, `Record`, `Pick`, `Omit`, `Exclude`, `Extract`, `NonNullable`, `ReturnType`, `Parameters`, 等

- **模块与命名空间**
  - ES 模块（`import`/`export`）
  - 命名空间（`namespace`）
  - 模块解析策略（相对路径 / Node.js）
  - 声明文件（`.d.ts`）与 `declare` 关键字
  - 三斜线指令（`/// <reference>`）

- **装饰器（Decorators）**
  - 类装饰器 / 方法装饰器 / 属性装饰器 / 参数装饰器
  - 装饰器工厂（带参数的装饰器）
  - 元数据反射（`reflect-metadata`）

- **类型兼容性**
  - 结构子类型（鸭子类型）
  - 函数参数双变（`strictFunctionTypes` 控制）
  - 枚举与数值的兼容性
  - 泛型兼容性规则

- **异步编程**
  - `Promise<T>` 类型
  - `async`/`await` 语法
  - 异步迭代器（`for await...of`）

- **配置与工具**
  - `tsconfig.json` 配置项（`target`, `module`, `strict`, `lib`, 等）
  - 类型定义管理（`@types` 包）
  - 编译器 API（`tsc` 命令选项）
  - 调试配置（Source Map 集成）

- **错误处理**
  - `never` 类型（表示不可能的状态）
  - 断言函数（`asserts condition`）

- **其他高级特性**
  - 模板字面量类型（`${T}`）
  - `satisfies` 操作符（TS 4.9+）
  - 常量枚举（`const enum`）
  - 混入（Mixins）
  - 全局类型扩展（`declare global`）
  - 条件类型中的 `infer` 推理



