`Jest` 是一个功能强大且流行的 JavaScript 测试框架，广泛用于前端开发中的**单元测试、集成测试、快照测试**。它由 Facebook 开源，特别适合 React 项目，但也支持其他框架（如 Vue、Angular）和纯 JavaScript 项目。以下是关于 Jest 的核心知识点和使用方法。

---

### 1. Jest 的特点
- **零配置**：  
  Jest 提供开箱即用的默认配置，适合快速上手。
- **快照测试**：  
  支持快照测试，用于捕获 UI 或数据结构的变化。
- **内置断言库**：  
  不需要额外安装断言库，`expect` 提供丰富的匹配器。
- **Mock 功能**：  
  内置强大的 Mock 和 Spy 功能，用于模拟模块或函数行为。
- **并行执行**：  
  测试用例可以并行运行，提升测试效率。
- **代码覆盖率**：  
  内置代码覆盖率工具，无需额外配置即可生成覆盖率报告。

---

### 2. 安装与配置
#### 1) 安装 Jest
通过 npm 或 yarn 安装 Jest：
```bash
npm install --save-dev jest
# 或
yarn add --dev jest
```

#### 2) 配置 Jest
Jest 默认不需要额外配置，但可以通过 `jest.config.js` 文件进行自定义：
```javascript
module.exports = {
  testEnvironment: 'jsdom', // 测试环境，默认为 Node.js 或 jsdom
  testMatch: ['**/__tests__/**/*.test.js'], // 匹配测试文件
  moduleNameMapper: {
    '\\.(css|less|scss)$': 'identity-obj-proxy', // 处理样式文件
  },
  setupFilesAfterEnv: ['<rootDir>/setupTests.js'], // 测试前的初始化文件
};
```

#### 3) 配置脚本
在 `package.json` 中添加测试脚本：
```json
"scripts": {
  "test": "jest"
}
```

---

### 3. 核心功能
#### 1) 单元测试
测试*单个函数或模块*的功能是否正确。
```javascript
// sum.js
function sum(a, b) {
  return a + b;
}
module.exports = sum;

// sum.test.js
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

#### 2) 快照测试
参考：[[快照测试-QW]]
*捕获组件或数据结构的状态*，并在后续测试中比较是否有变化。
```javascript
// snapshot.test.js
const generateConfig = () => ({
  apiUrl: 'https://api.example.com',
  timeout: 5000,
});

test('snapshot matches', () => {
  const config = generateConfig();
  expect(config).toMatchSnapshot();
});
```
如果快照发生变化，Jest 会提示差异，并允许更新快照。

#### 3) 异步测试
测试异步代码（如 Promises 或 async/await）。
```javascript
// fetchData.js
async function fetchData() {
  return { data: 'mocked' };
}
module.exports = fetchData;

// fetchData.test.js
const fetchData = require('./fetchData');

test('fetchData returns correct data', async () => {
  const data = await fetchData();
  expect(data).toEqual({ data: 'mocked' });
});
```

#### 4) Mock 功能
*模拟外部依赖*或函数行为。
```javascript
// api.js
function fetchData() {
  return Promise.resolve({ data: 'real' });
}
module.exports = fetchData;

// api.test.js
jest.mock('./api', () => ({
  fetchData: jest.fn(() => Promise.resolve({ data: 'mocked' })),
}));

const { fetchData } = require('./api');

test('fetchData is mocked', async () => {
  const data = await fetchData();
  expect(data).toEqual({ data: 'mocked' });
});
```

---

### 4. 常用匹配器
Jest 提供了丰富的匹配器（Matchers），用于*验证测试结果*。
- **基本匹配器**：
  ```javascript
  expect(value).toBe(42); // 精确匹配
  expect(value).toEqual({ key: 'value' }); // 深度匹配
  ```
- **布尔值匹配器**：
  ```javascript
  expect(value).toBeTruthy();
  expect(value).toBeFalsy();
  ```
- **数组和字符串匹配器**：
  ```javascript
  expect(array).toContain(42);
  expect(string).toMatch(/regex/);
  ```
- **异常匹配器**：
  ```javascript
  expect(() => {
    throw new Error('error');
  }).toThrow('error');
  ```

---

### 5. 代码覆盖率
Jest 内置代码覆盖率工具，可以通过以下命令生成覆盖率报告：
```bash
jest --coverage
```
生成的报告包括：
- 覆盖率百分比（Statements、Branches、Functions、Lines）。
- 未覆盖的代码高亮显示。

---

### 6. 集成 React 测试
参考：[[React Testing Library-QW]]

Jest 与 [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) 结合，*是 React 项目的最佳实践*。
#### 1) 安装依赖
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

#### 2) 示例
```javascript
// App.js
import React from 'react';

function App() {
  return <h1>Hello, World!</h1>;
}

export default App;

// App.test.js
import { render, screen } from '@testing-library/react';
import '@testing-library/jest-dom';
import App from './App';

test('renders hello world', () => {
  render(<App />);
  const heading = screen.getByText(/hello, world/i);
  expect(heading).toBeInTheDocument();
});
```

---

### 7. 最佳实践
#### 1) 遵循 AAA 模式
- **Arrange**：准备测试数据和环境。
- **Act**：执行被测试的功能。
- **Assert**：验证结果是否符合预期。

#### 2) 使用描述性测试名称
测试名称应*清晰描述测试内容*：
```javascript
test('should return true when input is valid', () => {});
```

#### 3) 避免测试实现细节
专注于测试行为，而不是具体实现。

#### 4) 保持测试独立
每个测试用例应独立运行，避免相互依赖。

---

### 8. 总结
Jest 是一个功能全面、易于使用的测试框架，适合前端开发中的各种测试需求。通过结合 React Testing Library 等工具，可以高效地测试组件和逻辑代码。合理使用 Jest 的快照测试、Mock 功能和代码覆盖率工具，能够显著提升代码质量和开发效率。