TypeScript 作为 JavaScript 的超集，二者具有良好的互操作性，这使得开发者能够在项目中灵活地结合使用它们。以下从不同方面详细介绍 TypeScript 和 JavaScript 的互操作性：

### 1. 在 TypeScript 中使用 JavaScript 代码

#### 直接引入 JavaScript 文件

TypeScript 可以直接引入 JavaScript 文件并使用其中的代码。当引入 JavaScript 文件时，TypeScript 编译器会根据一些规则来处理类型信息。

- **示例**：假设有一个名为 `math.js` 的 JavaScript 文件，内容如下：

```javascript
// math.js
function add(a, b) {
  return a + b;
}

module.exports = {
  add,
};
```

在 TypeScript 文件中引入并使用该文件：

```typescript
// main.ts
const math = require("./math");
const result = math.add(1, 2);
console.log(result);
```

#### 类型声明文件（`.d.ts`）

当引入第三方 JavaScript 库时，*为了让 TypeScript 能够理解这些库的类型信息*，可以使用类型声明文件。类型声明文件可以为 JavaScript 代码提供类型定义，*使得 TypeScript 编译器能够进行类型检查*。

- **示例**：很多流行的 JavaScript 库都有对应的类型声明文件，例如 jQuery。可以使用 `@types` 包来安装这些类型声明文件：

```bash
npm install @types/jquery
```

安装完成后，就可以在 TypeScript 代码中使用 jQuery 并*获得类型检查和代码提示：*

```typescript
import $ from "jquery";
$("#myElement").hide();
```

#### 手动编写类型声明

如果没有可用的类型声明文件，也可以手动编写类型声明。例如，对于上述的 `math.js` 文件，可以编写一个简单的类型声明文件 `math.d.ts`：

```typescript
// math.d.ts
declare module "./math" {
  export function add(a: number, b: number): number;
}
```

### 2. 在 JavaScript 中使用 TypeScript 代码

#### 编译 TypeScript 为 JavaScript

TypeScript 代码需要先编译成 JavaScript 代码才能在 JavaScript 环境中运行。可以使用 TypeScript 编译器（`tsc`）将 TypeScript 文件编译为 JavaScript 文件。

- **示例**：假设有一个 TypeScript 文件 `greeter.ts`：

```typescript
// greeter.ts
function greeter(name: string) {
  return `Hello, ${name}!`;
}

export default greeter;
```

使用以下命令将其编译为 JavaScript：

```bash
npx tsc greeter.ts
```

编译后会生成一个 `greeter.js` 文件，内容如下：

```javascript
function greeter(name) {
  return "Hello, " + name + "!";
}
exports.default = greeter;
```

#### 在 JavaScript 中引入编译后的代码

编译后的 JavaScript 代码可以像普通的 JavaScript 模块一样在其他 JavaScript 文件中引入和使用。

```javascript
// main.js
const greeter = require("./greeter");
const message = greeter.default("John");
console.log(message);
```

### 3. 混合项目开发

在实际项目中，可能会同时存在 TypeScript 和 JavaScript 文件。可以通过配置 TypeScript 编译器（`tsconfig.json`）来支持混合项目开发。

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "allowJs": true, // 允许编译 JavaScript 文件
    "checkJs": true, // 对 JavaScript 文件进行类型检查
    "outDir": "./dist"
  },
  "include": ["src/**/*.ts", "src/**/*.js"]
}
```

在上述配置中，`allowJs` 选项允许 TypeScript 编译器处理 JavaScript 文件，`checkJs` 选项开启对 JavaScript 文件的类型检查。这样，在一个项目中就可以同时使用 TypeScript 和 JavaScript 进行开发。

综上所述，TypeScript 和 JavaScript 的互操作性使得开发者可以根据项目的需求和团队的技术栈，灵活地选择使用这两种语言，逐步迁移项目或者在同一个项目中混合使用它们。

# TS、JS Interoperability

TypeScript and JavaScript have full interoperability, meaning you can use TypeScript code in JavaScript projects and vice versa. TypeScript is a superset of JavaScript, which means that any valid JavaScript code is also valid TypeScript code.  
Typescript 和 JavaScript 具有完整的互操作性，这意味着您可以在 JavaScript 项目中使用 TypeScript 代码，反之亦然。 Typescript 是 JavaScript 的超集，这意味着任何有效的 JavaScript 代码也是有效的 Typescript 代码。

You can use JavaScript libraries in TypeScript projects by either including the JavaScript files directly or using type definitions for the library. Type definitions provide type information for JavaScript libraries, making it easier to use them in TypeScript.  
您可以通过直接包括 JavaScript 文件或使用库的类型定义来使用 JavaScript 库中的 JavaScript 库。类型定义为 JavaScript 库提供类型信息，使其更容易在 TypeScript 中使用它们。

On the other hand, you can use TypeScript code in JavaScript projects by simply compiling the TypeScript code into JavaScript. The generated JavaScript code can be used in any JavaScript environment, and it will work the same way as regular JavaScript code.  
另一方面，您可以在 JavaScript 项目中使用 Typescript 代码，只需将 TypeScript 代码编译到 JavaScript 中即可。生成的 JavaScript 代码可以在任何 JavaScript 环境中使用，并且其工作方式与常规 JavaScript 代码相同。

TypeScript’s compiler also supports type checking for plain JavaScript code by adding the `// @ts-check` comment at the top of a file. This allows the compiler to validate types by inspecting the JSDoc comments:  
TypeScript 的编译器还通过在文件顶部添加`// @ts-check`评论来支持类型检查普通的 JavaScript 代码。这允许编译器通过检查 JSDOC 评论来验证类型：

```ts
// @ts-check

/**
 * Adds two numbers together.
 * @param {number} a - The first number.
 * @param {number} b - The second number.
 * @returns {number} The sum of the two numbers.
 */
function add(a, b) {
  return a + b;
}
```
