大多数可用于 `Number` 数据类型的运算符也可用于 `BigInt` 值（例如算术、比较等）。但是，无符号右移 `>>>` 运算符是一个例外，不受支持。同样，某些运算符的行为可能略有不同（例如，除以 `BigInt` 将四舍五入到零）。

### 注意事项
- **不能与普通 `Number` 混合运算**：`BigInt` 和普通的 `Number` 不能直接进行算术运算，否则会抛出错误。需要先将它们转换为相同类型。

```js
// 下面这行代码会报错
// const errorResult = 5 + 5n; 
```

- **`JSON.stringify` 不支持**：`BigInt` 不能直接使用 `JSON.stringify` 进行序列化，如果需要存储或传输 `BigInt`，需要手动将其转换为字符串。