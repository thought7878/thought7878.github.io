

本视频讲述了React中**避免组件不必要的重新渲染的三种方法**，包括*使用memo函数、shouldComponentUpdate生命周期函数、PureComponent类组件*，并强调了*Context使用时应尽量精简以减少更新范围*。

![](https://img-view-c-sz.drive.quark.cn/KCSmM7Cv/3419011611/8725eaebac9e4e0b8eb62636609bb3ea6867d7a8/6867d7a8a0b8b7d646f7438fac9ac0124af4cd67/preview?auth_key=1767617393-1-10800-5132eb5f62a6953590aa9f49b19078ee&sp=100&token=5-c958601b5eb547522c489024c079a75a-0-3-1024-6b18e99de1eb408b8540b81b0f6da14d-0-0-0-0-1767606593058-40aa7c62aee68ba4d18b4337a3c2640c&ork=m3131FRMG5355beas891UGUuQskW09DH2iBQyME2f&ud=24-0-3-N-N-N-11-N-1-N-0-N-12-N-N&dfi=152)

## 问题背景 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)

- **组件更新有时并不需要重新渲染**，若强行渲染会造成资源浪费。
- 示例：点击按钮添加新元素时，旧元素未发生任何变化，理论上不应重新渲染。

### 示例演示：不拦截的情况 
[00:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=32)
    
- 点击按钮添加一个节点时，原有节点如0、1、2、3均会重新render。
- 实际上*这些节点的props、state、context均未改变，无需更新*。

## 解决方案一：使用memo函数 
[01:33](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=93)
    
- `memo`是一个高阶组件函数，接收组件作为参数返回新组件。
- 默认比较props是否发生变化，若未变则跳过渲染。
- 支持传入第二个比较函数自定义判断逻辑（浅比较）：
	
	```js
	const MemoizedComponent = React.memo(Component, (prevProps, nextProps) => {
	  // 返回 true 表示跳过更新，false 表示继续更新
	});
	```
        
### memo源码解析 
[04:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=274)
    
- 浅比较函数（shallowEqual）用于判断props是否相等。
- 比较过程包括：
	- 判断是否为对象；
	- 属性个数是否一致；
	- 所有属性值是否一一对应。
- 若全部通过则返回true，阻止组件更新。

## 解决方案二：shouldComponentUpdate 
[07:42](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=462)
    
- 类组件生命周期函数，决定组件是否应更新。
- 接收nextProps和nextState两个参数，返回布尔值。
- 示例代码：
	
	```js
	shouldComponentUpdate(nextProps, nextState) {
	  return this.props.item !== nextProps.item;
	}
	```
	
- 若返回false，则跳过render阶段。

## 解决方案三：PureComponent 
[09:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=575)
    
- `React.PureComponent`内置了shouldComponentUpdate的浅比较逻辑。
- 效果与手动实现shouldComponentUpdate相同，但更简洁。
- 不适合深层对象或复杂数据结构的比较。

## 注意事项：Context的影响 
[10:26](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=626)
    
- Context用于祖先组件与后代组件通信。
- 当Context的value变化时，所有依赖该Context的组件都会重新渲染。
- 建议项目中仅使用少量Context（如2~3个），避免影响性能。
- 频繁使用Context可能导致大范围不必要的更新。