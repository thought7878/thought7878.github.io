# 1
>请全面介绍这个项目，包括其整体目标与用途、核心功能模块、技术架构与所用技术栈、目录结构与关键文件说明、主要业务流程、配置与运行方式，以及依赖关系和可扩展点，并指出需要注意的限制或潜在问题。


## 一句话结论

`hongguo-data` 是一个零依赖的 Node.js 数据管道：*从红果短剧官网 Sitemap 动态发现全部分片，只抓取有变动的剧集，合并成一份带 SHA256 校验的全量索引 + 增量补丁，通过 GitHub Releases 每天凌晨自动发布，供下游（`hongguo-web` 的 SQLite 同步脚本）消费*。

---

## 1. 整体目标与用途

- **目标**：把 hongguoduanju.com 的全站短剧元数据（约 57.5 万部）维护成一份**可增量更新、可校验、可分发**的公共数据集。
- **用途**：作为下游 Web 服务/App 的离线索引底座。下游不直接爬官网，只吃 Releases 里的 `catalog.delta.json.gz`（1–3 MB）或 `catalog.json.gz`（~35 MB gz / ~80 MB 明文）。
- **核心取舍**：不追求"每次全量重抓"，而是用 `lastmod` 做精确差分 —— 正常日更只抓几十到几百条，周日做一次全量审计修正偏差。

## 2. 技术栈与架构

| 维度 | 选型 |
|---|---|
| 运行时 | Node.js ≥ 18（用原生 `fetch`、`AbortSignal.timeout`），CI 固定 Node 20 |
| 模块体系 | 纯 ESM（`"type": "module"`） |
| 第三方依赖 | **零**。只用 `node:fs / path / zlib / crypto / stream / http / child_process` |
| 压缩 | `zlib` Level-9 Gzip，流式 `pipeline` 写盘 |
| 校验 | SHA256（`catalog_hash` 内容哈希 + `source_hash` 状态哈希） |
| CI/CD | GitHub Actions：`cron 0 19 * * *`（UTC）= 北京时间 03:00 |
| 分发 | GitHub Releases（`full-YYYYMMDD` tag，`--latest`） |
| 并行模型 | GHA matrix（分片 × part）+ 进程内 worker pool + 游标抢占 |

**流水线**：

```
Sitemap 总索引 index.xml ──动态发现──> [28 个分片] index1..indexN.xml
        │
   plan_run.mjs ── 比对 baseline source_manifest 的 (id,lastmod,status)
        │            ├─ unchanged=true → 直接终止，0 抓取
        │            └─ 否则生成 plan.json + 112 个 plan_<shard>_<part>.json
        ▼
   build_shard.mjs × 112 (matrix) ── 只抓 plan 内 ID，走 SSR loader 接口
        │            产物：shard_S.partP.json + _report.json（含 goneIds/invalidIds）
        ▼
   merge_catalog.mjs ── 基线复用 + 严格归因 + Delta 差集 + 全局缺口阈值
        ▼
   catalog.json.gz / catalog.delta.json.gz / source_manifest.json.gz / summary.json
        ▼
   gh release create（同日重复则先 delete --cleanup-tag）
```

## 3. 目录与关键文件

