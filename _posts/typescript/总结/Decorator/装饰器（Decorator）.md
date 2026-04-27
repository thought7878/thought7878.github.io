参考：[[装饰器模式]]

TypeScript `装饰器（Decorator）` 是一种特殊类型的声明，它能够被附加到类声明、方法、访问符、属性或参数上。
装饰器**使用 `@expression` 这种形式，其中 `expression` 求值后必须为一个函数，该函数会在运行时被调用，传入被装饰目标的详细信息**。

`TypeScript 装饰器`*是实现 **AOP** 和 **元编程（Meta-Programming）** 的核心语法糖，也是现代框架（如 `NestJS`, `Angular`）的基石*。

---

### 1. 核心概念与配置

TypeScript 装饰器目前处于 **Stage 3** 提案阶段（旧版为 experimental）。要使用它，需要在 `tsconfig.json` 中开启配置。

#### 配置 `tsconfig.json`
```json
{
  "compilerOptions": {
    "target": "ES5",          // 或更高
    "experimentalDecorators": true,  // 启用装饰器语法
    "emitDecoratorMetadata": true    // 启用设计元数据（用于依赖注入）
  }
}
```
*   `experimentalDecorators`: 允许使用 `@Decorator` 语法。
*   `emitDecoratorMetadata`: 配合 `reflect-metadata` 库，允许在运行时获取类型信息（如 `design:type`）。

#### 安装反射库（必需）
大多数高级用法需要安装 `reflect-metadata` 并在入口文件导入：
```bash
npm install reflect-metadata
```
```typescript
// main.ts
import "reflect-metadata";
```

---

### 2. 装饰器的种类

TypeScript 支持*五种类型的装饰器，每种接收的参数不同：*

1.  **类装饰器 (Class Decorator)**
2.  **方法装饰器 (Method Decorator)**
3.  **访问符装饰器 (Accessor Decorator)**
4.  **属性装饰器 (Property Decorator)**
5.  **参数装饰器 (Parameter Decorator)**

#### (1) 类装饰器 (Class Decorator)
作用于类构造函数。常用于修改类行为、注册类或返回新的构造函数。

```typescript
function Frozen(constructor: Function) {
  // 冻结对象，防止被修改
  Object.freeze(constructor.prototype);
  Object.freeze(constructor);
}

@Frozen
class User {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

const u = new User('Alice');
// u.name = 'Bob'; // 错误：无法修改冻结对象
```

#### (2) 方法装饰器 (Method Decorator)
**最常用**。作用于方法描述符。常用于 **AOP**（日志、权限、事务）。

```typescript
function Log(target: any, key: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  // 重写方法
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${key} with`, args);
    const result = originalMethod.apply(this, args);
    console.log(`Result:`, result);
    return result;
  };

  return descriptor;
}

