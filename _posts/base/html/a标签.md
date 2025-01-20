
# rel 属性

1. **定义和基本用途**
   - 在 HTML 中，`<a>`标签的`rel`属性（relationship 的缩写）用于定义*当前文档与被链接文档之间的关系*。它主要是为搜索引擎和浏览器等用户代理*提供额外的语义信息*，帮助它们更好地理解链接的性质。
   - 例如，当你有一个指向外部网站的链接时，使用`rel`属性可以表明这个链接是外部引用的关系。
2. **常见的值及含义**
   - **`noopener`和`noreferrer`**
     - `noopener`：当在一个新窗口或标签页中打开链接（通过`target="_blank"`）时，使用`noopener`可以*防止新打开的页面通过`window.opener`脚本访问原始页面的`window`对象*。**这是一个安全特性，可以避免恶意网站利用打开的新窗口对原始页面进行攻击**。
	     - 例如：`<a href="https://example.com" target="_blank" rel="noopener">外部链接</a>`。
     - `noreferrer`：它的功能比`noopener`更强大。*除了具有`noopener`的安全功能外，还会隐藏引用信息，即当用户点击链接时，在目标页面的请求头中不会包含`Referer`信息*。这对于隐私保护很有用，因为`Referer`信息可能包含用户之前访问页面的敏感信息。
	     - 例如：`<a href="https://example.com" target="_blank" rel="noreferrer">外部隐私链接</a>`。
   - **`nofollow`**
     - 这个值主要用于告诉搜索引擎不要追踪这个链接，也不要将链接权重传递给被链接的页面。通常用于用户生成内容（如博客评论中的链接）或者不信任的链接。这样可以防止搜索引擎认为你的网站在为低质量或垃圾网站做推广。
     - 例如：`<a href="https://spam - site.com" rel="nofollow">可能是垃圾链接</a>`。
   - **`stylesheet`和`alternate stylesheet`**
     - `stylesheet`：用于链接外部样式表。当`<a>`标签被用于加载 CSS 样式表文件时（通常在`<head>`标签内），`rel="stylesheet"`告诉浏览器这是一个样式表链接。不过，在实际应用中，更常见的是使用`<link>`标签来加载样式表，并且使用`rel="stylesheet"`属性。例如：`<link rel="stylesheet" href="styles.css">`。
     - `alternate stylesheet`：用于指定一个可选的样式表。这允许用户在不同的样式之间进行切换。比如，一个网站可能有一个默认样式和一个高对比度样式，就可以使用`alternate stylesheet`来提供切换功能。不过，浏览器对这种方式的支持有限。
   - **`prev`和`next`**
     - 这两个值用于表示文档之间的顺序关系，主要用于分页内容。`rel="prev"`表示当前页面的上一页，`rel="next"`表示当前页面的下一页。这有助于搜索引擎理解一组分页内容的顺序，也可以在一些支持的浏览器中用于提供更好的导航体验。
     - 例如，在一个博客的分页中：
       - `<a href="page1.html" rel="prev">上一页</a>`
       - `<a href="page3.html" rel="next">下一页</a>`
3. **对搜索引擎优化（SEO）的影响**
   - 正确使用`rel`属性可以对 SEO 产生积极影响。如使用`nofollow`可以控制链接权重的传递，避免将权重传递给低质量的网站，从而维护自己网站的搜索引擎排名。而使用`prev`和`next`等属性可以帮助搜索引擎更好地理解网站内容的结构和顺序，提高网站在搜索结果中的展示效果。
4. **浏览器支持情况**
   - 所有主流浏览器都支持`rel`属性的基本功能，如`noopener`、`noreferrer`和`nofollow`。但是对于一些比较复杂的功能，如`alternate stylesheet`的完全支持可能会因浏览器而异。例如，在一些旧版本的浏览器中，可能无法很好地实现样式表切换的功能。
