### 装饰器（Decorator）

装饰器（Decorator）是 TypeScript 中的一种特殊语法，用于**增强或修改类、方法、属性等的行为**。它通过 `@expression` 的形式附加到目标上，广泛应用于框架（如 Angular）、日志、验证等场景。以下是装饰器的核心概念与用法：

---

#### 一、装饰器基础

##### 1. 启用装饰器
在 `tsconfig.json` 中启用实验性装饰器支持：
```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

##### 2. 装饰器类型
- **类装饰器**（Class Decorator）
- **方法装饰器**（Method Decorator）
- **属性装饰器**（Property Decorator）
- **参数装饰器**（Parameter Decorator）
- **访问器装饰器**（Accessor Decorator）

---

#### 二、装饰器用法详解

##### 1. 类装饰器
**作用**：修改或替换类的构造函数。  
**参数**：`target`（类的构造函数）。  
**示例**：添加元数据或扩展功能。
```typescript
function Logger(target: Function) {
  console.log(`类 ${target.name} 被定义`);
}

@Logger
class User {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}
// 输出：类 User 被定义
```

##### 2. 方法装饰器
**作用**：修改或替换类的方法。  
**参数**：`target`（类原型）、`propertyKey`（方法名）、`descriptor`（属性描述符）。  
**示例**：日志记录或性能监控。
```typescript
function LogMethod(target: any, key: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`调用方法 ${key}，参数：${args}`);
    return originalMethod.apply(this, args);
  };
  return descriptor;
}

class Calculator {
  @LogMethod
  add(a: number, b: number): number {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(2, 3); // 输出：调用方法 add，参数：2,3
```

##### 3. 属性装饰器
**作用**：修改或观察类的属性。  
**参数**：`target`（类原型或构造函数）、`propertyKey`（属性名）。  
**示例**：属性初始化或元数据标记。
```typescript
function DefaultValue(value: string) {
  return function (target: any, propertyKey: string) {
    target[propertyKey] = value;
  };
}

class Product {
  @DefaultValue("未命名")
  name: string;
}

const p = new Product();
console.log(p.name); // 输出：未命名
```

##### 4. 参数装饰器
**作用**：标记或验证方法的参数。  
**参数**：`target`（类原型）、`methodName`（方法名）、`parameterIndex`（参数索引）。  
**示例**：参数类型检查或依赖注入标记。
```typescript
function ValidateParam(target: any, methodName: string, parameterIndex: number) {
  console.log(`参数 ${parameterIndex} 在方法 ${methodName} 中被装饰`);
}

class ApiService {
  fetchData(@ValidateParam id: number) {
    // ...
  }
}
// 输出：参数 0 在方法 fetchData 中被装饰
```

##### 5. 访问器装饰器（Getter/Setter）
**作用**：修改或观察类的访问器。  
**参数**：同方法装饰器。  
**示例**：访问控制或数据格式化。
```typescript
function Readonly(target: any, key: string, descriptor: PropertyDescriptor) {
  descriptor.writable = false;
  return descriptor;
}

class Config {
  private _apiUrl: string = "https://api.example.com";

  @Readonly
  get apiUrl(): string {
    return this._apiUrl;
  }
}

const config = new Config();
config.apiUrl = "新地址"; // ❌ 错误：无法修改只读属性
```

---

#### 三、装饰器工厂（Decorator Factory）

装饰器工厂是一个返回装饰器函数的函数，允许传递参数定制装饰器行为。

```typescript
function LogWithMessage(message: string) {
  return function (target: Function) {
    console.log(`${message}: ${target.name}`);
  };
}

@LogWithMessage("类被装饰")
class MyClass {}
// 输出：类被装饰: MyClass
```

---

#### 四、装饰器执行顺序

装饰器的应用顺序遵循以下规则：
1. **参数装饰器** → **方法装饰器** → **访问器装饰器** → **属性装饰器** → **类装饰器**。
2. **同类型装饰器**：从上到下执行，从右到左执行（针对多个装饰器叠加的情况）。

```typescript
function First() {
  console.log("First 工厂");
  return function (target: any) {
    console.log("First 装饰器");
  };
}

function Second() {
  console.log("Second 工厂");
  return function (target: any) {
    console.log("Second 装饰器");
  };
}

@First()
@Second()
class Example {}

/* 输出顺序：
First 工厂
Second 工厂
Second 装饰器
First 装饰器
*/
```

---

#### 五、实际应用场景

##### 1. 依赖注入（Angular）
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class UserService {
  getUsers() { /* ... */ }
}
```

##### 2. 路由定义（NestJS）
```typescript
@Controller('users')
export class UsersController {
  @Get()
  findAll(): string {
    return '返回所有用户';
  }
}
```

##### 3. 数据验证
```typescript
function ValidateEmail(target: any, key: string) {
  let value = target[key];
  const getter = () => value;
  const setter = (newVal: string) => {
    if (!newVal.includes('@')) {
      throw new Error('无效的邮箱地址');
    }
    value = newVal;
  };
  Object.defineProperty(target, key, {
    get: getter,
    set: setter,
  });
}

class User {
  @ValidateEmail
  email: string;
}

const user = new User();
user.email = "invalid"; // ❌ 抛出错误
```

##### 4. 性能监控
```typescript
function MeasureTime(target: any, key: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    const start = performance.now();
    const result = originalMethod.apply(this, args);
    const end = performance.now();
    console.log(`方法 ${key} 执行耗时：${end - start} 毫秒`);
    return result;
  };
}

class DataProcessor {
  @MeasureTime
  processData(data: any[]) {
    // 复杂数据处理逻辑
  }
}
```

---

#### 六、注意事项

1. **实验性特性**：装饰器仍处于 ECMAScript 提案阶段，语法可能变动。
2. **元数据反射**：结合 `reflect-metadata` 库可支持更复杂的元数据操作。
3. **性能影响**：过度使用装饰器*可能增加启动时间或内存消耗*。
4. **静态分析限制**：部分工具（如代码压缩器）可能无法正确处理装饰器。

---

### 总结

装饰器通过**声明式语法**为 TypeScript 代码提供了强大的扩展能力，尤其适合框架开发、AOP（面向切面编程）等场景。合理使用装饰器可以显著提升代码的可维护性和复用性，但需注意其适用边界和潜在的性能影响。