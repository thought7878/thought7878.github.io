```json
/* eslint-env node */
// @ts-check

/**
 * ESLint配置对象，用于定义代码检查规则和设置
 * @type {import("eslint").Linter.Config}
 */
const config = {
  // 指定ESLint使用的解析器
  parser: "@typescript-eslint/parser",
  // 指定不进行检查的文件模式
  ignorePatterns: ["**/*.css.d.ts", "dist", "build"],
  // 定义要使用的插件
  plugins: ["react", "@typescript-eslint", "jest"],
  // 定义继承的配置
  extends: [
    "plugin:jest/recommended",
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react-hooks/recommended",
    "prettier",
    "plugin:prettier/recommended"
  ],
  // 设置特定于环境的选项
  settings: {
    react: {
      // 自动检测安装的React版本
      version: "detect" 
    }
  },
  // 定义具体的规则及其级别
  rules: {
    "react/jsx-uses-react": "off",
    "react/prop-types": "off",
    "prettier/prettier": "warn",
    "no-console": "warn",
    "@typescript-eslint/no-unused-vars": "off",
    "@typescript-eslint/no-empty-interface": "off",
    "@typescript-eslint/no-explicit-any": "warn"
  },
  // 定义针对特定文件的配置
  overrides: [
    {
      // 匹配测试文件
      files: ["**/?(*.)+(spec|test).[jt]s?(x)"],
      // 设置测试环境
      env: {
        jest: true
      },
      // 继承测试相关的配置
      extends: ["plugin:jest/recommended", "plugin:testing-library/react"],
      // 设置测试库特定的规则
      rules: {
        "testing-library/no-render-in-setup": "off",
        "testing-library/no-node-access": "off",
        "testing-library/render-result-naming-convention": "off",
        "testing-library/no-render-in-lifecycle": "off"
      }
    }
  ]
};

// 导出配置对象以供ESLint使用
module.exports = config;
```