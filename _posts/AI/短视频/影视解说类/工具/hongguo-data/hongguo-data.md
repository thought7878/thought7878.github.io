
**这个项目必须分两步走：**
1. 先从 sitemap 拿 ID 全表，
2. 再逐个去请求 `/player/<id>` 并带上 `__ssrDirect=true`，才能拿到`seriesDetail` 字段。

