
### **TypeScript 中 `void` 类型的总结**

#### **1. 基本定义**
- **`void`** 是 TypeScript 的原始类型之一，表示 **函数没有返回值**。
- 当函数不返回任何有效值时，应声明其返回类型为 `void`。

#### **2. 函数返回值**
- **显式声明**：  
  ```typescript
  function logMessage(): void {
    console.log("This is a message");
  }
  ```
- **默认推断**：  
  若函数无 `return` 语句或返回 `return;`，TypeScript *自动推断*返回类型为 `void`。

#### **3. 返回值限制**
- **严格模式（`strictNullChecks`）**：  
  - 允许返回 `undefined`（因 `void` 兼容 `undefined`）。  
  - 返回 `null` 会报错（除非显式允许 `null`）。  
  ```typescript
  function fn1(): void {
    return undefined; // ✅ 允许
  }
  function fn2(): void {
    return null; // ❌ 报错（strictNullChecks 下）
  }
  ```

#### **4. 回调函数中的特殊行为**
- **忽略返回值**：  
  声明为 `void` 的回调函数可返回任意值，但 TypeScript 会*忽略返回值检查*。  
  ```typescript
  [1, 2, 3].forEach((item): void => {
    return item * 2; // ✅ 不报错（但返回值无效）
  });
  ```

#### **5. 变量与 `void`**
- **赋值限制**：  
  - 非严格模式：允许 `undefined` 或 `null`。  
  - 严格模式：仅允许 `undefined`。  
  ```typescript
  let a: void = undefined; // ✅
  let b: void = null;      // ❌（strictNullChecks 下）
  ```

#### **6. `void` vs `undefined`**
- **语义差异**：  
  - `void`：表示 **无返回值**（函数逻辑无需返回值）。  
  - `undefined`：表示 **明确返回 `undefined` 值**。  
  ```typescript
  // 返回类型为 undefined 需显式返回
  function getUndef(): undefined {
    return undefined; // 必须显式返回
  }
  ```

#### **7. `void` 操作符**
- **JavaScript 行为**：  
  `void expression` 执行表达式并返回 `undefined`。  
- **TypeScript 类型**：  
  操作结果类型为 `void`，实际值为 `undefined`。  
  ```typescript
  const result: void = void 42; // 类型为 void，值为 undefined
  ```

#### **8. 泛型与异步函数**
- **泛型约束**：  
  泛型可指定 `void` 表示无返回值。  
  ```typescript
  function wrap<T>(callback: () => T): T {
    return callback();
  }
  wrap<void>(() => {
    console.log("No return");
  });
  ```
- **异步函数**：  
  返回 `Promise<void>` 表示无有效结果。  
  ```typescript
  async function fetchData(): Promise<void> {
    await fetch("/api");
    // 无 return
  }
  ```

#### **9. 类型兼容性**
- **函数兼容规则**：  
  源函数返回 `void` 时，目标函数可返回任意类型（返回值被忽略）。  
  ```typescript
  type VoidFunc = () => void;
  const func: VoidFunc = () => "Hello"; // ✅ 允许但返回值无效
  ```

---

### **总结**
- **核心用途**：*声明函数无返回值*，优化代码意图表达。  
- **特殊场景**：回调函数忽略返回值、泛型占位、异步操作无结果。  
- **注意点**：严格模式下的 `undefined`/`null` 限制、与 `undefined` 类型的语义区别。