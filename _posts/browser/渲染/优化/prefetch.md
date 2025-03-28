# 基本概念
    
- **定义**：Prefetch 是一种浏览器资源预获取机制。它通过在 HTML 中添加`<link rel="prefetch"`标签来*指示浏览器在空闲时间预先获取用户可能会访问的**下一个页面**或者资源*。**与 Preload 主要关注当前页面关键资源提前加载不同，Prefetch 更侧重于对未来可能需要的资源进行提前准备**。
- **目的**：其核心目的是提升用户体验，减少用户访问下一个页面或者使用下一个功能时的等待时间。*通过在浏览器后台提前获取资源，当用户真正需要这些资源时，它们已经存储在缓存中，从而加快页面的加载速度*。

![[_posts/base/js/Advanced Web Development Quiz/media/e15b7fda8331ba510577845ec12e861a_MD5.jpeg]]

![[_posts/base/js/Advanced Web Development Quiz/media/ff0341edcc610726f3327c16eb76ff6f_MD5.jpeg]]


# 使用方式
    
- **语法格式**：`<link rel="prefetch" href="资源路径">`。其中，`href`属性指定了要预获取资源的 URL。*这个资源可以是一个完整的网页、一个脚本文件、一个样式表或者其他任何类型的网络资源*。例如，`<link rel="prefetch" href="next-page.html">`表示预获取下一个页面的 HTML 文档，`<link rel="prefetch" href="next-page-script.js">`则是预获取下一个页面的脚本文件。

# 工作原理

- **资源获取时机**：当浏览器解析到带有`rel="prefetch"`属性的`<link>`标签时，*它会在当前页面加载完成且浏览器处于空闲状态（如没有其他紧急的资源加载任务、用户交互操作等）时，利用剩余的网络带宽和系统资源去获取指定的资源*。这种机制充分利用了浏览器的闲置资源，避免对当前页面的加载和性能产生干扰。
- **缓存存储**：*预获取的资源会被存储在浏览器缓存中*。当用户真正访问预获取资源对应的页面或者功能时，浏览器首先会检查缓存，如果缓存中有该资源，就可以直接使用，从而加快了资源的加载速度。例如，如果预获取了下一个页面的脚本文件，当用户进入下一个页面时，*浏览器可以直接从缓存中提取该脚本，而不需要重新从服务器请求*。
- **优先级和资源管理**：*Prefetch 资源的优先级通常低于当前页面关键资源的加载优先级*。浏览器会根据自身的资源管理策略，在不影响当前页面性能的前提下，合理安排 Prefetch 资源的获取。如果浏览器发现当前网络状况不佳或者系统资源紧张，可能会暂停或者延迟 Prefetch 操作。

# 应用场景
    
- **多页面应用（MPA）中的页面预获取**：在一个具有多个页面的网站中，当用户在浏览当前页面时，可以预获取用户可能会访问的下一个页面的资源。比如，在一个电商网站的商品列表页面，可以预获取商品详情页面的资源。

```html
<head>
    <link rel="prefetch" href="product-detail-page.html">
</head>
```

- **异步模块加载的预获取**：对于一些采用异步加载模块的应用，如 JavaScript 模块加载器，可以预获取可能会在后续异步加载的模块。假设一个 Web 应用有一个高级功能模块，只有部分用户会使用，但是当用户需要时希望能快速加载。

```html
<head>
    <link rel="prefetch" href="advanced-feature-module.js">
</head>
```

- **预获取静态资源**：在一些内容丰富的网站中，对于一些静态资源（如图片、样式表等），如果知道用户在后续操作中很可能会用到，也可以进行预获取。例如，在一个图片展示网站，当用户在浏览当前一组图片时，可以预获取下一组图片的资源。

```html
<head>
    <link rel="prefetch" href="next-set-of-pictures.jpg">
</head>
```


# 参考
[ref](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel)

[[5-q5-resource-hints：dns-prefetch、preconnect、prefetch、preload]]
