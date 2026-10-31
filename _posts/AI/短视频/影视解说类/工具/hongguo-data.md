>请详细介绍 GitHub 项目 github.com/wmefanit/hongguo-data ，包括项目的主要用途和目标、核心功能与技术架构、目录结构及关键模块的职责、数据来源与处理流程、依赖项和运行方式，以及适用场景与使用注意事项。

## 一句话定位

`wmefanit/hongguo-data` 是***红果短剧（hongguoduanju.com）全库元数据的抽取仓库**：每天凌晨自动获取全站 60 万部剧的索引，打包成“全量快照 + 当日增量补丁”*，通过 GitHub Releases 对外分发。它**本身不是爬虫服务，也不是数据 API**，而是一条由 CI 驱动的 ETL 流水线 + 静态数据集。

| 硬指标     | 实测值（2026-10-27，最新 release）                                                           |
| ------- | ------------------------------------------------------------------------------------ |
| 建库 / 语言 | 2026-09-30，JavaScript 100%，**零第三方 npm 依赖**                                           |
| 热度      | 7 star / 2 fork / 22 个 Release / 1 位贡献者                                              |
| 数据规模    | `catalog_total` = **603,474 部**（sitemap 唯一 ID 603,594）                               |
| 产物体积    | 全量 `catalog.json.gz` **86.6 MB**（明文 235 MB）、`delta` 2.06 MB、`source_manifest` 6.0 MB |
| 当日变化量   | +10,749 / ~2,902 / −7,411（新增/修改/下架）                                                  |

## 核心功能与技术架构

整条链路的核心思想是 **“sitemap 即事实来源，逐条归因，宁可暂缺，绝不编造”**：

<svg viewBox="0 0 680 216" width="100%" role="img" xmlns="http://www.w3.org/2000/svg">
<title>hongguo-data 增量构建流水线</title>
<desc>上游 Sitemap 经规划、分片抓取、合并三个阶段，产出全量与增量索引并发布到 GitHub Releases。</desc>
<defs>
<marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
<path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</marker>
</defs>
<g>
<rect x="40" y="40" width="132" height="56" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
<text x="106" y="60" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="14" font-weight="500" fill="#04342C">上游 Sitemap</text>
<text x="106" y="79" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="12" fill="#0F6E56">28 片 · 60 万部</text>
</g>
<path d="M172 68L196 68" fill="none" stroke="#5F5E5A" stroke-width="1.5" marker-end="url(#arrow)"/>
<g>
<rect x="196" y="40" width="132" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
<text x="262" y="60" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="14" font-weight="500" fill="#0C447C">plan_run</text>
<text x="262" y="79" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="12" fill="#185FA5">增量比对 lastmod</text>
</g>
<path d="M328 68L352 68" fill="none" stroke="#5F5E5A" stroke-width="1.5" marker-end="url(#arrow)"/>
<g>
<rect x="352" y="40" width="132" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
<text x="418" y="60" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="14" font-weight="500" fill="#0C447C">build_shard</text>
<text x="418" y="79" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="12" fill="#185FA5">112 job 并行抓取</text>
</g>
<path d="M484 68L508 68" fill="none" stroke="#5F5E5A" stroke-width="1.5" marker-end="url(#arrow)"/>
<g>
<rect x="508" y="40" width="132" height="56" rx="8" fill="#E6F1FB" stroke="#185FA5" stroke-width="0.5"/>
<text x="574" y="60" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="14" font-weight="500" fill="#0C447C">merge_catalog</text>
<text x="574" y="79" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="12" fill="#185FA5">全量 + Delta + Hash</text>
</g>
<path d="M574 96L574 116L340 116L340 140" fill="none" stroke="#5F5E5A" stroke-width="1.5" marker-end="url(#arrow)"/>
<g>
<rect x="40" y="140" width="600" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.5"/>
<text x="340" y="160" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="14" font-weight="500" fill="#2C2C2A">GitHub Releases · full-YYYYMMDD</text>
<text x="340" y="179" text-anchor="middle" dominant-baseline="central" font-family="system-ui" font-size="12" fill="#5F5E5A">catalog.json.gz 86.6 MB · catalog.delta.json.gz 2.0 MB · source_manifest.json.gz 6 MB</text>
</g>
</svg>

