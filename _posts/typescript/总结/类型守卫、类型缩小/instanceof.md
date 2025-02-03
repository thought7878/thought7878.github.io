以下是 TypeScript 中 **`instanceof` 操作符** 的核心用法、类型收窄作用及注意事项的总结：

---

### **一、基本概念**
`instanceof` 是 *JavaScript 和 TypeScript 共有的*操作符，用于 **检查对象是否为某个构造函数或类的实例**。在 TypeScript 中，它同时具备 **类型守卫（Type Guard）** 功能，可收窄变量类型。

---

### **二、核心作用**
#### **1. 类型收窄（Type Narrowing）**
在条件语句中使用 `instanceof` 后，TypeScript 会自动将变量类型收窄到构造函数对应的类型：  
```typescript
function logValue(value: Date | string) {
  if (value instanceof Date) {
    console.log(value.toISOString()); // value 类型收窄为 Date
  } else {
    console.log(value.toUpperCase()); // value 类型收窄为 string
  }
}
```

#### **2. 检查类的实例**
验证对象是否属于某个类（包括自定义类）：  
```typescript
class Animal {}
class Dog extends Animal {}

const pet = new Dog();
console.log(pet instanceof Dog);    // true
console.log(pet instanceof Animal); // true（继承链生效）
```

---

### **三、使用场景**
#### **1. 处理联合类型**
在联合类型中区分具体类型：  
```typescript
type Response = string | Error;

function handleResponse(res: Response) {
  if (res instanceof Error) {
    console.error(res.message); // res 类型收窄为 Error
  } else {
    console.log(res);           // res 类型收窄为 string
  }
}
```

#### **2. 自定义类的类型守卫**
结合自定义类实现精确类型判断：  
```typescript
class ApiError extends Error {
  code: number;
  constructor(message: string, code: number) {
    super(message);
    this.code = code;
  }
}

function handleError(err: Error) {
  if (err instanceof ApiError) {
    console.log(`API Error ${err.code}: ${err.message}`);
  } else {
    console.log("Generic Error:", err.message);
  }
}
```

---

### **四、注意事项**
#### **1. 无法用于接口或类型别名**
`instanceof` 依赖运行时的构造函数检查，而接口和类型别名是纯类型概念，编译后不存在：  
```typescript
interface Cat {
  meow(): void;
}

// 错误！无法用于接口类型
function test(obj: Cat) {
  if (obj instanceof Cat) { /* ... */ } // 编译错误：'Cat' 仅表示类型
}
```

#### **2. 对原始类型无效**
`instanceof` 仅适用于对象类型，不适用于原始类型（如 `string`、`number`）：  
```typescript
const value: any = "hello";
console.log(value instanceof string); // false（应使用 typeof）
```

#### **3. 跨执行上下文问题**
如果构造函数来自不同作用域（如 iframe、不同模块实例），`instanceof` 可能返回 `false`：  
```typescript
// 假设框架 A 和框架 B 各自定义了相同的类
const objFromA = new FrameworkA.Request();
const objFromB = new FrameworkB.Request();

console.log(objFromA instanceof FrameworkA.Request); // true
console.log(objFromA instanceof FrameworkB.Request); // false
```

---

### **五、与其他类型守卫对比**
| **操作符**      | **适用场景**                  | **示例**                      |
| ------------ | ------------------------- | --------------------------- |
| `typeof`     | 检查原始类型（`string`、`number`） | `typeof value === "string"` |
| `in`         | 检查对象属性                    | `"name" in obj`             |
| `instanceof` | 检查类实例或构造函数                | `obj instanceof Date`       |

---

### **六、总结**
- **核心用途**：  
  `instanceof` 主要用于*运行时检查对象是否为某个类的实例，并结合类型守卫收窄类型*。  
- **适用对象**：  
  类的实例、内置对象（如 `Date`、`Array`）、自定义构造函数。  
- **局限性**：  
  *无法用于接口、类型别名或原始类型*，需结合其他类型守卫（如 `typeof`、`in`）覆盖全部场景。  

熟练掌握 `instanceof` 的类型收窄能力，可以在处理复杂类型逻辑时提升代码的类型安全性。