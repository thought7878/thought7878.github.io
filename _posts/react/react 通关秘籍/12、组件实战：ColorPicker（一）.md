选择颜色是常见需求，想必大家都用过 ColorPicker 组件。

比如 Chrome DevTools 的这个：

![[react/react 通关秘籍/media/b13702cf7828e61631a795d177a414b2_MD5.gif]]

antd 也有 ColorPicker 组件：

![[react/react 通关秘籍/media/732d859c6ddc42b6fefd1a2764fd455f_MD5.gif]]

其实浏览器原生也支持 color 类型的 input：
```html
<input type="color"/>：
```
![[react/react 通关秘籍/media/8ce1c7e14034916f709fb9c55523372b_MD5.gif]]

功能更强大，还支持网页颜色吸取。

兼容性也很不错：

![[react/react 通关秘籍/media/7dd8108dba2cdbfe3fae002833b4fc21_MD5.png]]

那为什么 antd 还在 5.5 版本实现一个 ColorPicker 呢？

![[react/react 通关秘籍/media/89d4000ab6e4490834423febf70d3f25_MD5.png]]

主要是为了统一 UI，因为浏览器原生组件各个浏览器都不一样。

比如 safari 的 \<input type="color"/> 是这样的：

![[react/react 通关秘籍/media/4eecc0278952c09c8600f51dbd23f5fc_MD5.gif]]

safari 的这个做的还挺复杂的，还有一个原生的窗口来做选择，支持的功能挺多：

![[react/react 通关秘籍/media/66354732929bd82e16e3662628b36269_MD5.gif]]

但这样会导致产品在各个浏览器的体验是不一致的。

出于这个原因，我们会用 antd 的 ColorPicker 组件，而不是原生的 color 类型的 input。

那这个颜色选择组件是怎么实现的呢？

![[react/react 通关秘籍/media/7d4006e1daa9e504e1571d6d6ee18a03_MD5.png]]

这就要学习一些颜色的知识了。

颜色有很多种表示法，RGB 是最常用的，分别是 red、green、blue，还可以用十六进制标识法 #FFFFFF

R、G、B 的取值范围是 0 到 255。

颜色用空格或者逗号分隔都行，最后的 / 后面是透明度，可以用百分比或者小数：

![[react/react 通关秘籍/media/88e31e2996ec18c47f2946dd632c4c28_MD5.png]]

此外，HSL 标识法也很常用，分别是色相、饱和度、亮度，/ 后面是透明度。

色相的取值范围是 0 到 360

饱和度和亮度都是 0% 到 100%

![[react/react 通关秘籍/media/ca9296dc3608de9c55ef280f057d2e0f_MD5.png]]

那为什么还会有 0.3turn、150deg 这种单位呢？

因为色相是色相环上的颜色：

![[react/react 通关秘籍/media/cc164bbc6ab29b866283f08230f677f0_MD5.png]]

美术生应该很熟悉这种色相环，什么相差 60 度是邻近色、相差 180 度是互补色等等。

所以色相的取值范围是 0 到 360 也就是共 360 度。

0.3 turn 就是一圈的 0.3 的地方的颜色，而 150 deg 就是 150 度的地方的颜色。

有 RGB 不就好了么？为啥还要搞个 HSL？

红绿蓝是计算机存储颜色的方式，它喜欢这种表示法，可以直接用来显示颜色。

但是对人来说，是不是还是明暗关系、色彩饱和度更容易理解一点？

所以选色的时候都是基于色相、饱和度、亮度这些东西，但存储的时候使用 RGB，最后屏幕显示颜色也是基于 RGB的。

此外，还有 HSV/HSB，这俩用明度而不是亮度，都是差不多的东西：

![[react/react 通关秘籍/media/6e97110cd6f8d1ecf7c9ac01758d45b8_MD5.png]]

所以说，HSL 对人很友好，调解下明暗度、色彩饱和度等很直观。在网页里支持 RGB 和 HSL 这俩表示法。

颜色选择器一般都是基于 HSL 来做的：

![[react/react 通关秘籍/media/8ce916162dc5874322b6ad345e8c56f7_MD5.gif]]

你拖动下面的色彩条的时候，调节的就是色相环的位置，色相环为 0 的时候是红色、色相环 360 的时候也是红色，正好转一圈。

![[react/react 通关秘籍/media/f13e7e046e7e9eceac746b57b1f733fc_MD5.gif]]

你拖动上面的滑块的时候，调节的就是饱和度和亮度。

图中可以看到色相没变，往下滑亮度减少、往左滑饱和度减少。

是不是很直观？调节颜色的体验很好？

那如果用 RBG 来做这种颜色调节呢？

safari 的颜色选择器里就有这个，是这样的：

