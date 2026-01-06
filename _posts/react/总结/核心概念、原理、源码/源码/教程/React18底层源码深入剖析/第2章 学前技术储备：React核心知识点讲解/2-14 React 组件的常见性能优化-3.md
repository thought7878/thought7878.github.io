

本视频讲述了React组件**性能优化中的缓存策略**，重点介绍*useMemo与useCallback的使用场景*及实现方式，并通过代码示例*演示了如何减少不必要的组件渲染和昂贵计算*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/947951d5f0d7ff285fa1bc6b7cc43966_MD5.webp]]

## 缓存策略概述 
[00:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=7)
    
- 使用 `useMemo` 和 `useCallback` 实现缓存。
- **目的**是**避免重复计算**和**不必要的组件重新渲染**。

## useMemo
[00:12](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=12)
    
- `useMemo` 可以**缓存计算结果**，**避免在每次渲染时都重新执行昂贵操作**。
- 示例：点击按钮新增组件时，仅最后一个组件需要重新渲染，其余组件不更新。

### useMemo 缓存计算值 
[01:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=95)
    
- 组件包含两个状态：`count` 和 `value`。
- 每次点击按钮或输入框变化都会触发组件重新渲染。
- 存在一个函数 `expensive` 根据 `count` 值进行累加计算。
- 如果 `value` 改变也触发该函数重新计算，则造成浪费。
- 使用 `useMemo` 缓存该函数返回值，仅当 `count` 变化时才重新执行：

```js
	const expensive = useMemo(() => {
	  return countFunction(count);
	}, [count]);
```
  
### 优化效果验证 
[03:52](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=232)
    
- 点击 `count` 按钮，`expensive` 函数重新执行。
- 修改 `value` 输入框内容，`expensive` 不再重新计算。

## useCallback
[04:18](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=258)
    
- `useCallback` 用于**缓存函数本身**。
- 在*将函数作为 props 传递给子组件*时，**防止因函数引用变化导致子组件不必要的重新渲染**。

### 未使用 useCallback 的问题 
[04:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=290)
    
- *子组件使用 `React.memo` 进行优化，但传入的函数每次都不同，导致无效比较*。
- 即使 `value` 变化与函数无关，子组件仍会重新渲染。

### 使用 useCallback 缓存函数 
[06:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=368)
    
- 将 `addClick` 函数用 `useCallback` 包裹并指定依赖项 `[count]`：
```js
	const addClick = useCallback(() => {
	  setCount(count + 1);
	}, [count]);
```
- *仅当 `count` 变化时函数引用才会更新*。
- *子组件在 `value` 变化时不重新渲染*。

## useMemo 与 useCallback 的区别 
[06:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=411)
    
- `useMemo` **缓存的是值（如计算结果）**。
- `useCallback` **缓存的是函数引用**。
- 两者*均通过依赖项控制是否重新执行*。

## 性能优化总结 
[07:36](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=456)
    
- React 组件常见性能优化策略包括：
	- 组件拆分：降低组件粒度。
	- 避免不必要的渲染：使用 `React.memo`。
	- 缓存策略：使用 `useMemo` 和 `useCallback` 减少重复计算和函数创建。