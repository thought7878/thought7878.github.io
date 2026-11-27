 video-talkcraft 调研报告（供零基础用户上手）

> 调研时间：2026-11-27 · 全部为只读网络调研，未 clone、未安装、未改任何文件
> 事实标注约定：**[README]** = 仓库 README 明写；**[源码]** = 我直接读取源文件确认；**[文档]** = references/ 内方法论文档；**[推断]** = 我的推理，未经验证；**[未找到]** = 查不到

---

## 0. 一句话结论

video-talkcraft **不是一个视频编辑器，也不是模板站**，而是一个 **Claude Code / Codex 的 "Agent Skill"（技能包）**——一套写给 AI 编程助手的操作手册 + 可执行脚本 + 108 张可复制的动效配方卡。
你把「口播稿 + 一条成品配音」交给 AI，它替你算字级时间戳、写分镜、生成 Remotion 视频工程、渲染成 MP4。
**必须有 AI 编程助手（Claude Code / Codex 等）+ 会写中文稿子 + 有耐心盯流程**，纯手工点鼠标用它会非常痛苦。

---

## 1. 项目身份与元数据

| 项 | 值 | 来源 |
| --- | --- | --- |
| 仓库 | https://github.com/Vincentwei1021/video-talkcraft | — |
| Star / Fork | **2553 / 253** | [GitHub API, 2026-11-27] |
| 创建 / 最后推送 | 2026-08-22 / **2026-11-25** | [GitHub API] |
| 默认分支 | `main` | [GitHub API] |
| 主语言 | HTML（GitHub 统计口径；实际是 TSX + Python 混合） | [GitHub API] |
| 仓库体积 | 33,413 KB ≈ 33 MB（不含 node_modules） | [GitHub API] |
| Open issues | 4 | [GitHub API] |
| License | **PolyForm Noncommercial 1.0.0**（GitHub 识别为 "Other / NOASSERTION"） | [LICENSE 源码] |
| 在线画廊 | https://vincentwei1021.github.io/video-talkcraft/ | [README] |
| 姊妹项目 | https://github.com/Vincentwei1021/video-shotcraft（宣传片向，**22.8k star，Apache-2.0**） | [该仓库页面] |
| 多语言 README | README.md(中) / README_EN.md / README_JA.md | [目录树] |


**⚠️ 卡数量口径三套并存（重要，别被绕晕）：**

- README 正文 / taxonomy.md / 在线画廊 / demos 目录：**108 张**（我数了 `demos/` = 107 个卡目录 + `_lib`，与 108 一致）[源码+目录树]
- GitHub 仓库 description 写 **109** [GitHub API]
- `template/README.md` 写 **78**（2026-08-28 旧文，未更新）[源码]
- 部分中文公众号文章写 78 / 79（引用的是更早版本）[外部文章]

→ 稳妥说法：**"百余张动效配方卡"**，以 README 的 108 为准。

---

## 2. **仓库结构**（实测目录树）

```
video-talkcraft/
├── SKILL.md                    # ★ agent 入口，62KB，八步管线与硬规则（最重要的一份文件）
├── README.md / README_EN.md / README_JA.md
├── LICENSE                     # PolyForm Noncommercial 1.0.0
├── THIRD_PARTY_NOTICES.md      # 第三方许可（Lucide ISC / 部分 MIT）
├── .env.example                # Fish Audio API key 模板
├── package.json                # 仅 56B（根目录几乎没用，依赖在 runtime/）
├── references/                 # 方法论（17 个文件）
│   ├── design-language.md      # 视觉系统：色板/字阶/布局/字幕（28KB）
│   ├── cinematography.md       # 七层镜头模型、转场、排版预算、验收关卡
│   ├── shot-design.md          # 镜头三面工作单 + 七型预设
│   ├── shotbook-example.md     # ★ 完整 SHOTBOOK 范例（照抄这个格式写分镜）
│   ├── taxonomy.md             # 108 卡索引（按类别 + 输入类型 + 语义）
│   ├── cards-index.json        # 85KB 机器可读卡片索引
│   ├── cards/                  # 每张卡的配方文档
│   ├── layout.md / schematic.md / semantic-annotation.md
│   ├── broll-sources.md        # 免署名素材源（Pexels/Pixabay/Mixkit/Coverr/NASA）
│   ├── host-footage.md         # ★ 人物素材：抠像、人脸安全区、角标规则
│   ├── review-protocol.md / demo-spec.md
├── template/                   # 可直接复制的 Remotion 代码
│   ├── cards/                  # 108 张卡逐卡自包含 tsx（≥92 个 .tsx 已确认）
│   ├── motion-systems/         # camera.tsx / life.tsx / env.tsx / transitions.tsx
│   │                           #   / longtake.tsx / shots.ts / theme.ts / timing.ts
│   │                           #   / MainVideo-example.tsx
│   └── components/             # lib.tsx / components.tsx / pencil.tsx / mascot.tsx
├── scripts/                    # 31 个可执行脚本（管线主体）
│   ├── timestamps_cpu.py       # ★ 字级时间戳（FireRedASR2-CTC / faster-whisper）
│   ├── make_timing.py          # timestamps.json → timing.json
│   ├── tts_fishaudio.py        # 可选：Fish Audio 云端配音+对齐
│   ├── voice_trim.py           # 真人录音预剪（口水词/结巴重说/长停顿）
│   ├── render_shots.mjs        # ★ 分段渲染母版制
│   ├── render_stills.mjs       # 批量静帧
│   ├── preflight.py            # ★ 开工体检（45KB，最强的断言集）
│   ├── motion_check.py / sfx_check.py / beat_lint.py / card_lint.py
│   ├── face_bbox.py            # 人脸安全区检测（OpenCV YuNet）
│   ├── beat_gap_check.py / freeze_probe.py / contact_sheet.py
│   ├── pipeline_state.mjs / workbench_contract_lint.py / semantic_annotate.py
│   └── test_*.py（回归测试）
├── runtime/                    # ★ 统一依赖（只有这一份 node_modules）
│   ├── check-runtime.sh        # 装依赖+下浏览器+链 workbench+冒烟渲
│   ├── link-runtime.sh         # 把工程接到共享依赖
│   ├── package.json            # remotion 4.0.519 / react 19.2.8 / three / gsap / animejs
│   ├── package-lock.json / tsconfig.json / test_runtime.py / smoke/
├── workbench/                  # ★ 剪映式动效工作台（Vite + React）
│   ├── GUIDE.md                # 图文指南（17KB）
│   ├── package.json / vite.config.ts / remotion.config.ts
│   ├── src/ pipeline/ cards/ kb/ panels/ preview/ timeline/ gen/ scripts/ tests/
│   ├── kbsrc-stub/             # 未接工程时的降级实现
│   └── node_modules → ../runtime/node_modules（软链）
├── demos/                      # 107 个可跑的 HTML 预览 + _lib（含内嵌 base64 音效）
├── gallery/                    # 单页本地画廊（build.mjs + i18n/）
└── assets/ .github/
```

