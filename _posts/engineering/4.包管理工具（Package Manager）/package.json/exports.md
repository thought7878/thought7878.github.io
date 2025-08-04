# 基本概念
    
- 在`package.json`文件中，`exports`字段是一个用于**定义模块导出**的对象。它提供了一种*更灵活和安全的方式来控制包的使用者可以访问哪些模块路径*，以及如何重定向模块请求。

- 这是从 *Node.js 12 版本*开始引入的一个实验性功能，在后续版本中逐渐稳定和完善，主要用于替代旧的`main`字段来处理模块入口。

# 与传统`main`字段的对比
    
- **旧的`main`字段**：
	- 在`exports`出现之前，`package.json`中的`main`字段用于*指定包的入口点*。*例如*，当你在另一个项目中`require('your - package')`时，Node.js 会查找`your - package`包中的`main`字段指定的文件作为入口。但是，`main`字段**只能指定一个单一的入口文件**，而且不能很好地控制模块的内部结构暴露。

- **`exports`字段的优势**：
	- 有了`exports`字段，你*可以更精细地定义模块的导出规则*。比如，你可以根据不同的导入路径，返回不同的文件，或者对内部模块进行更好的封装，只暴露特定的接口。

# 使用示例

## 简单的模块导出

- 假设你有一个简单的 JavaScript 库，结构如下：

```
my - library/
 ├── src/
 │   ├── index.js
 │   ├── utils.js
 │   └── constants.js
 └── package.json
```

在`package.json`中，你可以这样使用`exports`：

```json
{
 "name": "my - library",
 "exports": {
   ".": "./src/index.js"
 }
}
```

这里`".": "./src/index.js"`表示当使用者`require('my - library')`或者`import 'my - library'`时，实际上会加载`src/index.js`文件作为模块的入口。

## 条件导出（基于环境等因素）

- 你还可以*根据不同的条件进行模块导出*。例如，你的库可能有不同的版本用于浏览器和 Node.js 环境。可以这样写：

```json
{
 "name": "my - library",
 "exports": {
   ".": {
	 "node": "./src/node - index.js",
	 "browser": "./src/browser - index.js"
   }
 }
}
```

*当在 Node.js 环境中导入该库时*，会加载`src/node - index.js`；*在浏览器环境中导入*（如果工具链支持这种区分），会加载`src/browser - index.js`。

## 子模块导出

- 假设`utils.js`文件中有一些实用函数，你想单独暴露这些函数作为子模块。可以这样定义`exports`：

```json
{
 "name": "my - library",
 "exports": {
   ".": "./src/index.js",
   "./utils": "./src/utils.js"
 }
}
```

这样使用者可以通过`require('my - library/utils')`或者`import 'my - library/utils'`来访问`utils.js`文件中的功能。

# 限制和注意事项
- **兼容性**：
	- 虽然`exports`是一个很有用的功能，但在使用时要注意它的兼容性。较老的 Node.js 版本可能不支持`exports`字段，或者支持的方式有限。如果你的包需要兼容较老的环境，可能需要同时保留`main`字段或者使用一些兼容策略。
- **封装和私有模块**：
	- `exports`可以帮助你更好地封装内部模块，但要注意如果不小心暴露了过多的内部细节，可能会导致使用者依赖这些未打算公开的接口。这可能会在未来的库版本更新中引起问题，因为你可能需要改变这些内部模块的实现。