| 文件 | 行数 | 职责 |
|---|---|---|
| `lib/catalog_util.js` | 239 | **数据契约中心**。`CATALOG_SCHEMA=2`；卡片规范化 `normalizeCard`（intro 截 50 字、tags 截 6 个）；封面 CDN 域名归一（`pN-novel` → `p3-novel`）；雪花 ID 反解日期 `idToDate`；Sitemap 解析；FNV-1a 稳定分桶 `partForId`；规范化 + SHA256（`catalogHash`/`sourceHash`）；`diffCatalogs` / `applyDeltaToCatalog`（对 delta 做严格重复/非法校验） |
| `lib/http.mjs` | 107 | **容错层**。反转白名单分类：404/410 → `gone` 终态；2xx → ok；**其余一切可重试**（444/403/429/5xx/超时/DNS）；指数退避 + 抖动、`retry-after` 尊重；全局熔断器 `createBreaker`（滑动窗口失败率 ≥50% → 全体冷却 15s 起，翻倍至 120s） |
| `scripts/plan_run.mjs` | 180 | 规划器。读总索引拿分片列表（拉不到就 fail-fast，**绝不回退写死分片数**）；拉全部 sitemap；跨分片重复 ID 取最大 `lastmod`；输出 plan + 每 part 子计划 + `unchanged` 短路标志 |
| `scripts/build_shard.mjs` | 234 | 抓取器。并发 ≤10、默认 6；`--delay 80ms`；`--max-runtime 40` 自管 deadline（到点落盘交差，不赌 CI 超时）；断点恢复（只认本 plan 内的 ID）；语义级可重试分类 `bad_payload / loader_fail / no_seriesDetail`；失败项集中重试 2 轮（并发降到 2）；每 200 条或 30s 落盘 |
| `scripts/merge_catalog.mjs` | 255 | 合并器。以**当前 sitemap 为唯一真相**组装全量；四类归因：新抓 / 基线复用 / 失败暂留旧卡 / 下架删除；全局缺口 >0.5% 直接拒绝发布；产出全量、Delta、Source Manifest、Summary |
| `scripts/test_incremental_e2e.mjs` | 360 | 端到端测试（1000 条基线 → 删10/改20/增30；Mock 上游 + Mock Release；含"delta 损坏回退全量""网络全挂零坏账"两组容错断言） |
| `.github/workflows/build-all-shards.yml` | 256 | `plan → shard(matrix) → merge → release` 四段式编排 |

## 4. 主要业务流程的关键设计

- **Sitemap 是唯一真相源**：分片数、条目存在性都来自上游索引。找不到就报错，不猜。
- **`source_manifest` 是状态真相表**：每个 ID 记 `[id, lastmod, status]`，`status ∈ {ok, gone, invalid}`。`invalid` 是"下一轮必须重抓"的标记，这是增量正确性的锚点。
- **三层容错分工**（这块设计是精心想过的）：
  - `build_shard`：局部缺口只告警，只有"一条都没抓到且非超时"才 exit 1；
  - `merge`：单分片产物缺失降级为 `invalid`，不阻断其余 100+ 分片；
  - 全局：`gapCount / sitemapTotal > 0.5%` 才拒绝发布。注释里明确写了教训——局部 8% 阈值曾把 19.7% 缺口的分片判死，导致 99 个成功分片全部无法发布。
- **确定性**：`canonicalCatalog` 统一排序（`new_at` 降序 → `id`），`normalizeCard` 固定键序，保证同样数据 → 同样字节 → 同样 hash。这是 Delta 和下游校验的前提。
- **CI 状态机细节**：`if: ${{ !cancelled() && needs.plan.result == 'success' && unchanged != 'true' }}` —— 用 `!cancelled()` 而非 `always()`，堵住"手动取消后 merge 照样发布残缺包"的漏洞。这条在 commit 里是修过的。

## 5. 配置与运行

```bash
# 1) 规划（本地可用 --sitemap-dir 走离线 XML）
node scripts/plan_run.mjs --out-dir ./plan \
  --baseline-manifest ./baseline/source_manifest.json.gz --parts 4 --mode incremental

# 2) 抓取单个 (shard, part)
node scripts/build_shard.mjs --shard 1 --part 0 \
  --plan-file ./plan/plan_1_0.json --out-dir ./shards \
  --concurrency 6 --delay 80 --max-runtime 40

# 3) 合并发布包
node scripts/merge_catalog.mjs --plan-file ./plan/plan.json \
  --in-dir ./shards --out-dir ./dist --baseline-catalog ./baseline/catalog.json.gz
```

GHA 手动触发参数：`mode`(incremental/full)、`concurrency`、`limit`（实验用，限每 part 抓 N 条）、`publish`。`mode=full` 或**每周日**自动升格为全量审计，但仍携带 baseline 以保证产出 Delta（否则下游退化为 80 MB 全量下载）。

## 6. 可扩展点