上图四个环节中，`plan_run` 先读取 `sitemap/hongguoduanju/index.xml`，**动态解析出分片号**（当前 28 片，不硬编码），逐片下载后得到 `(id, lastmod)` 全集，再与上一版 `source_manifest.json.gz` 比对——如果 `source_hash` 完全一致，就直接判定 `unchanged=true`，整轮**不抓取任何记录**。有变化时，它按 ID 的 FNV-1a 哈希把任务稳定地分配到 4 个 part，输出 `plan.json` + 每片每 part 的 `plan_<shard>_<part>.json`。

`build_shard` 只抓计划内的 ID。并发上限为 10，每个请求之间间隔 80ms，具备**断点恢复**（重启后读取已有产物并跳过已完成项）、**失败集中重试 2 轮**、**滑动窗口熔断器**（最近 20 次失败率≥50% 就全局冷却，15s 起倍增到 120s），还自带 `--max-runtime` 截止时间；**到达时限后写入磁盘并结束**，避免 CI 超时杀进程丢整片数据。

`merge_catalog` 是质量把关环节：以 sitemap 为唯一事实来源重建全量，要求**每个计划 ID 恰好有一个归属结果**（抓到 / 404-410 下架 / invalid）；`invalid` 且在基线里存在的会保留旧卡，等下一轮强制重抓；全局异常缺口超过 **0.5%** 就拒绝发布。

技术栈非常轻量：纯 Node.js（≥18，CI 用 20）内置模块 `fs/path/zlib/crypto/stream` + 原生 `fetch`，没有 Express、没有数据库、没有 ORM。**状态全部保存在 Git 仓库外的产物文件里**（上一版 Release 就是“数据库”）。

## 目录结构与模块职责

| 路径                                       | 行数    | 职责                                                                                                                                                                                 |
| ---------------------------------------- | ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `.github/workflows/build-all-shards.yml` | 255   | 负责编排：cron `0 19 * * *`（北京时间 03:00）、周日自动转全量审计、矩阵 `shard × part` = 28×4 = **112 个 job**、`concurrency: build-full-catalog` 串行防止自相覆盖、merge 用 `!cancelled()`（依赖失败仍合并，被取消即中止）、发布 Release |
| `lib/catalog_util.js`                    | 239   | 纯函数库：sitemap 与卡片解析、封面域名归一化（`p*-novel.byteimg.com` → `p3`）、雪花 ID → 日期回退、`canonicalCatalog` 规范化排序（`new_at` 降序 + `id` 升序）、`sha256`、`diffCatalogs` / `applyDeltaToCatalog`、manifest 校验 |
| `lib/http.mjs`                           | 107   | 统一容错层。**反转白名单**：404/410 = 终态不重试，2xx 交给语义校验，**其余一切**（444、403 挑战、429、5xx、超时、连接重置）一律指数退避重试——反爬 WAF 换状态码也不影响行为                                                                         |
| `scripts/plan_run.mjs`                   | 180   | 规划器，产出 `plan.json` + 分片任务文件；索引拉不到就 **fail-fast**，绝不回退写死分片数                                                                                                                         |
| `scripts/build_shard.mjs`                | 234   | 单分片单 part 抓取器，产出 `shard_X.partY.json` + `_report.json`（含 `goneIds` / `invalidIds` / 缺口统计）                                                                                          |
| `scripts/merge_catalog.mjs`              | 255   | 合并 + Delta 生成 + Level-9 gzip + 全局阈值把关                                                                                                                                              |
| `scripts/test_incremental_e2e.mjs`       | 360   | 本地 Mock 上游 + Mock Release 的端到端测试，11 项断言（规划精确到 50 条、零冗余请求、Delta 增删改精准、Delta 损坏降级全量、网络全挂时**零坏账**）                                                                                    |
| `README.md` / `AGENTS.md`                | 42/40 | 前者面向数据使用者，后者是给 AI 编码代理看的架构说明                                                                                                                                                       |

## 数据来源与处理流程

**来源**：官网 sitemap（仅用于发现 ID 和 `lastmod`）+ 播放页的 Next.js loader 端点 `GET /player/<id>?__loader=player_(series_id)/page&__ssrDirect=true`（SSR 直出，返回带 `data:` 前缀的 JSON，从 `seriesDetail` 取字段）。请求伪装成 Chrome 124 桌面 UA 并带 Referer。

