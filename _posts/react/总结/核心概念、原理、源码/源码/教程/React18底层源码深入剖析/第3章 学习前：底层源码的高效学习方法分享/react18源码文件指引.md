
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
### ReactChildFiber.js 
- 定义子节点协调方法reconcileChildFiber（React的diff重点在于数组情况下的协调方法reconcileChildrenArray）

### ReactFiber.js 
- 定义Fiber(转移到ReactInternalTypes.js)、FiberNode数据结构
- 定义创建fiber方法createFiber()

### ReactFiberBeginWork.js 
- 不同节点的更新方法与子节点协调方法reconcileChildren，节点更新方法如updateFunctionComponent、updateClassComponent、updateFragment等。
- 其实**节点的更新就是更新自身和协调子节点**，如函数组件就是处理context、检查自身props、处理hook等，然后执行本函数，再协调子节点。

### ReactFiberHooks.js（面试考hooks源码都在这里）
- hooks的处理，如执行函数组件里的renderWithHooks等

## scheduler/src：调度 

## react-is/src
- 判断React元素类型，如ReactIs.isContextConsumer(<ThemeContext.Consumer />);//true