**语言构成（实测文件类型）：** `.tsx`（卡片/模板/工作台，数百个）、`.md`（方法论，最重）、`.py`（管线脚本，31 个）、`.mjs`（Node 渲染脚本）、`.html`（demo 预览）、`.json`（索引/配置）、`.sh`（runtime 脚本）。

---

## 3. 它到底做什么：输入 / 输出 / 管线

### 3.1 输入（README "你提供什么 vs 它做什么" 表 + SKILL.md）

| 你提供                  | 形态                                                      | 必需？                                           |
| -------------------- | ------------------------------------------------------- | --------------------------------------------- |
| **口播稿**              | `script.json` `{"sentences":["句1","句2"]}` 或每行一句的 `.txt` | **必需**                                        |
| **成品配音**             | 一条完整 wav/mp3，**必须与口播稿逐字一致**                             | **必需**                                        |
| 人物素材（真人录播 / 数字人成品视频） | 绿幕 mp4 或已抠好的 alpha WebM                                 | 可选                                            |
| B-roll / 网页截图 / 图片   | 落盘到 `public/`                                           | 可选但**强烈建议**（preflight 会 FAIL：只有动效+口播 = PPT 感） |


**⚠️ 关键边界（README/SKILL.md 明写）：**

> "TTS 合成/数字人生成是制作端输入，**不在本库**"
> "数字人生成技术不在本 skill 内（人物素材是输入）"

→ **它不生成配音、不生成数字人、不生成图像。** 你必须自带配音和素材。（唯一例外：可选的 Fish Audio 云端 TTS。）

### 3.2 输出

- **横屏 1920×1080** MP4（默认）；竖屏 1080×1920 需显式指定（抖音渠道）[SKILL.md ⓪]
- 层叠结构：幕底（背景）→ 配音 → 各镜头 → 字幕 → 幕级覆盖
- 附带可编辑 Remotion 工程（可再改）、`workbench/` 里可后期微调
- 响度归一交付

### 3.3 八步管线（SKILL.md 原文）

```
① 文案 → ② 配音输入+时间戳(本机CPU) → ③ 素材 → ④ SHOTBOOK 层矩阵
      → ⑤ 实现(全局系统 → 首镜先做先确认 → 其余镜头)
      → ⑥ 渲染 → ⑦ 三重验收（机器闸全过 + 1 轮审片修 P0/P1）→ ⑧ 交付（可选续审 ≤3 轮）
```

**每步细节：**

- **① 文案**：一句一个信息点，钩子第一句；**数字一律写汉字**（`197747` 无法和"十九万七千"对位）
- **② 配音 + 时间戳**：ASR 出带时间的词表 → 与口播稿做字符级 difflib 对齐 → 输出 `timestamps.json`（含每句 `match` 分）→ `make_timing.py` 转成 `timing.json`（每字一个 t/e）
- **③ 素材**：B-roll 用 Pexels+Pixabay 双源 API 并行；网页素材用 Playwright 全页 2× 长截图 + DOM 实测坐标 JSON
- **④ SHOTBOOK**：分层矩阵，每镜写"节拍/Layer/对象/动作/角色"，另有 G0 风格档 + G1~G6 全局系统
- **⑤ 实现**：先搭全合成骨架（其余镜头占位）→ 只做样板镜 s01 → `--seg-audio` 渲有声单镜预览 → **问用户确认** → 才做其余镜头
- **⑥ 渲染**：分段母版制（按镜头切段、段内单进程保光栅一致、段间 `--parallel 4`）→ 拼装 → 整条音轨混入 → 帧数断言
- **⑦ 三重验收**：① 画面健康（静止段 + 并发光栅抖动，机器判定）② 音效逐 cue 能量验证 ③ 独立审片（锚点帧 + 3×4 拼图）
- **⑧ 交付**：可选续审，累计封顶 3 轮

**对齐精度（README + timestamps_cpu.py docstring 实测表，110s 中英混合 vs GPU Qwen3-ForcedAligner）：**

| 配置 | 中位 | p95 | 最大 | match 误报 | 模型+环境 |
| --- | --- | --- | --- | --- | --- |
| **FireRedASR2-CTC int8（默认推荐）** | 40ms | 100ms | **200ms** | **0** | 767MB+103MB |
| faster-whisper small/int8 | 20ms | 140ms | 413ms | 2 句边缘 | 464MB+231MB |
| Qwen3-ASR+Aligner（需 GPU） | 20ms | 60ms | 293ms | 0 | 3.5GB+1.5GB(torch) |


