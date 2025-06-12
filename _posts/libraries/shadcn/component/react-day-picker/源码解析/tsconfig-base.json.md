```json
{
  "compilerOptions": {
    "target": "ES2020", // 指定编译器输出的目标 ECMAScript 版本
    "lib": ["DOM", "ES2020", "DOM.Iterable"], // 定义编译时包含的库文件，启用现代 JavaScript 和 DOM API 的使用
    "jsx": "react", // 配置 JSX 语法支持，设置为 "react" 以支持 React 框架，如果使用 Preact 可改为 "preact"
    "module": "ES2020", // 指定使用的模块系统，这里使用 ES2020 模块系统
    "moduleResolution": "Node", // 指定模块解析策略，使用 Node.js 模块解析方式
    "sourceMap": true, // 启用源码映射生成，有助于调试
    "declaration": true, // 启用声明文件生成，输出 .d.ts 类型定义文件
    "outDir": "./dist", // 指定编译输出目录
    "esModuleInterop": true, // 启用 ES 模块与 CommonJS 模块之间的互操作性，便于模块交互
    "forceConsistentCasingInFileNames": true, // 在文件名中强制保持大小写一致性，避免因大小写不一致导致的错误
    "strict": true, // 启用严格的类型检查模式，帮助在编译时捕获潜在错误
    "alwaysStrict": true, // 对所有代码（包括全局代码）启用严格模式
    "skipLibCheck": true, // 跳过对依赖项的类型检查，提升编译性能
    "noUnusedLocals": true // 对未使用的局部变量报告错误，帮助清理冗余代码
  }
}
```