[Largest Contentful Paint (LCP)](https://web.dev/articles/lcp?hl=zh-cn) 是三个[核心网页指标](https://web.dev/articles/vitals?hl=zh-cn#core_web_vitals)指标之一，表示**网页主要内容的加载速度**。具体来说，**LCP 衡量的是从用户开始加载网页到在视口中渲染最大的图片或文本块之间的时间**。

为了提供良好的用户体验，**网站应尽力将至少 75% 的网页访问的 LCP 控制在 2.5 秒以内。**

![]()

有很多因素会影响浏览器加载和渲染网页的速度，而其中任何因素的阻塞或延迟都会对 LCP 产生重大影响。

对网页的单个部分进行快速修复能够显著改善 LCP 的情况很少见。**要改进 LCP，您必须了解整个加载过程，并确保整个过程中的每一步都得到优化**。

**针对 LCP 进行优化是一项复杂的任务，对于复杂的任务，通常最好将它们分解为更小、更易于管理的任务，并分别处理每个任务**。本指南将介绍如何将 LCP 细分为最关键的子部分，然后提供有关如何优化每个部分的具体建议和最佳做法。

**注意** ：如需直观地了解本指南中展示的背景信息，请参阅 2022 年 Google I/O 大会上的[深入探究优化 LCP](https://youtu.be/fWoI9DXmpdk)：

## LCP 细分

大多数网页加载通常包含大量网络请求，但为了找出提升 LCP 的机会，您应先查看以下两项：

1. 初始 HTML 文档
2. LCP 资源（如果适用）

虽然网页上的其他请求可能会影响 LCP，但这两个请求（尤其是 LCP 资源开始和结束的时间）会显示您的网页是否针对 LCP 进行了优化。

<u>要确定 LCP 资源</u>，您可以使用开发者工具（例如 [Chrome 开发者工具](https://developer.chrome.com/docs/devtools/?hl=zh-cn)或 [WebPageTest](https://webpagetest.org/)）来确定 [LCP 元素](https://web.dev/articles/lcp?hl=zh-cn#what_elements_are_considered)。然后，您便可以在该元素中匹配由该网页加载的所有资源的[网络瀑布流](https://developer.chrome.com/docs/devtools/network/reference/?hl=zh-cn)中由该元素加载的网址（同样，如果适用）。例如，以下可视化图表所示为在典型网页加载的网络瀑布图上突出显示的资源，其中 LCP 元素需要图片请求才能渲染。

![一个网络瀑布流，其中突出显示了 HTML 和 LCP 资源](https://web.dev/static/articles/optimize-lcp/image/a-network-waterfall-the-919387402b1d3.png?hl=zh-cn)

对于经过充分优化的网页，<u>您应尽早开始加载 LCP 资源请求，并希望 LCP 元素在 LCP 资源加载完成后尽快渲染</u>。为了直观地了解特定网页是否遵循了这一原则，您可以**将总 LCP 时间细分为以下几部分：**

![LCP 明细，显示了四个单独的子部分](https://web.dev/static/articles/optimize-lcp/image/a-breakdown-lcp-showing-23a709f16b362.png?hl=zh-cn)

下表更详细地介绍了各个 LCP 子部分：

| LCP 子部分                               | 说明                                                                                                   |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| 加载第一个字节所需时间 (Time to first byte,TTFB) | 从用户开始加载网页到浏览器收到 HTML 文档响应的第一个字节的时间。（如需了解详情，请参阅 [TTFB](https://web.dev/articles/ttfb?hl=zh-cn) 指标文档）。 |
| 资源加载延迟                                | TTFB 与浏览器开始加载 LCP 资源之间的增量。***                                                                        |
| 资源加载时间                                | 加载 LCP 资源本身所需的时间。***                                                                                 |
| 元素渲染延迟                                | 从 LCP 资源完成加载到 LCP 元素完全渲染之间的增量。                                                                       |

每个网页的 LCP 值都可以细分为这四个子部分。它们之间没有重叠或缺口，它们加起来就是完整的 LCP 时间。

<u>优化 LCP 时，尝试单独优化这些子部分会很有帮助。但同样重要的是要记住，您需要优化所有这些。在某些情况下，应用于某个部分的优化不会改善 LCP，只会将节省的时间转移到另一个部分</u>。例如，在之前的网络瀑布流中，如果您通过进一步压缩图片或切换到更优化的格式（例如 AVIF 或 WebP）来缩减图片的文件大小，虽然这会缩短**资源加载时间**，<u>但实际上并不会改善 LCP，因为时间只会转移到“元素渲染延迟”子部分</u>：出现这种情况的原因是，在此网页上，LCP 元素会处于隐藏状态，直到 JavaScript 代码加载完毕，然后系统会立即显示所有内容。

此示例有助于说明，您需要优化所有这些子部分才能获得最佳 LCP 结果。

![与上文所示的 LCP 细分相同，资源加载时间子部分缩短了，但总体 LCP 时间保持不变。](https://web.dev/static/articles/optimize-lcp/image/the-same-breakdown-lcp-s-ac37446e062a6.png?hl=zh-cn)

### 最佳的子部分的时段

为了优化 LCP 的每个子部分，请务必了解在经过精心优化的网页上渲染这些子部分的理想细分是怎样的。四个子部分中，两个部分的名称中包含“delay”一词。这表示您希望这些时间尽可能接近零。其他两个部分涉及网络请求，就其本质而言，它们需要时间。

| LCP 子部分            | LCP 占比 (%) |
| ------------------ | ---------- |
| 加载第一个字节所需时间 (TTFB) | ~40%       |
| 资源加载延迟             | < 10%      |
| 资源加载时间             | ~40%       |
| 元素渲染延迟             | < 10%      |
| **总计**             | **100%**   |

请注意，这些时间细分并非严格的规则，只是准则。如果网页上的 LCP 时间始终在 2.5 秒以内，那么相对比例并不重要。但是，**如果您在任何一个“延迟”部分中花了很多不必要的时间，那么很难一直达到 [2.5 秒的目标](https://web.dev/articles/lcp?hl=zh-cn#what_is_a_good_lcp_score)。**

<u>LCP 时间明细的一个有效方法是：</u>

- 绝大部分 LCP 时间都应该用在加载 HTML 文档和 LCP 源代码上。
- 在 LCP 之前，每当这两种资源（document/LCP）中有一条未加载时，都有机会改进。

**警告** ：鉴于 LCP 为 [2.5 秒的目标](https://web.dev/articles/lcp?hl=zh-cn#what_is_a_good_lcp_score)，您可能会想要尝试将这些百分比转换为绝对数字，但建议不要这样做。这些子部分之间仅有相对意义，所以最好始终以这种方式进行测量。

## 如何优化各个部分

至此，您已了解在经过精心优化的网页中，每个 LCP 子部分时间应如何细分，接下来就可以开始优化自己的网页了。

接下来的四个部分将就如何优化各个部分提供建议和最佳做法。<u>这些建议是按顺序渲染的</u>，<u>从可能产生最大影响的优化开始</u>。

### 1. 消除*资源加载延迟*

**此步骤的目标是确保 LCP 资源尽早开始加载**。虽然从理论上讲，资源可以开始加载的最早时间是在 TTFB 之后立即进行，但实际上，在浏览器实际开始加载资源之前，总是需要一段延迟（要解析HTML）。

**推荐做法**是，**您的 LCP 资源应与该网页加载的第一个资源同时开始加载。换句话说，如果 LCP 资源开始加载的时间晚于第一个资源，那么就有改进空间**。

![显示 LCP 资源在第一个资源之后启动的网络瀑布图，显示了改进机会](https://web.dev/static/articles/optimize-lcp/image/a-network-waterfall-diagr-1ee19fc20ee1f.png?hl=zh-cn)

一般来说，有两个因素会影响 LCP 资源的加载速度：

- 资源被发现的时间。
- 资源被指定的优先级。

#### 优化资源被发现的时间

为了确保您的 LCP 资源尽早开始加载，请务必确保浏览器的[预加载扫描程序](https://web.dev/articles/preload-scanner?hl=zh-cn)可在初始 HTML 文档响应中检测到相应资源。例如，**在以下情况下，浏览器可以通过扫描 HTML 文档响应来发现 LCP 资源：**

- LCP 元素是一个 `<img>` 元素，其 `src` 或 `srcset` 属性存在于初始 HTML 标记中。
- LCP 元素要求使用 CSS 背景图片，但该图片是通过 HTML 标记中的 `<link rel="preload">`（或通过 `Link` 标头）预加载的。
- LCP 元素是一个文本节点，需要网页字体才能渲染，字体是通过 HTML 标记中的 `<link rel="preload">`（或通过 `Link` 标头）加载的。

下面列举了一些示例来说明如何**在扫描 HTML 文档中找不到 LCP 资源：**

- LCP 元素是通过 JavaScript 动态添加到网页的 `<img>`。
- LCP 元素使用会隐藏其 `src` 或 `srcset` 属性（通常为 `data-src` 或 `data-srcset`）的 JavaScript 库<u>延迟加载</u>。
- LCP 元素需要 CSS 背景图片。

在每种情况下，浏览器都需要先运行脚本或应用样式表（这通常需要等待网络请求完成），然后才能发现 LCP 资源并开始加载它。这从来都不是最佳选择。

为消除不必要的资源加载延迟，您的 LCP 资源应始终可从 HTML 源代码中发现。如果资源仅从外部 CSS 或 JavaScript 文件引用，则应预加载 LCP 资源，并具有较高的提取优先级（[下一部分将详细介绍提取优先级](https://web.dev/articles/optimize-lcp?hl=zh-cn#optimize_the_priority_the_resource_is_given)）；例如：

```html
<!-- Load the stylesheet that will reference the LCP image. -->
<link rel="stylesheet" href="/path/to/styles.css">

<!-- Preload the LCP image with a high fetchpriority so it starts loading with the stylesheet. -->
<link rel="preload" fetchpriority="high" as="image" href="/path/to/hero-image.webp" type="image/webp">
```

**警告** ：在大多数网页上，确保 LCP 资源与第一个资源同时开始加载就足够了，但请注意，这样构建的网页可能没有提前发现任何资源，并且所有资源开始加载的时间都比 TTFB 大得多。因此，虽然与第一个资源进行比较是发现改进机会的好方法，但在某些情况下，这可能是不够的，因此仍有必要衡量相对于 TTFB 的时间，并确保其较少。

#### 优化为资源指定的优先级

即使可从 HTML 标记中找到 LCP 资源，它也可能无法尽早与第一个资源一样，开始加载。如果浏览器预加载扫描程序的优先级启发式算法**未意识到相应资源很重要，或者认为其他资源更重要，就可能会发生这种情况**。<u>例如</u>，如果您在 `<img>` 元素上设置了 [`loading="lazy"`](https://web.dev/articles/browser-level-image-lazy-loading?hl=zh-cn)，则可以通过 HTML 延迟 LCP 图片。使用延迟加载意味着：在布局确认图片位于视口内之前，资源不会加载，因此开始加载的时间可能会比实际时间要晚。**警告** ：切勿延迟加载 LCP 图片，因为这样做总是会导致不必要的资源加载延迟，并对 LCP 产生负面影响。

**fetchpriority属性**

即使没有延迟加载，浏览器也不会最初以最高优先级加载图像，因为它们不是阻止渲染的资源。**您可以通过 [`fetchpriority`](https://web.dev/articles/fetch-priority?hl=zh-cn) 属性提示浏览器哪些资源最重要**，以表明资源能从较高的优先级中获益：

```html
<img fetchpriority="high" src="/path/to/hero-image.webp">
```

如果您认为某个 `<img>` 元素可能是网页的 LCP 元素，那么最好在该元素上设置 `fetchpriority="high"`，**但最好仅将其设为一到两张图片**，**否则将变得毫无意义**。**还可以降低文档中早期不可见的图片**（例如轮播幻灯片中在启动时不可见的图片）：

```html
<img fetchpriority="low" src="/path/to/carousel-slide-3.webp">
```

<u>降低某些资源的优先级可以腾出更多带宽提供给需要更多资源的资源</u>，但要小心。请始终在开发者工具中查看资源优先级，并使用实验室和现场工具测试更改。

优化 LCP 资源优先级和发现时间后，您的网络瀑布流应如下所示（LCP 资源与第一个资源同时开始）：

![显示 LCP 资源现在与第一个资源同时启动的网络瀑布图](https://web.dev/static/articles/optimize-lcp/image/a-network-waterfall-diagr-b6906b9fce22.png?hl=zh-cn)

**要点** ：另一个原因可能是您的 LCP 资源未尽早开始加载（即使可从 HTML 源代码中发现该资源），**原因是其托管在其他来源上**，<u>因为这些请求要求浏览器先连接到相应来源，然后才能开始加载资源</u>。如果可能，**最好将关键资源与 HTML 文档资源托管在同一源上，因为这些资源可以通过重复使用现有连接来节省时间**（稍后会详细介绍）。

### 2. 消除*元素渲染延迟*

**此步骤的目标**是确保 LCP 元素在其资源完成加载后可以立即渲染（无论何时）。

LCP 元素无法在其资源完成加载后立即渲染的主要原因是，**渲染因其他原因而[被阻塞](https://developer.chrome.com/docs/lighthouse/performance/render-blocking-resources/?hl=zh-cn)：**

- 由于 `<head>` 中的<u>样式表或同步脚本仍在加载</u>，整个网页的渲染都被阻塞。
- LCP 资源已加载完毕，<u>但 LCP 元素尚未添加到 DOM 中</u>（正在等待一些 JavaScript 代码加载完毕）。
- 此元素被其他一些代码（例如仍在确定用户应参与的实验的 A/B 测试库）<u>隐藏了</u>。
- 主线程因[耗时较长的任务](https://web.dev/articles/long-tasks-devtools?hl=zh-cn#what_are_long_tasks)而处于阻塞状态，并且<u>渲染工作需要等待这些耗时较长的任务完成</u>。

下面几部分将介绍如何解决导致不必要的元素渲染延迟的最常见原因。

#### 减少或内嵌阻止渲染的样式表

<u>从 HTML 标记加载的样式表会阻止渲染其后面的所有内容，这是好事，因为您通常不想渲染没有样式的 HTML</u>。不过，**如果样式表过大，以致于加载所需的时间远远超过 LCP 资源，则会阻止 LCP 元素渲染**，即使其资源加载完成后也是如此，如以下示例所示：

![网络瀑布图，显示了大型 CSS 文件阻止渲染 LCP 元素（因为加载用时比 LCP 资源长）](https://web.dev/static/articles/optimize-lcp/image/a-network-waterfall-diagr-42b740846d30d.png?hl=zh-cn)

**要解决此问题，您可以选择以下两种方式之一：**

- 将样式表内嵌到 HTML 中，以避免额外的网络请求；或者，
- 减小样式表的大小。

<u>一般来说，只有当样式表很小时，才建议内嵌样式表，因为 HTML 中的内嵌内容无法从后续网页加载的缓存中获益</u>。如果样式表过大，以致加载用时比 LCP 资源长，则不太可能适合进行内嵌。

在大多数情况下，确保样式表不会阻止渲染 LCP 元素的**最佳方法是缩减其大小，使其小于 LCP 资源**。这样可以确保它不会成为大多数访问的瓶颈。<u>下面是一些缩减样式表大小的建议：</u>

- [移除未使用的 CSS](https://developer.chrome.com/docs/lighthouse/performance/unused-css-rules/?hl=zh-cn)：使用 Chrome 开发者工具查找未在使用且可能会被移除（或延迟）的 CSS 规则。
- [延迟非关键 CSS](https://web.dev/articles/defer-non-critical-css?hl=zh-cn)：将样式表拆分为初始网页加载所需的样式，然后拆分为可延迟加载的样式。
- [减小和压缩 CSS ](https://web.dev/articles/reduce-network-payloads-using-text-compression?hl=zh-cn)：对于关键样式，确保尽可能缩减其[传输大小](https://developer.mozilla.org/docs/Web/API/PerformanceResourceTiming/transferSize)。

#### 延迟或内嵌阻止渲染的 JavaScript

**几乎从来没有必要将同步脚本（不含 `async` 或 `defer` 属性的脚本）添加到网页的 `<head>` 中，因为这样做几乎总是会对性能产生负面影响**。

**如果 JavaScript 代码需要在网页加载过程中尽早运行，最好内嵌该代码**，这样渲染就不会出现延迟。不过，<u>与样式表一样，您只应内嵌非常小的脚本</u>。

错误做法：

```html
<head>
  <script src="/path/to/main.js"></script>
</head>
```

正确做法：

```html
<head>
  <script>
    // Inline script contents directly in the HTML.
    // IMPORTANT: only do this for very small scripts.
  </script>
</head>
```

#### 使用服务器端渲染

[服务器端渲染](https://web.dev/articles/rendering-on-the-web?hl=zh-cn#server-side_rendering) (SSR) 是在服务器上运行客户端应用逻辑，并使用完整的 HTML 标记响应 HTML 文档请求的过程。

**从优化 LCP 的角度来看，SSR 有两大主要优势：**

- 您的图片资源可显示在 HTML 源代码中（如前面的[第 1 步](https://web.dev/articles/optimize-lcp?hl=zh-cn#1_eliminate_resource_load_delay)中所述）。
- 您的网页内容无需额外的 JavaScript 请求就能渲染。

**SSR 的主要缺点**是需要额外的服务器处理时间，这可能会降低 TTFB 的速度。不过，这种取舍通常是值得的，因为服务器处理时间由您控制，而用户的网络和设备功能则不然。

与 SSR 类似的选项称为 **静态网站生成 (static site generation-SSG) 或[预渲染](https://web.dev/articles/rendering-on-the-web?hl=zh-cn#terminology)。** 这是在构建步骤中（而非按需）生成 HTML 页面的过程。如果您的架构可以进行预渲染，则它通常更适合用于提升性能。

#### 拆分长任务

即使您遵循了上述建议，并且 JavaScript 代码既不会阻止渲染，也不负责渲染元素，它仍然会延迟 LCP。

**发生这种情况的最常见原因**是，网页加载了大型 JavaScript 文件，这些文件需要在浏览器的主线程上解析和执行。<u>这意味着，即使图片资源已完全下载，可能仍然需要等到不相关的脚本执行完毕后才能渲染</u>。

<u>目前，所有浏览器都会在主线程上渲染图片，这意味着，任何阻塞主线程的内容也可能会导致不必要的*元素渲染延迟*。</u>

### 3. 缩短*资源加载时间*

此步骤的目的是减少通过网络将资源的字节传输到用户设备所花费的时间。一般来说，您可以通过以下三种方法实现此目的：

- 缩减资源的大小。
- 缩短资源需要的行程距离。
- 减少网络带宽争用。
- 完全消除网络时间。

#### 缩减资源大小

网页的 LCP 资源（如果有）将会是图片或网页字体。以下指南详细介绍了如何缩减这两者的大小：

- [提供最佳大小的图片](https://developer.chrome.com/docs/lighthouse/performance/uses-responsive-images/?hl=zh-cn)
- [使用新型图片格式](https://developer.chrome.com/docs/lighthouse/performance/uses-webp-images/?hl=zh-cn)
- [压缩图片](https://developer.chrome.com/docs/lighthouse/performance/uses-optimized-images/?hl=zh-cn)
- [缩减网页字体大小](https://web.dev/articles/reduce-webfont-size?hl=zh-cn)

#### 缩短资源需要的行程距离

除了缩减资源的大小，您还可以使服务器在地理位置上尽可能靠近用户，从而缩短加载时间。要做到这一点，**最好的方法是使用[内容分发网络](https://web.dev/articles/content-delivery-networks?hl=zh-cn) (CDN)**。

事实上，[图片 CDN](https://web.dev/articles/image-cdns?hl=zh-cn) 是非常不错的选择，因为它们不仅能缩短资源需要行进的距离，而且总体来说还会缩减资源的大小 - 自动为您实现前面提到的所有缩减大小建议。

**要点** ：虽然图片 CDN 是缩短资源加载时间的好方法，但使用第三方网域托管图片会产生额外的连接费用。虽然预先连接到来源可以减少这部分费用，但最好的选择是从与 HTML 文档相同的来源投放图片。许多 CDN 都允许您将来自您的源站的请求代理到他们的源站，这是一个很好的选择（如果有）。

#### 减少网络带宽争用

如果您同时加载许多其他资源，那么即使您已经缩减了资源的大小并缩短了资源穿越的距离，也仍然需要很长时间才能加载资源。此问题称为*网络争用*。

如果您为 LCP 资源指定[高 `fetchpriority`](https://web.dev/articles/fetch-priority?hl=zh-cn) 并[开始尽快加载该资源](https://web.dev/articles/optimize-lcp?hl=zh-cn#1_eliminate_resource_load_delay)，则浏览器会尽最大努力阻止优先级较低的资源与其竞争。但是，如果您要加载许多资源并且 `fetchpriority` 较高，或者一般只需加载大量资源，则可能会影响 LCP 资源的加载速度。

#### 完全消除网络时间

缩短资源加载时间的最佳方法是从进程中完全消除网络。如果您通过[高效的缓存控制政策](https://developer.chrome.com/docs/lighthouse/performance/uses-long-cache-ttl/?hl=zh-cn)提供资源，那么第二次请求这些资源的访问者将从缓存中提供这些资源，这会使*资源加载时间*基本上为零！

如果您的 LCP 资源是一种网页字体，那么除了[缩减网页字体大小](https://web.dev/articles/reduce-webfont-size?hl=zh-cn)之外，您还应考虑是否需要在加载网页字体资源时阻止渲染。如果您将 [`font-display`](https://developer.mozilla.org/docs/Web/CSS/@font-face/font-display) 值设为 `auto` 或 `block` 以外的任何其他值，则文本在加载期间将始终可见，并且系统不会在收到额外的网络请求时屏蔽 LCP。[](https://developer.chrome.com/docs/lighthouse/performance/font-display/?hl=zh-cn)

最后，如果 LCP 资源较小，可以将资源内嵌为[数据网址](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/Data_URIs)，这样也可以消除额外的网络请求。但是，使用数据网址[需要注意](https://calendar.perfplanet.com/2018/performance-anti-patterns-base64-encoding/)，因为如果资源无法缓存，则在某些情况下，由于会产生额外的[解码成本](https://www.catchpoint.com/blog/data-uri)，导致渲染延迟时间延长。

### 4. 缩短*加载第一个字节所需时间*

此步骤的目的是尽快提供初始 HTML。之所以将此步骤列在最后，是因为此步骤通常是开发者最无法控制的步骤。但是，这也是最重要的步骤之一，因为它会直接影响其之后的每一个步骤。在后端传送第一个字节内容之前，前端不会发生任何情况，因此，为加快 TTFB 速度而采取的任何措施也会改善所有其他加载指标。

如需有关此主题的具体指导，请参阅[优化 TTFB](https://web.dev/articles/optimize-ttfb?hl=zh-cn)。

## 在 JavaScript 中监控 LCP 细分

您可以通过以下性能 API 组合使用 JavaScript 获取上述所有 LCP 子部分的时间信息：

- [Largest Contentful Paint API](https://w3c.github.io/largest-contentful-paint/)
- [Navigation Timing API](https://www.w3.org/TR/navigation-timing-2/)
- [Resource Timing API](https://www.w3.org/TR/resource-timing-2/)

使用 JavaScript 计算这些时间值的好处是，您可以将其发送给分析服务提供商，或将其记录到开发者工具中，以帮助进行调试和优化。

例如，以下屏幕截图使用 [User Timing API](https://w3c.github.io/user-timing/) 中的 `performance.measure()` 方法向 Chrome 开发者工具中的“Performance”面板的“Timings”轨道添加栏。

![用于衡量 Chrome 开发者工具中显示的 LCP 子部分的用户计时指标](https://web.dev/static/articles/optimize-lcp/image/user-timing-measures-the-6c944e15c221c.png?hl=zh-cn)

在与 Network 轨道和主线程轨道一起查看时，时间轨道中的可视化功能特别有用，因为您可以一目了然地查看在这些时间范围内网页上发生的其他事件。

除了在时间轨道中直观渲染 LCP 子部分之外，您还可以使用 JavaScript 计算每个子部分占总 LCP 时间的百分比。根据这些信息，您可以确定网页是否满足上文所述的[建议的百分比细分](https://web.dev/articles/optimize-lcp?hl=zh-cn#optimal_sub-part_times)。

此屏幕截图显示的示例记录了每个 LCP 子部分的总时间，及其在总 LCP 时间中占到控制台时间的百分比。

![输出到控制台的 LCP 子部分时间及其 LCP 百分比](https://web.dev/static/articles/optimize-lcp/image/the-lcp-sub-part-times-b589094e0db8.png?hl=zh-cn)

这两个可视化图表都是使用以下代码创建的：

```
const LCP_SUB_PARTS = [  'Time to first byte',  'Resource load delay',  'Resource load time',  'Element render delay',];new PerformanceObserver((list) => {  const lcpEntry = list.getEntries().at(-1);  const navEntry = performance.getEntriesByType('navigation')[0];  const lcpResEntry = performance    .getEntriesByType('resource')    .filter((e) => e.name === lcpEntry.url)[0];  // Ignore LCP entries that aren't images to reduce DevTools noise.  // Comment this line out if you want to include text entries.  if (!lcpEntry.url) return;  // Compute the start and end times of each LCP sub-part.  // WARNING! If your LCP resource is loaded cross-origin, make sure to add  // the `Timing-Allow-Origin` (TAO) header to get the most accurate results.  const ttfb = navEntry.responseStart;  const lcpRequestStart = Math.max(    ttfb,    // Prefer `requestStart` (if TOA is set), otherwise use `startTime`.    lcpResEntry ? lcpResEntry.requestStart || lcpResEntry.startTime : 0  );  const lcpResponseEnd = Math.max(    lcpRequestStart,    lcpResEntry ? lcpResEntry.responseEnd : 0  );  const lcpRenderTime = Math.max(    lcpResponseEnd,    // Use LCP startTime (which is the final LCP time) as sometimes    // slight differences between loadTime/renderTime and startTime    // due to rounding precision.    lcpEntry ? lcpEntry.startTime : 0  );  // Clear previous measures before making new ones.  // Note: due to a bug this does not work in Chrome DevTools.  LCP_SUB_PARTS.forEach((part) => performance.clearMeasures(part));  // Create measures for each LCP sub-part for easier  // visualization in the Chrome DevTools Performance panel.  const lcpSubPartMeasures = [    performance.measure(LCP_SUB_PARTS[0], {      start: 0,      end: ttfb,    }),    performance.measure(LCP_SUB_PARTS[1], {      start: ttfb,      end: lcpRequestStart,    }),    performance.measure(LCP_SUB_PARTS[2], {      start: lcpRequestStart,      end: lcpResponseEnd,    }),    performance.measure(LCP_SUB_PARTS[3], {      start: lcpResponseEnd,      end: lcpRenderTime,    }),  ];  // Log helpful debug information to the console.  console.log('LCP value: ', lcpRenderTime);  console.log('LCP element: ', lcpEntry.element, lcpEntry.url);  console.table(    lcpSubPartMeasures.map((measure) => ({      'LCP sub-part': measure.name,      'Time (ms)': measure.duration,      '% of LCP': `${        Math.round((1000 * measure.duration) / lcpRenderTime) / 10      }%`,    }))  );}).observe({type: 'largest-contentful-paint', buffered: true});
```

您可以按原样使用此代码进行本地调试，也可以对其进行修改以将这些数据发送给分析服务提供商，以便更好地了解您网页上对于真实用户的 LCP 细分情况。

**注意**：

上述代码适用于标准导航，但对于[预渲染的网页](https://developer.chrome.com/blog/prerender-pages/?hl=zh-cn)，您必须额外考虑，预渲染的网页应从启动开始时间开始计算，但为简单起见，我们未添加此代码。

[web-vitals 库](https://github.com/GoogleChrome/web-vitals)将这一细分数据纳入了归因构建流程，并考虑了以下注意事项。

对于希望实施自己的解决方案的用户，[相关代码是开源的](https://github.com/GoogleChrome/web-vitals/blob/main/src/attribution/onLCP.ts)，与上述代码类似，但开始时启用的像素会更高。

## 通过 Web Vitals 扩展程序监控 LCP 细分数据

[Web Vitals 扩展程序](https://chrome.google.com/webstore/detail/web-vitals/ahfhijdlegdabablpippeagghigmibma?hl=zh-cn)会[在控制台日志中记录 LCP 时间、LCP 元素以及这四个子部分](https://web.dev/articles/debug-cwvs-with-web-vitals-extension?hl=zh-cn#lcp_debug_information)，以便您轻松查看细分数据。

![Web Vitals 扩展程序的控制台日志记录的屏幕截图，其中显示了 LCP 子部分的时间](https://web.dev/static/articles/optimize-lcp/image/screenshot-the-console-l-3e74ce0646ab8.png?hl=zh-cn)

## 总结

LCP 很复杂，其时间可能会受多种因素影响。不过，如果您认为优化 LCP 主要是为了优化 LCP 资源的负载，那么它可以大大简化工作。

概括来讲，优化 LCP 可以总结为四个步骤：

1. 确保 LCP 资源尽早开始加载。
2. 确保 LCP 元素可在其资源完成加载后立即渲染。
3. 在不牺牲质量的情况下，尽可能缩短 LCP 资源的加载时间。
4. 尽快提供初始 HTML 文档。

如果您能够在网页上按照这些步骤进行操作，那么您应该确信自己向用户提供了最佳的加载体验，并且应该会在真实的 LCP 得分中反映出来。
