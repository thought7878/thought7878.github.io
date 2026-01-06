
# react/packages/

## shared：
- react中公用文件，包含了很多的基本类型、数据结构、公用方法
### ReactVersion.js 
- React版本号，可通过React.version查看

### ReactElementType.js 
- ReactElement的数据结构

### ReactTypes.js 
- ReactNode、ReactFragment、ReactContext、ReactProvider等的数据结构

### shallowEqual.js 
- 浅比较函数，如PureComponent、Component的shouldComponentUpdate进行浅比较多时候有用到

### isValidElementType.js 
- 检查是否是有效的Element，主要看type值

### ReactSymbols.js 
- 标记ReactElement的type，同时做了Symbol的兼容 

### objectIs.js 
- Object.is函数 

### ExecutionEnvironment.js 
- 赋值canUseDOM，标记当前执行环境

## react/src：React API

## react-dom/src--/client：ReactDOM API 


## react-dom-bindings

## react-reconciler/src：协调 

## scheduler/src：调度 

## react-is/src
- 判断React元素类型，如ReactIs.isContextConsumer(<ThemeContext.Consumer />);//true