---

## 4. 环境与依赖

### 4.1 硬性要求 [README + 源码]

| 依赖                    | 版本            | 必需性 | 说明                                                      |
| --------------------- | ------------- | --- | ------------------------------------------------------- |
| **Node.js**           | **18+**       | 必需  | Remotion 4.0.519 渲染                                     |
| **Python**            | **3.10+**     | 必需  | 时间戳/质检脚本                                                |
| **ffmpeg**            | 任意新版          | 必需  | 音视频处理、抠像、TTS 拼接                                         |
| Chrome Headless Shell | ~95MB / 190MB | 必需  | **由 `npx remotion browser ensure` 自动下载**，无需手动装          |
| 磁盘                    | ≈ **1.6 GB**  | —   | node_modules 760MB（含 190MB 浏览器）+ ASR 模型 767MB + 仓库本体    |
| 内存                    | 8GB 够用        | —   | [外部文章实测]；ASR 峰值内存实测 8.1GB（整段）/ 2.4GB（切段后）[源码 docstring] |
| **GPU**               | **不需要**       | —   | 全 CPU。**实测 4 配置均在 Apple Silicon CPU 上跑通**[源码 docstring] |


**不需要**：sox、playwright（浏览器是 Remotion 自带的 headless shell，不是 Playwright）、OpenAI key、Azure key、ElevenLabs key、图像生成 API。

### 4.2 Python 包 [README + 源码]

```bash
# 时间戳（默认 FireRed 后端）
pip install zhconv pypinyin sherpa-onnx soundfile numpy
# 可选：免手动下模型的 whisper 后端
pip install faster-whisper
# 可选：Fish Audio 云端 TTS
pip install requests python-dotenv
# 可选：人脸安全区检测
pip install opencv-python
```

> `zhconv` / `pypinyin` **不装也能跑**，但"锚点大量流失（ASR 随机吐繁体、同音字错写是常态）"[源码 docstring] → **强烈建议装**。

### 4.3 模型下载（唯一的大件）

| 模型 | 大小 | 默认位置 | 下载地址 |
| --- | --- | --- | --- |
| **FireRedASR2-CTC int8** | **767MB**（+103MB 环境） | `~/.cache/koubo/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25/`（含 `model.int8.onnx` + `tokens.txt`） | GitHub: `https://github.com/k2-fsa/sherpa-onnx/releases/download/asr-models/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25.tar.bz2`<br>HF: `https://huggingface.co/csukuangfj2/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25` |
| faster-whisper small/int8（备选） | 460–460MB | 自动缓存 | 首次运行自动下载 |
| OpenCV YuNet（人脸检测，可选） | ~230KB | 自动 | 随 opencv-python |


### 4.4 API Key（全部可选！）

| Key | 必需？ | 用途 |
| --- | --- | --- |
| `FISH_AUDIO_API_KEY` | **否** | 只有你想让云端合成配音才需要 [.env.example] |
| `FISH_AUDIO_REFERENCE_ID` | 否 | 音色库音色 ID，留空用默认音色 |
| `FISH_AUDIO_MODEL` | 否 | 默认 `s2.1-pro-free`；可选 `s1 / s2-pro / s2.1-pro / s2.1-pro-free / drama-3-preview` |
| `FISH_AUDIO_FORMAT` | 否 | `mp3`(默认)/`wav`/`opus` |
| `FISH_AUDIO_LATENCY` | 否 | `normal`/`balanced`(默认)/`low` |
| OpenAI / Azure / ElevenLabs | **完全不需要** | — |


**`.env.example` 原文结构：**

```
FISH_AUDIO_API_KEY=your_fish_audio_api_key_here
FISH_AUDIO_REFERENCE_ID=
FISH_AUDIO_MODEL=s2.1-pro-free
FISH_AUDIO_FORMAT=mp3
FISH_AUDIO_LATENCY=balanced
```

---

## 5. 完整运行命令清单

### 5.1 安装 Skill

```bash
# 方式 A：直接对 AI 说
# "帮我安装这个 skill：https://github.com/Vincentwei1021/video-talkcraft"

# 方式 B：skills CLI
npx skills add Vincentwei1021/video-talkcraft

# 方式 C：手动（目录名必须与 SKILL.md 的 name 一致）
git clone https://github.com/Vincentwei1021/video-talkcraft.git
cd video-talkcraft
ln -s "$(pwd)" ~/.claude/skills/video-talkcraft   # Claude Code
# 或
ln -s "$(pwd)" ~/.codex/skills/video-talkcraft    # Codex
# WorkBuddy 用户则是 ~/.workbuddy/skills/video-talkcraft
```

### 5.2 开工体检（⭐ 每支新片第一步）

```bash
bash <skill根>/runtime/check-runtime.sh --upgrade
```

做 5 件事 [check-runtime.sh 源码]：① `npm ci` 装 runtime/node_modules ② `npx remotion browser ensure` 下共享无头浏览器 ③ 把 `workbench/node_modules` 软链到 runtime ④ 检查 Remotion 版本一致性（有新版就全家升级 + 冒烟渲 1 帧，不过自动回滚）⑤ 冒烟渲 `/tmp/talkcraft-smoke.png`
**末行必须打印 `[runtime] OK` 才往下走。**

### 5.3 接新工程（依赖软链）

```bash
bash <skill根>/runtime/link-runtime.sh <工程根>
```

→ 把 `<工程>/remotion/node_modules` 软链到 `runtime/node_modules`，并把 package.json 依赖版本对齐 runtime。