**输出字段**（schema=2）：`id`（雪花 ID 字符串）、`title`、`cover`、`intro`（去空白后截前 50 字）、`tags`（≤6 个）、`eps`（总集数）、`ch`、`new_at`（优先 `first_visible_time`，缺失则用 ID 高 32 位反推日期）。

**每日流水**：拿上一版 Release 当基线 → 规划 → 112 job 并行抓 → 合并 → 写新 Release（`full-YYYYMMDD`，若同名先 delete 再 create）→ 下游只需拉 2 MB 的 delta 就能从昨天同步到今天。

## 依赖项与运行方式

零依赖，clone 下来直接跑（Node 20+）：

```bash
git clone https://github.com/wmefanit/hongguo-data.git && cd hongguo-data
node scripts/plan_run.mjs --out-dir ./plan --baseline-manifest ./baseline/source_manifest.json.gz --parts 4
node scripts/build_shard.mjs --shard 1 --part 0 --plan-file ./plan/plan_1_0.json --out-dir ./shards --concurrency 6
node scripts/merge_catalog.mjs --plan-file ./plan/plan.json --in-dir ./shards --out-dir ./dist --baseline-catalog ./baseline/catalog.json.gz
```

只作为数据使用者时则更简单，一条命令即可：

```bash
curl -LO https://github.com/wmefanit/hongguo-data/releases/latest/download/catalog.delta.json.gz
```

## 适用场景

- **短剧站/搜索/合集页的基础索引库**：直接使用全量或增量进入 SQLite/PG/ES，无需自己写爬虫。
- **竞品全量盘点**：60 万部的题材标签、集数、上新日期，能算出题材分布、日更节奏、下架率。
- **增量监控告警**：每日 delta 就是一份现成的“上新/下架/改简介”变更流，适合做选题或榜单初筛。
- **工程参考**：这是国内少见的、把“增量 ETL + 断点续传 + 熔断 + 严格归因”写全的小体量实现，比读一堆框架源码更直接。

## 使用注意事项（重点看这几条）

1. **没有热度、播放量、评论**。只有静态元数据。哥哥你在做的短剧热度榜调研，这个仓库**提供的是“剧库底座”，不是“热度数据”**——别指望用它算榜。
2. **端到端测试目前跑不通**。`test_incremental_e2e.mjs` 第 273、304 行硬编码了作者本机路径 `/mnt/data/code/namehere/hongguo-web/...`，第 7 段依赖下游 `catalog_db.js` 与 `build_sqlite_catalog.mjs`，这两个文件**都不在本仓库**。前 6 项（plan/shard/merge）可以跑，第 7 项必然失败。
3. **README 里的数字已经滞后**：写的是“57.5 万部 / ~35 MB / ~80 MB”，实际是“60.3 万部 / 86.6 MB 压缩 / 235 MB 明文”。以 `summary.json` 为准。
4. **消费端要自己写**。README 提到的 `build_sqlite_catalog.mjs` 不在仓库内，Delta 应用逻辑（`applyDeltaToCatalog`）虽然 `lib/` 里有，但落库那层要你自己实现。
5. **仓库没有 LICENSE 文件**（`package.json` 写了 MIT，GitHub 识别不到）。商用前建议找作者确认，尤其你如果要做产品。
6. **对上游强依赖且带反爬对抗**：接口是非公开的 loader 端点，官方改版即断；代码里的 UA 伪装、熔断、退避都说明该站有 WAF。自己跑时别把 `--concurrency` 调到 10 以上。
7. **`summary.json` 里 `published` 恒为 `false`**（源码写死），别拿它判断发布状态。
8. **同名 tag 每日 delete→create**，历史快照不保留（22 个 Release 就是 22 天）。要长期留痕得自己存 hash 或镜像产物。
9. **“不缺失但可能陈旧”**：变更项抓取失败时会保留旧卡，所以单看全量文件发现不了数据过期——判断新鲜度必须结合 `delta.summary.json` 和 `source_manifest` 里的 `invalid` 状态。

顺带一提：你工作区的 `红果短剧数据获取可行性方案.md` 里还没收录这个仓库，它恰好能补上“全量剧库从哪来”这一环，需要我合并进去的话说一声。