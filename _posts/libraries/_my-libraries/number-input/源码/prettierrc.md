```json
{
  // 指定JSON schema用于验证prettierrc配置的正确性
  "$schema": "https://json.schemastore.org/prettierrc",
  
  // 设置缩进宽度为2个空格
  "tabWidth": 2,
  
  // 不使用制表符进行缩进
  "useTabs": false,
  
  // 在语句末尾添加分号
  "semi": true,
  
  // 不使用单引号， 默认使用双引号
  "singleQuote": false,
  
  // 不添加尾随逗号
  "trailingComma": "none",
  
  // 使用的插件列表，用于扩展prettier的功能
  "plugins": ["@trivago/prettier-plugin-sort-imports", "prettier-plugin-jsdoc"],
  
  // 导入语句的排序顺序，按照正则表达式匹配的顺序进行排序
  "importOrder": [
    "^react$", // 优先导入react
    "<THIRD_PARTY_MODULES>", // 其次导入第三方模块
    "^@/(.*)$", // 然后导入项目内的模块
    "^../", // 接着导入相对路径的模块
    "^[./]" // 最后导入当前目录或子目录的模块
  ],
  
  // 在不同类型的导入语句之间添加空行进行分隔
  "importOrderSeparation": true
}

```