![[react/react 通关秘籍/media/5d640354e28eab765fb8c4c1856c69e2_MD5.gif]]

是不是用起来一脸懵逼？

怎么就变成粉色了，怎么又变紫色了？

不止你懵逼，设计师用起来也懵逼。

所以颜色选择器一般都是 HSL 的，调节色相、饱和度、亮度这三者，而不是直接调节 RGB。

最后显示的时候才转成 RGB。

rgb 和 hsl 的互转算法都是固定的，可以安装用 [@swiftcarrot/color-fns](https://www.npmjs.com/package/@swiftcarrot/color-fns) 这个包来做：

![[react/react 通关秘籍/media/7c2889eff143f1a0f1b438a0e452fd3d_MD5.png]]

然后我们具体来看下 ColorPicker 的每一部分怎么实现：

![[react/react 通关秘籍/media/dc9541269893ede5471ba46edd949033_MD5.gif]]

下面这个透明度滑块很容易理解，拖动改变的是透明度，从 0% 到 100%，

![[react/react 通关秘籍/media/2a1bf44730e40fb53c10b755c96d80c4_MD5.png]]

滑块设置一个渐变背景就行。

上面色相的滑块也差不多，取值范围是 0 到 360

![[react/react 通关秘籍/media/ad154e8aaee43cd6bec3929de14124bf_MD5.gif]]

但它的渐变设置比较麻烦，不是两个颜色的渐变。不然从红色渐变到红色么？

而是根据取色相环不同角度的颜色来设置渐变：

![[react/react 通关秘籍/media/cc164bbc6ab29b866283f08230f677f0_MD5.png]]

比如取 0、60、120、180、240、360 这些角度共 7 个颜色来渐变：

![[react/react 通关秘籍/media/95dad90cc5c5efa5fec5ebe1d2a41073_MD5.png]]

取出的值是 0 到 360 的色相值。

最后，上面的调整饱和度亮度的部分又是怎么实现的呢？

![[react/react 通关秘籍/media/8dfb9a63dff1250da1a1b236a40ee9ba_MD5.gif]]

其实也很简单，也是加了渐变，把渐变去掉就是纯色了。

![[react/react 通关秘籍/media/e65ac87b16bfb23ce4e013c6de9b26f6_MD5.png]]

一共两个渐变：

从下到上是黑色到透明的渐变。

然后从左到右是从白到透明的渐变。

这样，就可以根据 left、top 的值，计算出饱和度和亮度的值，从上到下饱和度从 100% 到 0%，从由向左饱和度从 100% 到 0%。

这样通过 3 个滑块，就实现了任意透明度、任意色相、亮度、饱和度的颜色的选取。

之后再转成计算机喜欢的 RGB 就好了。

这就是 ColorPicker 的实现原理。

有同学可能会问，那这个吸色器呢？

![[react/react 通关秘籍/media/a8f98f7a75cf38bf27e18d4e68ea37bd_MD5.png]]

这个东西可不是网页里实现的，这个是原生组件，浏览器底层可以很方便的拿到网页渲染的结果，然后取色。

不过浏览器现在也有这个 api 了，叫做 EyeDropper

![[react/react 通关秘籍/media/fefd31943ce6fe5df1fa7678a8f5455b_MD5.png]]

用起来很简单：

![[react/react 通关秘籍/media/5fab4473d30e321f2392185224838d39_MD5.png]]

效果也很好：

![[react/react 通关秘籍/media/f607ddd00800567ffd4a6877ea3e11c0_MD5.gif]]

但是你看看这个惨不忍睹的兼容性：

![[react/react 通关秘籍/media/b854128bf06fef6f1b41139959bd97e4_MD5.png]]

如果你要吸取颜色，还是用原生组件 \<input type="color"/>好了。

## 总结

选择颜色是常见需求，可以用浏览器的 \<input type="color"/> 的原生标签，也可以用 antd 的 ColorPicker 组件。

原生标签虽然支持的功能多，但是各个浏览器实现不一致。

实现这样的颜色选择组件，需要了解颜色表示法：

网页支持的颜色表示法有 RGB、HSL 两种：

RGB 是计算机喜欢的颜色表示法，可以直接用红绿蓝来显示颜色。

HSL 是人更喜欢的颜色表示法，用色相、饱和度、亮度来表示颜色，最后转成 RGB。

（HSV/HSB 和 HSL 是一样的东西，只不过叫明度而不是亮度）

ColorPicker 一般都是用 HSL 来实现的，通过滑块调节色相、饱和度、亮度，显示的时候加上几个渐变，就能实现这种组件：

![[react/react 通关秘籍/media/b7850930af28e86fbf71e0c364b17df7_MD5.png]]

理解了 HSL 颜色表示法，下节我们自己实现下 ColorPicker 组件。
