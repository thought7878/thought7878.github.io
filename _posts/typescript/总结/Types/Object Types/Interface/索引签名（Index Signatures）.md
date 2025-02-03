---

### **TypeScript 索引签名总结**

---

#### **一、基本概念**
索引签名（Index Signatures）用于定义对象的 **动态键类型**，允许对象通过特定类型的键（如 `string` 或 `number`）访问值。它适用于处理类似字典或动态键值对的结构。

---

#### **二、核心语法**
```typescript
interface DynamicObject {
  [key: string]: ValueType;  // 字符串索引
  [index: number]: ValueType; // 数字索引
}
```
- **`key`**：索引键名（可自定义名称，如 `id`）。  
- **`ValueType`**：值的类型约束。

---

#### **三、字符串索引签名**
1. **基本用法**  
   定义所有字符串键对应的值类型：  
   ```typescript
   interface StringKeyDict {
     [key: string]: number;
   }
   const dict: StringKeyDict = { a: 1, b: 2 };
   console.log(dict["c"]); // undefined（类型为 number）
   ```

2. **与显式属性共存**  
   显式声明的属性必须符合索引签名的类型约束：  
   ```typescript
   interface User {
     name: string;        // ✅ 必须符合 string: any 的约束
     [key: string]: any;  // 允许任意字符串键
   }
   ```

---

#### **四、数字索引签名**
1. **数组的隐式索引**  
   数组本质上是数字索引签名的对象：  
   ```typescript
   interface NumberKeyArray {
     [index: number]: string;
   }
   const arr: NumberKeyArray = ["a", "b"];
   console.log(arr[0]); // "a"
   ```

2. **与字符串索引共存**  
   若同时存在字符串索引，则 **数字索引的值类型必须是字符串索引值类型的子类型**：  
   ```typescript
   interface MixedDict {
     [key: string]: string | number;  // 父类型
     [index: number]: string;         // 子类型
   }
   ```

---

#### **五、类型限制**
1. **键类型限制**  
   - 索引键类型**只能是 `string` 或 `number`**。  
   - `symbol` 类型需使用 `symbol` 索引签名（ES6+）：  
     ```typescript
     interface SymbolDict {
       [key: symbol]: string;
     }
     ```

2. **值类型一致性**  
   所有动态键的值必须符合索引签名的类型：  
   ```typescript
   interface StrictDict {
     [key: string]: number;
     name: number; // ✅
     age: string;  // ❌ 类型不匹配
   }
   ```

---

#### **六、应用场景**
1. **动态 API 响应**  
   处理未知键的 JSON 数据：  
   ```typescript
   interface ApiResponse {
     status: "success" | "error";
     [key: string]: any; // 允许额外字段
   }
   ```

2. **字典结构**  
   实现键值映射：  
   ```typescript
   interface WordCount {
     [word: string]: number;
   }
   ```

3. **类型安全增强**  
   限制动态键的类型范围：  
   ```typescript
   type ValidKeys = "id" | "name" | "email";
   type StrictDict = {
     [K in ValidKeys]: string; // 映射类型替代宽泛的索引签名
   };
   ```

---

#### **七、高级用法**
1. **联合类型值**  
   允许不同键对应不同类型：  
   ```typescript
   interface MixedValueDict {
     [key: string]: string | number;
   }
   ```

2. **只读索引**  
   防止动态键被修改：  
   ```typescript
   interface ReadonlyDict {
     readonly [key: string]: number;
   }
   ```

3. **索引签名与泛型结合**  
   定义通用字典结构：  
   ```typescript
   interface GenericDict<T> {
     [key: string]: T;
   }
   const dict: GenericDict<boolean> = { a: true };
   ```

---

#### **八、注意事项**
1. **类型安全风险**  
   索引签名允许未声明的键访问，可能导致运行时错误：  
   ```typescript
   const dict: { [key: string]: number } = { a: 1 };
   console.log(dict["b"]); // undefined（类型为 number，但实际不存在）
   ```

2. **替代方案**  
   - **Record 工具类型**：更简洁的字典定义：  
     ```typescript
     type SafeDict = Record<string, number>;
     ```
   - **映射类型**：精确控制键范围：  
     ```typescript
     type Keys = "a" | "b";
     type ExactDict = { [K in Keys]: number };
     ```

---

### **总结**
- **核心作用**：为动态键对象提供类型约束，平衡灵活性与安全性。  
- **适用场景**：处理未知键结构、字典映射、动态配置等。  
- **最佳实践**：  
  - 优先使用 `Record` 或映射类型替代宽泛的索引签名。  
  - 结合显式属性声明增强类型准确性。  
  - 谨慎处理可能存在的未定义键值。