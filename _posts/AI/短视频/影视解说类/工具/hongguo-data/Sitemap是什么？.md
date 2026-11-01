`Sitemap` 是**网站主动交给爬虫的「站点地图」，一个 XML 清单，列出站内有哪内容页、各自上次何时改动**。红果这站有 57.5 万部剧，*一个清单装不下，所以拆成「总索引和多分片」两层*——这正是这个项目能做的根本原因。

## 总体结构
具体结构长这样：

<svg width="100%" viewBox="0 0 680 412" role="img" xmlns="http://www.w3.org/2000/svg">
<title>Sitemap 的两层结构</title>
<desc>总索引 index.xml 列出 28 个分片，每个分片内是若干 url 条目，每条包含 loc 与 lastmod。</desc>
<defs>
<marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
<path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</marker>
</defs>

<rect x="240" y="40" width="200" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
<text x="340" y="60" text-anchor="middle" dominant-baseline="central" font-family="system-ui,sans-serif" font-size="14" font-weight="500" fill="#0C447C">index.xml</text>
<text x="340" y="78" text-anchor="middle" dominant-baseline="central" font-family="system-ui,sans-serif" font-size="12" fill="#185FA5">总索引 · 只列分片地址</text>

<line x1="340" y1="96" x2="340" y2="130" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<rect x="40" y="136" width="600" height="100" rx="20" fill="#E6F1FB" stroke="#85B7EB" stroke-width="0.5"/>
<text x="60" y="156" dominant-baseline="central" font-family="system-ui,sans-serif" font-size="12" fill="#185FA5">站点地图分片 · 由索引动态发现，当前 28 个</text>

<rect x="60" y="170" width="128" height="44" rx="8" fill="#FFFFFF" stroke="#85B7EB" stroke-width="0.5"/>
<text x="124" y="192" text-anchor="middle" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#0C447C">index1.xml</text>

<rect x="204" y="170" width="128" height="44" rx="8" fill="#FFFFFF" stroke="#85B7EB" stroke-width="0.5"/>
<text x="268" y="192" text-anchor="middle" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#0C447C">index2.xml</text>

<rect x="348" y="170" width="128" height="44" rx="8" fill="#FFFFFF" stroke="#85B7EB" stroke-width="0.5"/>
<text x="412" y="192" text-anchor="middle" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#0C447C">index3.xml</text>

<rect x="492" y="170" width="128" height="44" rx="8" fill="#FFFFFF" stroke="#85B7EB" stroke-width="0.5" stroke-dasharray="3 3"/>
<text x="556" y="192" text-anchor="middle" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#0C447C">index28.xml</text>

<line x1="340" y1="236" x2="340" y2="274" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<text x="170" y="266" dominant-baseline="central" font-family="system-ui,sans-serif" font-size="12" fill="#185FA5">每个分片里是若干条目</text>

<rect x="170" y="280" width="340" height="112" rx="8" fill="#F1EFE8" stroke="#B4B2A9" stroke-width="0.5"/>
<text x="190" y="300" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#2C2C2A">&lt;url&gt;</text>
<text x="190" y="320" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#2C2C2A">  &lt;loc&gt;.../player/7601...&lt;/loc&gt;</text>
<text x="190" y="340" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#A32D2D">  &lt;lastmod&gt;2026-10-02&lt;/lastmod&gt;</text>
<text x="190" y="360" dominant-baseline="central" font-family="ui-monospace,monospace" font-size="12" fill="#2C2C2A">&lt;/url&gt;</text>
<text x="190" y="378" dominant-baseline="central" font-family="system-ui,sans-serif" font-size="11" fill="#5F5E5A">loc 给出页面地址，lastmod 给出最后修改时间</text>
</svg>

```
<url>
	<loc>https://hongguoduanju.com/detail?series_id=7592897248476793918</loc>
	<lastmod>2026-07-07T16:03:19+08:00</lastmod>
</url>
```

**Sitemap 是站点自己公开的页面清单**，遵循 sitemaps.org 协议，一个纯 XML 文件，用来*告诉搜索引擎“我这儿有哪些页面、各自最后什么时候改过”*。这个站点把它放在 https://hongguoduanju.com/sitemap/hongguoduanju/index.xml 。

## 三层含义

1. **只有索引，没有内容。** Sitemap 只告诉你“有个 `/player/7601...` 这个页面”，剧名、封面、集数一概没有。所以*这个项目必须分两步走：*
	1. 先从 sitemap 拿 ID 全表，
	2. 再逐个去请求 `/player/<id>` 并带上 `__ssrDirect=true`，才能拿到`seriesDetail` 字段。

2. **为什么要分片。** 协议给单个 sitemap 的上限是 5 万条 URL / 50 MB（未压缩），57.5 万部必须拆分。拆完后入口变成「sitemap index」，也就是总索引：它只列 `<loc>`，指向 `index1.xml`、`index2.xml`…，最多再带个 `lastmod`。这就是 `parseSitemapIndex()` 读取的那个文件。

3. **`lastmod` 才是这个项目的核心。** 整条增量链路完全建立在它上面：
   - `plan_run.mjs` 把今天的 `(id, lastmod)` 全表与上一版 `source_manifest` 逐条比对；
   - `lastmod` 没变 → 复用基线，`fetch_count` 不增加；
   - 变了 / 新出现 / 上次状态是 `invalid` → 才进抓取队列；
   - 全表 `source_hash` 一致 → `unchanged=true`，当天直接零抓取收工。

   **换句话说，这个项目并没有把 Sitemap 当作需要抓取或解析的网页，它只把 Sitemap 当成一份“全站 ID 加版本戳”的权威清单。**

---

另外有两条和 Sitemap 特性直接相关的隐患，跟我上一轮说的问题同源，哥哥需要知道：

- **`lastmod` 是站点自己填的，它不保证准确。** 如果上游改了内容却没有更新 `lastmod`，增量会永远漏掉这条记录，所以周日必须跑全量审计，这是唯一补救手段，不能跳过。
- **Sitemap 分片只是“本次返回了这些”。** 上游某片临时少返回几千条时，这些 ID 会直接从本次计划里消失，既不算 `gone` 也不算 `invalid`，会静默地从 catalog 里被删掉并发布。这就是我上一轮提的“Sitemap 缩水无校验”，建议加一道总量环比熔断。

要动手的话，我可以先写个只读的 `robots.txt` + 实际 sitemap 探查脚本，把当前真实分片数、每片条目数、`lastmod` 分布拉出来看一眼，再决定阈值定多少。