1. **加字段**：`normalizeCard` + `parseCard` 两处同改，`CATALOG_SCHEMA` 升版即可（下游按 schema 判兼容）。
2. **加数据源**：`plan_run` 的 `loadSitemaps` 与 `build_shard` 的 `parseCard` 是仅有的两处站点耦合，加个 adapter 层就能接第二个站。
3. **改分片粒度**：`--parts` 与 workflow matrix 的 `part: [0,1,2,3]` 目前需人工同步，可改成由 plan 输出 `parts` 再 `fromJSON`。
4. **多版本增量链**：`diffCatalogs` 已具备能力，可改为按 `from_tag → to_tag` 生成链式补丁，避免下游落后两版只能下全量。
5. **指标输出**：`summary.json` 里加 `gone_count`、`retained_stale_count`、`breaker_trips`，就能接监控告警。

## 7. 需要注意的限制与潜在问题

**高风险（建议尽快处理）**

1. **`gone` 没有比例守卫。** `merge_catalog.mjs` 只对 `invalid` 缺口设了 0.5% 阈值，对 `gone`（404/410 直接删卡）零限制。若上游 WAF 或 CDN 抖动对全站返回 404，`lib/http.mjs` 会把它判成"内容确实不存在"的终态，**整库被清空仍能通过阈值正常发布**。这是我最担心的一条。
2. **Sitemap 缩水无校验。** 若某个分片临时只返回部分条目，这些 ID 会从 `sitemap_entries` 消失 → 直接不进最终 catalog → 静默发布。缺少"总量环比骤降"熔断。
3. **`README.md` 的命令与代码不符。** 里面写的 `--baseline`、`--parts`、`--full`（`build_shard`）和 `--from-tag`（`merge_catalog`）在代码里都不存在，照抄会直接报错。`AGENTS.md` 的命令是对的，README 是旧的。

**中风险**

4. **端到端测试在本机跑不起来。** `test_incremental_e2e.mjs` 硬编码 `/mnt/data/code/namehere/hongguo-data/scripts/...` 和 `/mnt/data/code/namehere/hongguo-web/lib/catalog_db.js`（另一台开发机的路径）。我实测直接 `MODULE_NOT_FOUND`。且它依赖 `hongguo-web` 的 `build_sqlite_catalog.mjs`，跨仓库耦合，而当前 `hongguo-web` 里已没有该文件。
5. **`invalid` 的旧卡会长期陈旧。** 变更项抓取失败时保留基线旧卡，若某条持续失败，catalog 里会长期挂过期元数据，且没有任何出口指标。
6. **`lastmod` 失真即漏更。** 站点若不改 `lastmod` 而改内容，增量永远抓不到 —— 这正是周日全量审计存在的原因，属已知补偿，但要确保审计不被跳过。另外 `unchanged=true` 当天完全不出 Release，下游这天拿不到"无变化"的信号。
7. **`parts=4` 双份硬编码**（plan 的 `--parts` 与 workflow matrix），改一处忘另一处会造成产物与归因错位。
8. **资源边界。** plan job 串行拉 28 个 sitemap + 三次全量排序 57.5 万条（`normalizeManifestEntries` 在 `plan_run` 里被调了 3 次，可优化为 1 次），20 分钟超时不算宽裕；merge 全量驻留内存，实测需 ≥1 GB。112 个 matrix job 也会受 GitHub 免费额度并发（20）限制排队。
9. **合规风险。** UA 伪装成 Chrome、112 job × 6 并发对单站施压、无 `robots.txt` 校验。这是数据获取链路上最需要哥哥自己拍板的部分。

**低风险**

10. `ch` 字段恒为 `''`，是遗留占位；`intro` 截 50 字、`tags` 截 6 个都是有损压缩，下游无法还原。
11. 落盘用 `writeFileSync` 直覆盖，非原子写；断电可能留下半截文件（好在 merge 有 hash 自检兜底）。
12. 仓库无 lint / 无类型 / 无单测框架，只有脚本式 e2e（且已失效）。`keepalive.yml` 被有意移除、依赖外部 `agentscope-keepalive` 中央守望 —— 若那套挂了，60 天无活动 GitHub 会停掉定时任务。
13. README 体积描述自相矛盾：规格段写 gz `~35 MB`，下载段写 `~80 MB`。

---

哥哥要是要，我可以先把第 1、2、3 条修掉（gone 比例守卫 + sitemap 总量熔断 + README 对齐代码），改动都很小，风险可控。


# 2

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