# z-index
https://www.bilibili.com/video/BV11J4m1e7n2/?spm_id_from=pageDriver&vd_source=22af953ea4c09540ad1966711a2d53f0

- [00:01](https://www.bilibili.com/video/BV11J4m1e7n2/?t=1.367681#t=1.37)z-index的含义：绘制顺序
- [03:58](https://www.bilibili.com/video/BV11J4m1e7n2/?t=238.89954#t=03:58.90) 
- [04:17](https://www.bilibili.com/video/BV11J4m1e7n2/?t=238.89954#t=04:16.90) z-index的值
- [06:20](https://www.bilibili.com/video/BV11J4m1e7n2/?t=238.89954#t=06:20.90) 演示


# 理解层叠机制的原理 z-index
https://www.bilibili.com/video/BV1Bx4y1C7oL/?spm_id_from=333.788&vd_source=22af953ea4c09540ad1966711a2d53f0

- [00:01](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=1.150573#t=1.15) 层叠机制：堆叠上下文 / stacking context
	- [03:14](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=194.436489#t=03:14.44) 用图解释stacking context
- 
- [08:13](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=493.16374#t=08:13.16)  创建元素自己的堆叠上下文
	- 文档流：设置容器position非static，且设置子元素z-index非auto
	- flex/grid：设置容器flex/grid，且设置子元素z-index非auto [10:44](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=644.330673#t=10:44.33) 
- 
- [11:35](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=695.476#t=11:35.48) 嵌套的层叠上下文，子元素参与的是最近的祖先元素的层叠上下文
- 
- [14:25](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=865.684524#t=14:25.68) 相同stacking context，相同z-index，后面覆盖前面元素
- 
- [15:34](https://www.bilibili.com/video/BV1Bx4y1C7oL/?t=934.999958#t=15:35.00)  总结




# 用代码实际体验下层叠机制的效果 z-index
https://www.bilibili.com/video/BV1Pt421871L/?spm_id_from=333.788&vd_source=22af953ea4c09540ad1966711a2d53f0

- [00:43](https://www.bilibili.com/video/BV1Pt421871L/?t=43.796653#t=43.80) 设置position非static
04:46}}
06:30}} 子元素被有static context的父元素限制住
08:40}} 2种常用的堆叠情况