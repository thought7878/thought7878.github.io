以下是 TypeScript 中数组（Array）常用方法的分类总结，包含功能说明、示例及类型注意事项：

---

### **一、增删元素**
1. **`push(...items: T[]): number`**  
   - **功能**：末尾添加元素，返回新长度。  
   - **示例**：  
     ```typescript
     let arr = [1, 2]; 
     arr.push(3); // [1, 2, 3]
     ```

2. **`pop(): T | undefined`**  
   - **功能**：删除并返回末尾元素。  
   - **类型**：可能返回 `undefined`（空数组时）。

3. **`unshift(...items: T[]): number`**  
   - **功能**：开头添加元素，返回新长度。  

4. **`shift(): T | undefined`**  
   - **功能**：删除并返回第一个元素。

---

### **二、遍历与转换**
5. **`forEach(callback: (value: T, index: number, array: T[]) => void): void`**  
   - **功能**：遍历数组，无返回值。  
   - **示例**：  
     ```typescript
     [1, 2].forEach((num) => console.log(num)); // 1, 2
     ```

6. **`map<U>(callback: (value: T, index: number, array: T[]) => U): U[]`**  
   - **功能**：映射为新数组。  
   - **类型**：自动推断返回类型。  
   - **示例**：  
     ```typescript
     const squares = [1, 2].map(x => x * x); // number[]
     ```

7. **`flat(depth = 1): unknown[]`**  
   - **功能**：扁平化嵌套数组。  
   - **示例**：  
     ```typescript
     const arr = [1, [2, [3]]].flat(2); // number[] → [1, 2, 3]
     ```

8. **`flatMap<U>(callback: (value: T, index: number, array: T[]) => U | U[]): U[]`**  
   - **功能**：映射后扁平化结果。  

---

### **三、筛选与搜索**
9. **`filter(callback: (value: T, index: number, array: T[]) => boolean): T[]`**  
   - **功能**：筛选符合条件元素。  
   - **类型守卫**：使用类型断言可缩小类型：  
     ```typescript
     const mixed = [1, "a", 2];
     const numbers = mixed.filter((x): x is number => typeof x === "number"); // number[]
     ```

10. **`find(callback: (value: T, index: number, array: T[]) => boolean): T | undefined`**  
    - **功能**：查找*首个*符合条件的元素。  

11. **`findIndex(callback: (value: T, index: number, array: T[]) => boolean): number`**  
    - **功能**：*返回元素索引*（未找到返回 `-1`）。  

12. **`includes(searchElement: T, fromIndex?: number): boolean`**  
    - **功能**：检查是否包含元素。  
    - **注意**：严格类型检查（`1 !== "1"`）。

---

### **四、聚合与测试**
13. **`reduce<U>(callback: (acc: U, value: T, index: number, array: T[]) => U, initialValue: U): U`**  
    - **功能**：累计计算结果。  
    - **示例**：  
      ```typescript
      const sum = [1, 2].reduce((acc, num) => acc + num, 0); // 3
      ```

14. **`some(callback: (value: T, index: number, array: T[]) => boolean): boolean`**  
    - **功能**：测试是否有元素符合条件。  

15. **`every(callback: (value: T, index: number, array: T[]) => boolean): boolean`**  
    - **功能**：测试是否*所有元素*符合条件。  

---

### **五、操作与重组**
16. **`slice(start?: number, end?: number): T[]`**  
    - **功能**：截取子数组（不修改原数组）。  

17. **`splice(start: number, deleteCount?: number, ...items: T[]): T[]`**  
    - **功能**：删除/插入元素，返回被删除元素。  

18. **`concat(...items: (T | ConcatArray<T>)[]): T[]`**  
    - **功能**：合并数组。  

19. **`reverse(): T[]`**  
    - **功能**：反转数组（修改原数组）。  

20. **`sort(compareFn?: (a: T, b: T) => number): this`**  
    - **功能**：排序。  
    - **示例**：  
      ```typescript
      [3, 1].sort((a, b) => a - b); // [1, 3]
      ```

---

### **六、其他实用方法**
21. **`join(separator?: string): string`**  
    - **功能**：将元素拼接为字符串。  

22. **`indexOf(searchElement: T, fromIndex?: number): number`**  
    - **功能**：返回元素*首次出现的索引*。  

23. **`entries(): IterableIterator<[number, T]>`**  
    - **功能**：返回索引-值对的迭代器。  

24. **`fill(value: T, start?: number, end?: number): this`**  
    - **功能**：填充数组元素。  

25. **`Array.isArray(value: any): value is any[]`**  
    - **功能**：检查是否为数组（类型守卫）。  

---

### **类型注意事项**
- **类型推断**：多数方法自动推断类型，如 `map` 返回类型由回调决定。  
- **严格空值**：`find` 返回 `T | undefined`，需处理可能的 `undefined`。  
- **可变性**：`push`、`splice` 等方法直接修改原数组，注意副作用。  
- **只读数组**：若数组声明为 `readonly T[]`，则不可用修改方法（如 `push`）。  

---

掌握这些方法可高效操作数组，结合 TypeScript 的类型系统提升代码健壮性。