> **🚨 工程目录里永远不要 `npm install`**（会写进共享目录，破坏版本一致性）。要加包 → 加进 `runtime/package.json` → `npm ci` → 提交。

### 5.4 配音 + 时间戳

```bash
# 方案 A（默认）：自带配音，本机 CPU 对齐
python3 scripts/timestamps_cpu.py audio/full.wav script.json audio/timestamps.json
# 备选后端（免手动下模型，首跑自动下载 460MB）
python3 scripts/timestamps_cpu.py audio/full.wav script.json audio/timestamps.json --backend whisper
# 语速： --model small | --model-dir <路径> | --chunk-sec 75（0=不切段）

# 转成 Remotion timing.json（标点零时长）
python3 scripts/make_timing.py audio/timestamps.json remotion/src/timing.json

# 真人录音预剪（口水词/结巴重说/过长停顿）—— 必须在时间戳之前跑
python3 scripts/voice_trim.py <录音> <script.json> --out <...>   # 产出 cuts.json EDL
```

**方案 B（可选）：Fish Audio 一键云端配音+时间戳**

```bash
cp .env.example .env      # 填 FISH_AUDIO_API_KEY
# 默认逐句模式，每句一次请求，句间插 0.25 秒真静音
python3 scripts/tts_fishaudio.py script.json audio/full.wav audio/timestamps.json --timing-out remotion/src/timing.json
# 整稿一次请求，保留模型生成的句间节奏
python3 scripts/tts_fishaudio.py script.json audio/full.wav audio/timestamps.json --timing-out remotion/src/timing.json --mode stream
```

API 端点：`https://api.fish.audio/v1/tts/stream/with-timestamp`，默认采样率 24000。

> ⚠️ `stream` 模式**不是边生成边播**，文件在全部生成完成后才写出 [README]。

### 5.5 素材体检（③ 阶段必跑，FAIL 不许进 ④）

```bash
python3 scripts/preflight.py --media-only --host remotion/public/dh/host.webm --fps 30 --voice audio/full.wav
# 全量闸（④→⑤）：额外检查 SHOTBOOK 对账
python3 scripts/preflight.py --shotbook SHOTBOOK.md --host ... --fps 30 --voice ... --shots remotion/shots.json --min-footage-ratio 0.34
```

检查项（preflight.py 源码）：人物素材 VFR/重复帧/帧率一致/时长逐帧对齐/真实宽高比、素材盘点、sources.md 在册、SHOTBOOK「素材：」行路径存在、**全片零 V·图 → FAIL**、版式轮换、语义覆盖等。

### 5.6 渲染（⑥）

```bash
# ⑥-0 渲染前静态预检（零母版成本）
python3 scripts/beat_gap_check.py remotion/beats.json remotion/shots.json
cd remotion && python3 ../scripts/freeze_probe.py --shots shots.json

# ⑥-1 静帧抽样（一次 bundle 批量渲，比逐张 npx remotion still 快一个量级）
node <skill根>/scripts/render_stills.mjs --times 2.0,7.2,...

# ⑤-1 样板镜有声预览（只渲这一镜）
cd remotion
node <skill根>/scripts/render_shots.mjs --shots shots.json --only s01 --seg-audio --preview-dir out/preview
open out/preview/s01.mp4

# ⑥-2 首渲整片：K 段并行 + 拼装 + 整条音轨 + 混音
node <skill根>/scripts/render_shots.mjs --shots shots.json --all --parallel 4 \
     --concat out/assembled.mp4 --audio out/full-mix.wav --mux out/vN.mp4

# 改一个镜头 → 只重渲该段±邻段
node <skill根>/scripts/render_shots.mjs --shots shots.json --changed s14 \
     --concat out/assembled.mp4 --audio out/full-mix.wav --mux out/vN+1.mp4
```

**渲染脚本必须在工程 `remotion/` 目录下执行**（Remotion 模块从工程自己的 node_modules 解析 + 自动加载工程 `remotion.config.ts`）。

**原生 Remotion 命令（手动补渲时）**：

```bash
npx remotion compositions src/entry.ts      # 列出 Composition ID
npx remotion studio src/entry.ts
npx remotion render src/entry.ts <CompId> out/x.mp4
npx remotion still src/index.ts Demo15s out/f40.png --frame=40
npx remotion render src/entry.ts <Comp> out/sfx-solo.wav --props='{"sfxSolo":true}' --codec=wav
```

> ⚠️ **入口文件名不一致**：SKILL.md 的渲染命令用 `src/entry.ts`，而 `template/README.md` 和外部教程用 `src/index.ts`。**以你自己工程里实际存在的文件为准**，先用 `npx remotion compositions` 探明。

### 5.7 三重验收（⑦）

```bash
python3 scripts/motion_check.py       # 画面健康：静止段 + 并发光栅抖动（freezedetect n=0.003 d=0.8）
python3 scripts/card_lint.py          # 卡片必须复制自 template/cards
python3 scripts/beat_lint.py --shots shots.json   # 词落点对 timestamps + 镜尾保护带
python3 scripts/sfx_check.py           # solo 在场 + --mix 可听度
python3 scripts/contact_sheet.py       # QA 帧拼 3×4 网格给审片用
python3 scripts/workbench_contract_lint.py <工程根>   # 工作台拆解契约是否齐全
```

### 5.8 工作台（可选，后期微调）

```bash
bash <skill根>/runtime/check-runtime.sh
cd <skill根>/workbench
bash scripts/link-project.sh /path/to/<本片工程>   # 末行必须打印「拆解契约 OK」
npm run dev &                                       # http://localhost:5199
sleep 4 && curl -s http://localhost:5199 | grep -q '动效工作台' && echo "工作台 OK"
open 'http://localhost:5199/?tracks'                # 打开即多轨
```

