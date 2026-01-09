本视频讲述了**React中任务调度的两种方式**——`合作式调度`与`抢占式调度`，*对比了二者在浏览器环境下的应用及优缺点*，重点*解析了时间切片和aging策略如何避免低优先级任务“饿死”的问题*，并*简要提及源码中的`markStarvedLanesAsExpired`机制实现*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第6章 React18全新底层核心运行机制：任务调度机制源码阅读，思维拔高/media/8defa4ccbe7b711845375a4d4e020860_MD5.webp]]

## 合作式调度简介 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=0)
    
- React中的`schedule`文件夹采用的是合作式调度策略。
- 该调度器名称为Cooperative Scheduler，适用于浏览器环境。
- **调度过程**包括*将任务放入任务池，并依据策略决定执行顺序*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第6章 React18全新底层核心运行机制：任务调度机制源码阅读，思维拔高/media/a8d84243913887d388eb78b19c0c0b6a_MD5.webp]]

## 抢占式调度机制 
[00:57](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=57)
    
- `抢占式调度`**根据任务优先级决定执行顺序，高优先级任务可中断低优先级任务**。
- 相比合作式调度，*CPU利用率更高，能更快响应重要任务*。
- `“饿死”现象`：低优先级任务*因长期无法竞争到执行权而得不到处理*。

### 任务饿死的定义与影响 
[01:21](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=81)
    
- `“饿死”`指*某些低优先级任务由于持续被更高优先级任务抢占，导致长时间甚至永远得不到执行*。
- 即使是高优先级任务也应避免被阻塞，否则影响整体系统响应性。

### 解决饿死的策略概述 
[01:49](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=109)
    
- 可*通过定期检查机制发现即将“饿死”的任务，并动态提升其优先级以确保执行*。
- *React采用了此类策略来保障任务公平性*。

## 合作式调度的工作原理 
[02:24](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=144)
    
- `合作式调度`依赖**任务主动释放执行权，而非强制中断**。
- 使用`时间切片（time slicing）`技术，**在固定时间段后主动让出主线程**。
- 时间切片示意图显示：若不使用切片，单一长任务会独占主线程，阻碍其他任务执行。
- 到达时间片限制时，任务自行交还控制权，未到则继续执行。

## 时间切片的实现逻辑 
[03:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=236)
    
- 代码逻辑*判断当前是否已到达预设时间点*。
- *若到点，则交还控制权*；否则继续执行当前任务。
- 此机制已在`schedule`中实现，用于**防止主线程被长时间占用**。

## Aging策略：避免饿死的关键手段 
[04:21](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=261)
    
- 针对**抢占式调度下可能出现的任务饿死问题**，引入`aging策略`：
	- **定期检查任务等待时间，若发现任务过期但未执行，则提升其优先级**。
	- **提升后的任务将在下次调度中获得执行机会，从而避免饿死**。

### 源码中的`markStarvedLanesAsExpired`函数 
[05:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=300)
    
- 在`react-reconciler`源码中存在`markStarvedLanesAsExpired`函数，用于标记长期未执行的任务。
- 该函数强制提高“饿死”任务的优先级，使其能够被执行。
- 调用时机位于微任务执行期间或渲染任务结束、交还控制权之前。
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第6章 React18全新底层核心运行机制：任务调度机制源码阅读，思维拔高/media/c2a1557098ca1412b0ed87f0c3c41b72_MD5.webp]]

## 两种防饿死策略总结 
[06:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=376)
    
- **React当前使用两种主要策略防止任务饿死：**
	- `时间切片（Time Slicing）`：通过主动让出执行权保障响应性。
	- `Aging策略`：对长时间等待的任务动态提权，确保最终执行。

## 课程总结 
[06:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=394)
    
- 本节讲解了React中合作式与抢占式调度的区别。
- 强调了浏览器环境下合作式调度结合时间切片的优势。
- 介绍了避免任务饿死的实际解决方案及其在源码中的体现。