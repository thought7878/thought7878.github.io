

### **自定义守卫（类型谓词---Type Predicates）**

### **一、基本概念**
**自定义守卫（类型谓词）** 允许开发者*通过函数返回值明确指定变量的类型*，帮助 TypeScript 在特定作用域内精确推断类型。使用 `parameterName is Type` 语法定义。

**示例**：
```typescript
function isString(value: unknown): value is string {
    return typeof value === 'string';
}
```

---

### **二、核心用法**
#### **1. 联合类型的类型收窄**
处理联合类型时，通过*守卫函数*明确具体类型：
```typescript
type Value = string | number;

function processValue(value: Value) {
    if (isString(value)) {
        console.log(value.toUpperCase()); // 类型收窄为 string
    } else {
        console.log(value.toFixed(2));    // 类型收窄为 number
    }
}
```

#### **2. 复杂对象类型检查**
验证对象属性及类型，确保符合接口定义：
```typescript
interface User {
    name: string;
    age: number;
}

function isUser(obj: any): obj is User {
    return 'name' in obj && 'age' in obj 
        && typeof obj.name === 'string' 
        && typeof obj.age === 'number';
}

const data: unknown = { name: "Alice", age: 30 };
if (isUser(data)) {
    console.log(data.name); // 类型收窄为 User
}
```

#### **3. 结合泛型实现通用检查**
编写通用守卫函数，复用类型检查逻辑：
```typescript
function isArrayOf<T>(arr: any[], guard: (e: any) => e is T): arr is T[] {
    return arr.every(guard);
}

const arr: unknown[] = [1, 2, 3];
if (isArrayOf(arr, (x): x is number => typeof x === 'number')) {
    console.log(arr.map(x => x * 2)); // arr 类型收窄为 number[]
}
```

#### **4. 类方法中的 `this` 类型收窄**
在类方法中收窄 `this` 类型，安全调用子类方法：
```typescript
class Animal {
    isDog(): this is Dog {
        return this instanceof Dog;
    }
}

class Dog extends Animal {
    bark() { console.log('Woof!'); }
}

const animal = new Dog();
if (animal.isDog()) {
    animal.bark(); // 安全调用 Dog 类方法
}
```

---

### **三、注意事项**
1. **逻辑准确性**  
   守卫函数内部逻辑必须严格正确，否则会导致类型误判，引发运行时错误。

2. **参数名一致性**  
   类型谓词中的参数名必须与函数参数名一致：
   ```typescript
   // 正确
   function isString(value: unknown): value is string { ... }

   // 错误：参数名不匹配
   function isString(val: unknown): value is string { ... }
   ```

3. **避免过度复杂**  
   自定义守卫应保持简洁，过度复杂的逻辑可能降低代码可读性和维护性。

4. **不可替代类型系统**  
   守卫函数是辅助工具，不能替代 TypeScript 的静态类型检查。滥用可能导致隐藏的类型错误。

---

### **四、典型应用场景**
1. **动态数据校验**  
   如 API 响应数据校验，确保数据符合预期结构。
2. **类型安全的工厂函数**  
   根据输入参数动态创建不同类型的对象。
3. **处理第三方库的不确定类型**  
   对未知类型的数据进行验证，确保安全访问属性。

---

### **五、总结**
- **核心价值**：  
  自定义守卫通过显式类型断言，增强复杂场景下的类型安全性，*弥补内置类型守卫的不足*。
  
- **适用场景**：  
  - 联合类型的分支处理
  - 动态数据的结构验证
  - 类层次结构中的类型细化

- **最佳实践**：  
  - 保持守卫函数简洁、专注单一职责。
  - 结合单元测试验证守卫逻辑的正确性。
  - 优先使用内置守卫（如 `typeof`、`instanceof`），仅在必要时引入自定义守卫。

掌握自定义守卫的使用，能够显著提升 TypeScript 代码的健壮性和可维护性，尤其在处理动态类型和复杂数据结构时，提供更强大的类型安全保障。