其他命令：`npm run gen`（扫描 public/ 生成素材清单）、`npm run studio`（Remotion Studio + Inspector 调参）、`npm run build`。

### 5.9 回归测试（可选，验证安装是否正常）

```bash
python3 -m unittest discover -s scripts -p 'test_*fish*.py' -v     # Fish Audio 回归（不需要 API Key）
python3 -m unittest discover -s runtime -p 'test_*.py' -v
node --test workbench/tests/regressions.test.cjs scripts/render_shots.test.mjs
```

---

## 6. 常见坑（真实踩坑，非臆测）

### 6.1 🔴 结构性大坑

| 坑 | 现象 | 解法 |
| --- | --- | --- |
| **在工程目录跑 `npm install`** | 依赖被写进共享 runtime 目录，版本混乱，下次开新工程渲出来和上次不一致 | **只在 `runtime/` 装**；工程目录只放软链（`link-runtime.sh` 自动建） |
| **配音音频名写错** | 工作台多轨**整条静音**、导出丢人声 | 配音必须叫 **`public/narration.wav`**（`full.wav` 会被自动识别，但命名最好统一）[issue #40 + workbench/README] |
| **幕底层放错位置** | 渲染出来只有字幕，场景全不见 | 幕底必须画在**最下层（第一个元素）**；照抄模板时它常写在最后，会盖掉所有镜头 |
| **先做时间戳，后剪音频** | 剪完所有时刻整体错位 | 顺序锁死：**预剪 → 时间戳 → timing**。音频动了，时间戳必须重跑 |
| **音频整条分段渲染** | 每段各带 AAC 再拼，编码器前导延迟导致逐段错位 | **音轨整条不分段**，视频段全 muted，音轨单渲一次最后混入 |
| **主题色键对不上** | `tsc` 报"属性不存在"，卡在编译 | 复制组件后第一件事核 `theme.ts` 色键是否齐全（组件默认引用的色键你的主题可能没有） |
| **`Main` 组件调 `getInputProps()`** | Remotion Player 里必抛，看板上那一格变红 | 改成组件 props（Composition defaultProps）或加 `remotion_isPlayer` 守卫 |
| **Root.tsx 的 width/height/fps 写变量** | 看板按默认 1920×1080@30 画，并告警 | **必须写数字字面量** |
| **Remotion 静态服务器拒绝软链接** | 导出时素材 404 | 导出前自动解引用同步到 `.render-public/` 再渲染 |


### 6.2 🟠 中文 / TTS 相关

| 坑 | 说明 |
| --- | --- |
| **数字写阿拉伯数字** | `197747` 无法与"十九万七千"的读音对位 → **必须写汉字**（硬约束，SKILL.md ①） |
| **稿子与音频不完全一致** | 改口、临时加词 → 字幕对不上。规范化只忽略标点/大小写/字符宽度，**不推测 `2` 与 `two` 的读法转换** |
| **FireRed 整段超 193s 会崩** | onnxruntime 在 encoder 自注意力层崩（相对位置编码表定长），内存随长度平方涨。脚本**默认按静音切 ≤75s 段**；`--chunk-sec 0` 关闭切段（仅 ≤190s 安全）[源码 docstring] |
| **match < 0.90 的句子** | 脚本会打印 `← match<0.90，人工听核这一句`。match = 该句 CJK 字拿到直接 ASR 锚点的比例（CJK <5 字时退回全字符口径） |
| **whisper 后端不要传 initial_prompt** | 喂口播稿进 prompt 会让前几段整段幻觉错位（源码明确警告） |
| **Fish Audio 稿子与对齐文本不一致** | 直接报错退出，**不推测读法转换** → 改写为实际朗读文本，或用已有音频走本机 CPU 对齐 |
| **`match=1`/`ok=true` 不代表人工确认** | README 明说：仍需试听 |


### 6.3 🟡 下载 / 网络

| 坑 | 说明 |
| --- | --- |
| **HF 直连 502** | 换 `hf-mirror.com`（外部文章实测 7MB/s，110 秒下完 776MB） |
| **模型必须放固定目录** | `~/.cache/koubo/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25/`，两个文件 `model.int8.onnx` + `tokens.txt`，脚本默认找这里 |
| **首跑下载无头浏览器 ~95MB** | 离线机先 `npx remotion browser ensure`，或 `--browser` 指向本机 headless shell |
| **node_modules ≈760MB** | 含 190MB 浏览器。**不要每支片各装一份**（作者原来六个工程吃 3.9GB） |
| **制作中途不要 `--upgrade`** | 母版段缓存不认依赖版本，混版本渲出的段不一致 |


### 6.4 🟡 Windows 兼容（唯一开放的技术 issue）

[issue #45, open PR, 2026-11-10] `scripts/motion_check.py` 在 Windows 下三个问题：

1. 硬编码 `/tmp/motion_check_win` → 应用 `tempfile.gettempdir()`
2. 用已被新版 ffmpeg 移除的 `-vsync 0` → 改 `-fps_mode passthrough`
3. `cp1252/cp936` 控制台输出中文触发 `UnicodeEncodeError` → UTF-8 stdout

→ **Windows 用户建议优先用 WSL/Git Bash**（外部文章实测 Windows+Git Bash 跑通）。**macOS 是作者主力开发环境**（`open` 命令、`pbcopy` 等）。

### 6.5 🟡 质量闸的"数学不可达"陷阱

[SKILL.md 迭代纪律] 句间气口 <0.3s 且音效电平上限 −22dB 时，`sfx_check --mix` 的 UNMASKED **在数学上不可达**——这是"输入决定、不是本片可修"的结论，要早下，别反复改。
同理 `motion_check` 的静止判定是 `freezedetect n=0.003 d=0.8`（逐帧平均像素差 >0.3% 才算动）——**平滑渐变、漂背景、纯透明度呼吸都不产生像素变化，只有相机缩放才改每一个像素**。

---

## 7. 中文支持（结论：完全支持，且是首要设计目标）

**[README]** "能做哪类视频？知识科普、产品评测、新闻解读、观点锐评等口播/解说类横屏视频。**中文口播优先设计，中英混排完全支持。**"

具体证据：

- ASR 模型 `zh_en`（中英双语）[源码]
- 对齐算法：繁简归一（zhconv）+ 无声调拼音匹配（同音字不算错）+ 数字转中文读法（`num2cn`，≤8 位）[源码]
- 字幕组件 `Subtitles.tsx` 素排（整句硬现、整句硬走），横屏默认红线 bottom 100 / 字号 44 / 宽 66%，竖屏传 props [template/README]
- 字体：PingFang SC 600/400 + SF Mono tabular-nums [SHOTBOOK 范例]
- 竖屏安全区：**竖屏人物角标必须左下**（右缘是抖音点赞栏）
- README 有中文/英文/日文三版；在线画廊有 i18n 目录

**⚠️ 未能验证**：我没有找到任何**中文模型的 TTS 内置方案**——配音必须自己提供（真人录音 / 任意第三方 TTS / Fish Audio / edge-tts 等）。中文 TTS 质量完全取决于你自己选的工具。

---

## 8. 教程 / 社区 / 二次内容

### 8.1 中文教程（我实际读到的，质量排序）

| #     | 文章                                            | 出处                                             | 价值                                                                                                                                                                                                       |
| ----- | --------------------------------------------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **《学用系列丨video-talkcraft，口播稿文字，怎么变成一段AI短片？》**  | 微信公众号「…教育研究」（mp.weixin.qq.com, mid=2247500633） | **★★★ 最强**。作者是一线教育研究者，完整跑通 15 秒片：装了 skill、写了 5 句 JSON、用 **edge-tts** 合成配音（免费中文）、跑时间戳、建 5 文件 Remotion 工程、46 秒渲完。附**完整命令**、截图、**7 个真实踩坑表**（幕底位置、npm install、theme 色键、时间戳顺序…）、4 条镜头经验、硬件/磁盘实测。**强烈建议先读这篇。** |
| 2     | 《口播录好了，视频怎么做？video-talkcraft 字幕、动效与成片上手》      | 微信公众号（mid=2247484437）                          | ★★ 面向新手：最小素材集、三步法、现成提示词、常见问题（字幕对不上 / 像 PPT / 字体丢失 / 改一句重头生成）。**注意：它给的 `npx remotion compositions src/entry.ts` 是示例，实际要用你工程的真实入口和 ID。**                                                                   |
| 3     | 《5 个口播剪辑仓库：星数和能不能商用是两回事》                      | 微信公众号（mid=2247486477）                          | ★★ 横向对比（video-talkcraft vs video-use / herk-edit-kit / ghost-editor / one-prompt-video-skills），**把 Remotion 席位成本和 PolyForm 禁商用讲透了**                                                                      |
| 4     | 《20 个自媒体运营 Skill 完整清单》/《Codex 自媒体运营 Skill 大全》 | 多篇微信公众号                                        | ★ 只作为"skill 清单里的一条"提及                                                                                                                                                                                    |
| 5     | 《AI 智能视频口播一键剪辑…video-talkcraft》               | 微信公众号（mid=2247486601）                          | ★ 提及 Loomy 客户端安装演示                                                                                                                                                                                       |


### 8.2 其它

- **在线画廊**：https://vincentwei1021.github.io/video-talkcraft/ — 108 张动效预览一页全览，可搜索（**最直观的"风格体检"工具：先看这个再决定要不要用**）
- **本地画廊**：`open gallery/index.html`
- **GitHub Issues**：4 个 open，其中 3 个是**商用授权咨询**（#54 巴西工作室、#46 印尼 YouTube 变现、#43 中国个人开发者接单）；1 个是 Windows 兼容 PR（#45）
- **微信交流群**：「Vincent 的 AI 交流群 5」，二维码在 README，**2026-12-02 前有效**
- **Ko-fi** 赞助：https://ko-fi.com/vincentwei
- **作者其他渠道**：抖音 / 小红书 / X(@VincentWei93) / GitHub

### 8.3 Fork 情况

- **253 个 fork**（GitHub API）[实测]
- API 列出的 fork 全部是 `name: "video-talkcraft"` 的原样复制，**stars=0、无独立改动**——**没有值得跟进的第三方 fork**
- **未找到**：CSDN / 掘金 / 知乎 上的专门技术教程；B 站完整视频教程。中文内容**集中在微信公众号**。**这是我的搜索结论，不是"不存在"的断言**——搜索工具在这些中文站点的覆盖有限。

---

## 9. 许可与成本（⭐ 最容易踩的坑）

### 9.1 video-talkcraft 自身：PolyForm Noncommercial 1.0.0

[LICENSE 源码原文]

> Required Notice: Copyright (c) 2026 Vincent Wei (https://github.com/Vincentwei1021/video-talkcraft)
> Videos produced with this toolkit belong to their creators. This license governs the toolkit itself: **noncommercial use is free; any commercial use of the toolkit requires prior authorization from the author.**

| 场景 | 允许？ |
| --- | --- |
| 个人学习 / 教育 / 研究 / 业余创作 / 宗教用途 | ✅ 免费 |
| 慈善组织 / 教育机构 / 公共研究 / 公共卫生 / 环保组织 / 政府机构 | ✅ 免费（**不论资金来源**） |
| 接单收费 / 广告 / 商业发布 / YouTube 变现 / 付费课程 | ❌ **必须事先授权** |
| 把视频拿去赚钱（内容赚钱 vs 工具商用） | ⚠️ **区分清楚**：PolyForm 管的是"工具的用途"。README 说"用它做出的视频归你所有"。但 GitHub 上 #54/#46 两个商用授权 issue 说明**作者对"视频赚钱"也按商用处理**（保守起见，按需要授权对待） |


**授权联系**：`vincentwei1021@gmail.com` 或提 GitHub issue。

### 9.2 Remotion 渲染引擎（真正的隐藏成本）

[Remotion 官方 pricing 页，2026-11-27]

| 许可 | 条件 | 价格 |
| --- | --- | --- |
| **Free License** | **个人 或 ≤3 人公司**，含全部功能、**不限商用渲染** | **免费** |
| Remotion for Creators | 4 人以上团队做视频 | **$25/月/席位** |
| Remotion for Automators | 批量渲染 / 视频编辑器 / prompt-to-video 产品 | **$0.01/次渲染，$100/月起** |
| Enterprise | 高级需求 | 起步 $500/月 |


→ **单人 / 三人以内团队：Remotion 零成本。** 4 人以上接单团队要买席位。

### 9.3 其他成本

| 项 | 成本 |
| --- | --- |
| Fish Audio `s2.1-pro-free` | 免费额度（**额度和可用期限由 Fish Audio 自行决定，随时可能变**）[README 明写] |
| ASR 模型 / node_modules | 完全免费 |
| **Claude Code / Codex 订阅** | ⚠️ **需要付费订阅**（外部文章强调："这五个全都要 Claude Code 或同类的 coding agent"）。免费档能否支撑完整 8 步流程**未验证** |
| Pexels / Pixabay / Mixkit / Coverr / NASA | 免署名（Mixkit Sound Effects Free License） |
| Lucide 图标 | ISC（部分 Feather 图标 MIT） |
| 内嵌音效 | 全部 Mixkit 免署名可商用 [demos/_lib/sfx/ATTRIBUTION.md] |


### 9.4 ⚠️ 素材授权硬规则

- **B-roll 只用免署名源**：Pexels、Pixabay、Mixkit Free、Coverr、NASA（`references/broll-sources.md` 记录了被排除源的授权陷阱）
- **`sources.md` 必须存在**，链接与本地截图一一对应，`preflight.py` 会查（不存在 → FAIL）
- **第三方许可声明要随工程带上**（`THIRD_PARTY_NOTICES.md` + `demos/_lib/media/ATTRIBUTION.md` + `demos/_lib/sfx/ATTRIBUTION.md`）
- **`demos/_lib/dh-host.webm` 是 AI 生成的演示占位形象，生产必须换成你自己的素材**

---

## 10. 给零基础用户的行动方案

### 10.1 先做这个判断（5 分钟）

| 你的情况 | 建议 |
| --- | --- |
| 有 Claude Code / Codex 订阅，愿意跟 AI 协作，愿意写稿 | ✅ **适合**，按下面走 |
| 没有 AI 编程助手，想找"一键出片"的按钮 | ❌ **不适合**，这是给 agent 用的 skill，不是 GUI 软件 |
| 想做 Vlog / 剧情短片 / 需要真人出镜主演 | ❌ **不适合**（README 明说只做口播/解说；人物素材是输入） |
| 想用图片生成模型"一句话生视频" | ❌ **不是这类工具**（画面是代码算出来的，不是模型生成的） |
| 商用 / 接单 / 变现 | ⚠️ **先申请授权**（两重：PolyForm + Remotion 席位） |


### 10.2 第一支片：照着这个最小闭环做（作者 + 外部教程共同验证过的路径）

**目标：一条 20–30 秒、1920×1080、3 镜、1 条配音的短片。** 先跑通，再加复杂度。

```
[0] 装 skill
    git clone … && ln -s "$(pwd)" ~/.claude/skills/video-talkcraft
    或直接对 AI 说："帮我安装这个 skill：https://github.com/Vincentwei1021/video-talkcraft"

[1] 依赖体检
    bash <skill根>/runtime/check-runtime.sh --upgrade      # 末行 [runtime] OK

[2] 写稿（JSON，一句一条，数字写汉字）
    script.json  →  {"sentences": ["第一句钩子。", "第二句。", "第三句结论。"]}

[3] 出配音（★ 你必须自己解决这一步）
    方案A 手机录音 / 任意 TTS → audio/full.wav
    方案B edge-tts（免费中文，外部教程实测）：逐句合成 → 裁首尾静音 → 句间插 0.22s 真静音 → 拼接
    方案C Fish Audio（需 API Key）：见 §5.4 方案 B
    ⚠️ 稿子必须与念出来的字逐字一致

[4] 字级时间戳（首次会下 767MB 模型，耐心等；国内建议 hf-mirror.com）
    python3 scripts/timestamps_cpu.py audio/full.wav script.json audio/timestamps.json
    python3 scripts/make_timing.py audio/timestamps.json remotion/src/timing.json
    # 看输出：每句 match，<0.90 的要人工试听核实

[5] 素材（最少也要有！preflight 会 FAIL "只有动效+口播 = PPT 感"）
    3 张清晰截图 或 1 段 20s 录屏 → 落盘 public/
    同时写 sources.md（来源 + 授权）

[6] 写 SHOTBOOK（照抄 references/shotbook-example.md 的格式）
    references/shotbook-example.md 是完整范例，直接对照改

[7] 体检
    python3 scripts/preflight.py --shotbook SHOTBOOK.md --fps 30 --voice audio/full.wav

[8] 建 Remotion 工程 + 接共享依赖
    mkdir -p remotion/src/scenes remotion/public
    bash <skill根>/runtime/link-runtime.sh <工程根>
    # 工程文件：src/index.ts（注册 Composition）· src/Root.tsx（1920×1080@30，数字字面量）
    #           src/Main.tsx（幕底→配音→镜头→字幕 四层顺序）
    #           src/theme.ts · src/shots.ts · src/timing.json（从第4步来）· src/scenes/S1..S3.tsx
    #           remotion/public/narration.wav（★ 名字必须是这个）

[9] 样板镜预览（先只做 s01，渲一条有声单镜给自己看）
    cd remotion && node <skill根>/scripts/render_shots.mjs --shots shots.json --only s01 --seg-audio --preview-dir out/preview
    open out/preview/s01.mp4
    # 看：字号够大吗？字幕压到人了吗？卡片位置对吗？→ 改到满意再往下

[10] 静帧抽查（比整渲便宜）
    node <skill根>/scripts/render_stills.mjs --times 2.0,7.2,...

[11] 整片渲染
    node <skill根>/scripts/render_shots.mjs --shots shots.json --all --parallel 4 \
         --concat out/assembled.mp4 --audio out/full-mix.wav --mux out/final.mp4

[12] 三重验收
    python3 scripts/motion_check.py / sfx_check.py / beat_lint.py --shots shots.json

[13] 交付 + 可选工作台微调
    cd <skill根>/workbench && bash scripts/link-project.sh <工程根> && npm run dev
    open 'http://localhost:5199/?tracks'
```

### 10.3 时间与资源预期（README + 外部教程实测）

| 项 | 数据 | 来源 |
| --- | --- | --- |
| 全片首渲（201s 竖屏片） | **9 分钟**（优化前 13 分钟） | README 实测表 |
| 改一个镜头出有声新片 | **53 秒**（优化前整渲） | README 实测表 |
| 43 张静帧抽样 | **~1 分钟**（优化前 11 分钟） | README 实测表 |
| 15 秒片整渲（作者实测） | **46 秒** | 外部教程 |
| 6s 首镜有声预览 | **47 秒** | SKILL.md |
| 读 160 张 QA 帧 | ~4 万 token / 7 分钟（用 3×4 拼图） | README 实测表 |
| ASR 时间戳（110s 音频） | 24s 推理 + 模型载入 | 源码 docstring |
| node_modules | 760MB（含 190MB 浏览器） | README |
| 磁盘总计 | ≈1.6GB | 外部教程 |


---

## 11. 我没能验证的部分（诚实清单）

1. **未 clone、未运行任何命令** —— 所有命令来自 README/SKILL.md/脚本源码的**逐字摘录**，我没有实际执行过一条。
2. **`src/entry.ts` vs `src/index.ts`** 入口命名在官方文档内部不一致（SKILL.md 用 entry，template/README 用 index），真实工程用哪个取决于生成模板。
3. **108 vs 109 vs 78** 卡数量三个口径并存，未能确定哪个是当前真值（`demos/` 我数到 107 个卡目录 + `_lib` = 108，倾向 README 的 108）。
4. **Windows 兼容性未验证** —— 只知道有 open PR #45 在修 motion_check.py；workbench 的 Vite dev server、Remotion CLI 在 Windows 上是否正常，无人报告。
5. **Claude Code 免费档能否跑完整流程** —— 未验证。
6. **Fish Audio 免费额度到底多大、会不会随时取消** —— README 明说"以 Fish Audio 为准"，无从核实。
7. **CSDN / 掘金 / 知乎 / B站 有无更详细的技术教程** —— 我的搜索工具对这几个站点覆盖有限，**搜索无结果 ≠ 一定不存在**。已确认的中文深度教程全在微信公众号。
8. **`skills.sh` 上的安装量统计** —— 页面是动态渲染，抓取只拿到空表格，无数据。
9. **`references/cards/` 目录下每张卡的配方文档未逐一阅读** —— 只验证了索引结构。
10. **workbench 的 Vite + Remotion 双轨渲染在生产大工程上的稳定性** —— 只有作者自述。

---

## 12. 关键 URL 速查

```
仓库          https://github.com/Vincentwei1021/video-talkcraft
在线画廊      https://vincentwei1021.github.io/video-talkcraft/
姊妹项目      https://github.com/Vincentwei1021/video-shotcraft
入口方法论    https://github.com/Vincentwei1021/video-talkcraft/blob/main/SKILL.md
工作台指南    https://github.com/Vincentwei1021/video-talkcraft/blob/main/workbench/GUIDE.md
SHOTBOOK范例  https://github.com/Vincentwei1021/video-talkcraft/blob/main/references/shotbook-example.md
人物素材规范  https://github.com/Vincentwei1021/video-talkcraft/blob/main/references/host-footage.md
许可          https://github.com/Vincentwei1021/video-talkcraft/blob/main/LICENSE
第三方声明    https://github.com/Vincentwei1021/video-talkcraft/blob/main/THIRD_PARTY_NOTICES.md

ASR 模型
  GitHub   https://github.com/k2-fsa/sherpa-onnx/releases/download/asr-models/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25.tar.bz2
  HF       https://huggingface.co/csukuangfj2/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25
  镜像     https://hf-mirror.com/csukuangfj2/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25
  存放     ~/.cache/koubo/sherpa-onnx-fire-red-asr2-ctc-zh_en-int8-2026-02-25/

Fish Audio  https://fish.audio/  →  https://api.fish.audio/v1/tts/stream/with-timestamp
Remotion    https://www.remotion.dev/docs/license/pricing
```