class Calculator {
  @Log
  add(a: number, b: number) {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(1, 2);
// 输出:
// Calling add with [1, 2]
// Result: 3
```
*   `target`: 类的原型对象（对于静态方法是类构造函数）。
*   `key`: 方法名。
*   `descriptor`: 属性描述符（可修改 `value`, `writable`, `enumerable` 等）。

#### (3) 属性装饰器 (Property Decorator)
作用于属性声明。**注意**：在旧版装饰器规范中，属性装饰器**不能**直接修改属性描述符（因为属性定义在原型上，而非实例），通常用于记录元数据。

```typescript
function ReadOnly(target: any, key: string) {
  // 记录元数据，稍后在类装饰器或初始化时处理
  console.log(`Property ${key} is marked as ReadOnly`);
}

class Config {
  @ReadOnly
  apiKey: string = 'secret';
}
```

#### (4) 访问符装饰器 (Accessor Decorator)
作用于 `get` 或 `set` 访问器。用法同方法装饰器。

```typescript
class Person {
  private _name: string = '';

  @Log
  get name() {
    return this._name;
  }

  set name(val: string) {
    this._name = val;
  }
}
```

#### (5) 参数装饰器 (Parameter Decorator)
作用于方法参数。常用于 **依赖注入（DI）**。它无法修改方法行为，只能读取参数元数据。

```typescript
function PrintParam(target: any, key: string, index: number) {
  console.log(`Parameter ${index} of ${key}`);
}

class Service {
  greet(@PrintParam name: string) {
    console.log(`Hello ${name}`);
  }
}
// 输出：Parameter 0 of greet
```

---

### 3. 装饰器工厂（Decorator Factories）

如果装饰器需要配置（如 `@Log({ level: 'debug' })`），需要使用**装饰器工厂**。它是一个返回装饰器函数的函数。

```typescript
// 工厂函数
function LogMessage(message: string) {
  // 返回真正的装饰器
  return function (target: any, key: string, descriptor: PropertyDescriptor) {
    const original = descriptor.value;
    descriptor.value = function (...args: any[]) {
      console.log(`[ ${message} ] Before ${key}`);
      return original.apply(this, args);
    };
  };
}

class Business {
  @LogMessage('AUDIT')
  processOrder() {
    console.log('Processing...');
  }
}
```

---

### 4. 装饰器的执行顺序

理解执行顺序对于调试至关重要。当一个类有多个装饰器时，执行顺序如下：

1.  **参数装饰器** -> **方法/访问符/属性装饰器** -> **类装饰器**。
2.  在同一类别中：
    *   **实例成员**（属性、方法、参数）：从上到下。
    *   **静态成员**：从上到下。
    *   **类装饰器**：最后执行。
    *   **参数装饰器**：从内到外（从最后一个参数到第一个）。

**示例：**
```typescript
function A(name: string) {
  console.log(`A: ${name}`);
  return (target: any, key?: any, desc?: any) => {};
}

@A('Class Decorator')
class Test {
  @A('Property')
  prop: string;

  @A('Method')
  method(@A('Param 1') p1: string, @A('Param 2') p2: string) {}
}

/* 输出顺序:
A: Param 2
A: Param 1
A: Method
A: Property
A: Class Decorator
*/
```

---

### 5. TypeScript 装饰器 vs 装饰器设计模式

这是一个常见的混淆点，结合你之前的学习，我们需要明确区分：

| 特性 | TypeScript 装饰器语法 | 装饰器设计模式 (Decorator Pattern) |
| :--- | :--- | :--- |
| **本质** | **语言特性 / 元编程**。编译时/加载时修改类定义。 | **设计模式**。运行时通过组合对象来扩展功能。 |
| **实现方式** | 使用 `@` 符号，修改原型或描述符。 | 使用类继承/组合，包装对象。 |
| **目的** | 声明式地添加元数据、AOP 逻辑、DI。 | 动态地给对象添加职责。 |
| **关系** | TS 装饰器可以用来**实现**装饰器模式。 | 是一种思想，不依赖特定语法。 |
| **例子** | NestJS 的 `@Controller`, `@Get`。 | Java IO 流的 `new BufferedInputStream(new FileInputStream())`。 |

**总结**：TypeScript 装饰器语法是实现 AOP 和元编程的工具，而装饰器设计模式是解决对象扩展问题的方案。两者可以结合使用。

---

### 6. 高级应用：元数据反射（Metadata Reflection）

这是 TypeScript 装饰器最强大的地方，也是 **NestJS 依赖注入** 的核心。通过 `reflect-metadata`，我们可以存储和读取自定义元数据。

```typescript
import 'reflect-metadata';

// 定义元数据键
const ROLE_KEY = 'roles';

// 创建角色装饰器
function Roles(...roles: string[]) {
  return Reflect.metadata(ROLE_KEY, roles);
}

class UserController {
  // 存储元数据：这个方法需要 'admin' 角色
  @Roles('admin')
  deleteUser() {
    // ...
  }
}

// 运行时读取元数据（模拟框架行为）
const controller = new UserController();
const roles = Reflect.getMetadata(ROLE_KEY, controller, 'deleteUser');
console.log(roles); // ['admin']
```
**应用场景**：
*   **权限控制**：读取 `@Roles` 决定用户是否有权访问。
*   **API 文档**：读取 `@ApiProperty` 生成 Swagger 文档。
*   **验证**：读取 `@IsEmail` 进行参数校验（如 `class-validator` 库）。

---

### 7. 新标准装饰器（TC39 Stage 3）

TypeScript 5.0+ 开始支持新的 ECMAScript 装饰器标准。这与旧的 `experimentalDecorators` 有显著不同：

1.  **不再操作描述符**：新装饰器不能直接返回 `PropertyDescriptor` 来修改方法行为（这限制了 AOP 能力，但更规范）。
2.  **初始化时机**：新装饰器在类初始化时运行，可以访问实例字段。
3.  **兼容性**：目前 NestJS 等主流框架仍主要基于旧版装饰器。

**新语法示例：**
```typescript
// 新标准下，装饰器接收 (value, context)
function Log(value, { kind, name }) {
  if (kind === 'method') {
    return function (...args: any[]) {
      console.log(`Starting ${name}`);
      return value.call(this, ...args);
    };
  }
}

class MyClass {
  @Log
  greet() {}
}
```
*建议：目前企业级开发（特别是 NestJS）仍主要使用旧版 `experimentalDecorators`，但需关注新标准的迁移。*

---

### 8. 实际框架中的应用（以 NestJS 为例）

NestJS 是 TypeScript AOP 和装饰器的集大成者。

```typescript
// 1. 类装饰器：定义这是一个控制器
@Controller('cats')
export class CatsController {
  
  // 2. 方法装饰器：定义 HTTP GET 请求
  @Get()
  // 3. 装饰器工厂：定义权限
  @Roles('admin') 
  // 4. 参数装饰器：依赖注入
  findAll(@Query() query: any): string[] {
    return ['cat1', 'cat2'];
  }
}
```
*   `@Controller`: 注册路由。
*   `@Get`: 映射 HTTP 方法。
*   `@Roles`: 自定义元数据，用于守卫（Guard）检查权限。
*   `@Query`: 从请求中提取参数并注入。

---

### 9. 优缺点总结

#### ✅ 优点
1.  **声明式编程**：代码更简洁，意图更清晰（如 `@Transactional`）。
2.  **AOP 支持**：完美实现日志、权限、缓存等横切逻辑。
3.  **元编程能力**：运行时获取类型信息，支持依赖注入。
4.  **框架生态**：NestJS、Angular、TypeORM 等核心依赖。

#### ❌ 缺点
1.  **调试困难**：逻辑被隐藏在装饰器背后，调用栈复杂。
2.  **性能开销**：反射和代理会带来轻微性能损耗。
3.  **标准未完全稳定**：新旧装饰器标准并存，存在迁移成本。
4.  **魔法感**：过度使用会让代码像“魔法”，新人难以理解数据流向。

---

### 10. 最佳实践建议

1.  **保持纯粹**：装饰器内部逻辑应尽量简单，复杂逻辑下沉到服务或切面中。
2.  **文档化**：自定义装饰器必须有文档，说明它改变了什么行为。
3.  **避免副作用**：装饰器尽量不要产生难以预料的副作用（如静默失败）。
4.  **类型安全**：利用 TypeScript 泛型确保装饰器参数的类型安全。
5.  **区分场景**：
    *   如果是为了**扩展对象功能**（运行时），考虑 **装饰器设计模式**。
    *   如果是为了**元数据、AOP、DI**（编译/加载时），使用 **TypeScript 装饰器语法**。

### 总结

TypeScript 装饰器是连接 **代码** 与 **元数据** 的桥梁。它让你能够以声明式的方式编写代码，将 **AOP 思想** 落地。

*   对于 **SOLID**：它帮助实现 **SRP**（分离横切关注点）和 **OCP**（扩展功能无需改源码）。
*   对于 **架构**：它是构建 **依赖注入容器** 和 **声明式框架** 的基础。

掌握 TypeScript 装饰器，意味着你具备了阅读和开发现代企业级 TypeScript 框架（如 NestJS）的核心能力。