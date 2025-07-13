`tsc` 是 TypeScript 编译器的*命令行工具*，它用于将 TypeScript 代码（`.ts` 或 `.tsx` 文件）编译成 JavaScript 代码（`.js` 文件）。以下是关于 `tsc` 命令的详细介绍：

### 基本使用

#### 全局安装 TypeScript

在使用 `tsc` 命令之前，需要先全局安装 TypeScript：

```bash
npm install -g typescript
```

#### 编译单个 TypeScript 文件

可以使用 `tsc` 命令后跟 TypeScript 文件的路径来编译单个文件：

```bash
tsc app.ts
```

上述命令会将 `app.ts` 文件编译成 `app.js` 文件，生成的 JavaScript 文件会与原始的 TypeScript 文件位于同一目录下。

#### 编译多个 TypeScript 文件

如果需要编译多个文件，可以依次列出这些文件的路径：

```bash
tsc file1.ts file2.ts
```

### 使用 `tsconfig.json` 配置文件

在实际项目中，通常会使用 `tsconfig.json` 文件来配置编译选项。*当项目根目录下存在 `tsconfig.json` 文件时，直接运行 `tsc` 命令，编译器会根据该配置文件中的设置来编译项目：*

```bash
tsc
```

#### 配置 `tsconfig.json`

以下是一个简单的 `tsconfig.json` 示例：

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist"
  },
  "include": ["src/**/*.ts"]
}
```

在上述配置中，`target` 指定了编译后的 JavaScript 版本为 ES6，`module` 指定了模块系统为 CommonJS，`outDir` 指定了编译后文件的输出目录为 `./dist`，`include` 指定了需要编译的文件范围。

### 常用命令选项

#### `--watch` 或 `-w`

- **作用**：开启监听模式，当 TypeScript 文件发生变化时，编译器会自动重新编译。
- **示例**：

```bash
tsc --watch
```

#### `--project` 或 `-p`

- **作用**：*指定 `tsconfig.json` 文件的路径*，可以用于编译特定配置下的项目。
- **示例**：

```bash
tsc --project ./path/to/tsconfig.json
```

#### `--outDir`

- **作用**：临时指定编译后文件的输出目录，会覆盖 `tsconfig.json` 中的 `outDir` 设置。
- **示例**：

```bash
tsc --outDir ./output
```

#### `--target`

- **作用**：临时指定编译后的 JavaScript 版本，会覆盖 `tsconfig.json` 中的 `target` 设置。
- **示例**：

```bash
tsc --target ES5
```

### 错误处理

当编译过程中出现错误时，`tsc` 命令会输出相应的错误信息，帮助开发者定位和解决问题。例如，如果代码中存在类型错误，编译器会提示具体的错误位置和错误类型：

```bash
test.ts:2:5 - error TS2322: Type 'string' is not assignable to type 'number'.

2     let num: number = "abc";
        ~~~

Found 1 error.
```

通过合理使用 `tsc` 命令及其选项，开发者可以灵活控制 TypeScript 代码的编译过程，提高开发效率。
