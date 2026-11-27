

> 结论先行：**这个项目不是一个"双击就能用"的软件，它是一本写给 AI 的操作手册 + 108 个动效模板 + 一个剪映式工作台。**  
> 它的完整形态是"你给稿子和配音 → AI 按手册逐镜实现 → Remotion 渲染 → 机器 + 人眼验收 → 你在网页工作台里微调"。  
> 哥哥你不用学 Remotion、不用写代码、不用碰命令行——**这些是我（WorkBuddy）干的活**。  
> 你只需要提供两样东西：**口播稿 + 成品配音**。

---

## 0. 一分钟搞懂它是什么

| 它是什么                                  | 它不是什么      |
| ------------------------------------- | ---------- |
| 一个 Agent Skill（SKILL.md 497 行操作手册）    | 不是剪辑软件     |
| 108 张自包含动效配方卡（`template/cards/*.tsx`） | 不是模板素材站    |
| 一套机器验收脚本（静止检测 / 词落点 / 音效在场 / 卡片保真）    | 不是"一键出片"黑盒 |
| 一个 Vite 做的多轨工作台（`workbench/`），像剪映     | 不是必须有真人出镜  |

**它要求"实拍素材"（B-roll / 图片 / 网页截图）占比 ≥ 1/3**，这是硬性验收闸——纯文字动效片会被 `preflight.py` 判 FAIL。这跟你现有的字卡流不一样，得注意。

---

## 1. 硬门槛体检（已实测，2026-11-27）

| 项                 | 结果                                                |
| ----------------- | ------------------------------------------------- |
| Node v22.22.2     | ✅ 满足（要求 18+）                                      |
| FFmpeg N-126386   | ✅ 已装                                              |
| Python 3.13.12    | ✅ 满足（要求 3.10+）                                    |
| sherpa-onnx（时间戳用） | ✅ 有 macOS arm64 wheel，2.0.3，requires_python >=3.7 |
| Remotion          | ✅ runtime 统一钉 4.0.519，独立装，**不污染你现有的 4.0.250 工程**  |
| Fish Audio TTS    | ⚠️ 需要 API Key；可不用，走本机 CPU 对齐                      |
| 火山/FireRed ASR 模型 | ⚠️ 首次要下 767MB；或改用 faster-whisper（自动下 460MB）       |

**已安装位置**：`~/.workbuddy/skills/video-talkcraft/`（66MB 源码 + runtime 依赖约 760MB）

---

## 2. ⚠️ 许可：这是 PolyForm Noncommercial，商用要授权

```
PolyForm Noncommercial 1.0.0
```

- 个人、教育、研究用途：**免费**
- **任何商业用途需事先获得授权**（作者邮箱 <vincentwei1021@gmail.com> 或提 issue）
- **用它做出来的视频归你所有**（这点没问题）

**给你的判断**：你的账号定位是"影视推荐博主 / 短剧推广"，**接商单、带付费推广 = 商业用途**。如果成片本身归你（视频著作权归你），工具的使用是"非商业"还是"商业"取决于你用它谋生与否——边界模糊。  
**稳妥做法**：非商用/个人账号免费用；一旦要接商单或变现，提前发邮件问一句。这封邮件成本为零，别省。

另外它内嵌的音效采样来自 `demos/_lib/sfx/ATTRIBUTION.md`，Remotion 本身是 source-available（个人/≤3 人公司免费）。都归到"商用前一起问"那一封邮件里。

---

## 3. 它和你现有资产的关系（别重复造轮子）

| 你已有的                                            | video-talkcraft 提供的        | 关系                                            |
| ----------------------------------------------- | -------------------------- | --------------------------------------------- |
| `字卡引擎/`（字词时间戳 → 卡型 → JSON）                      | 字级时间戳（ASR 对齐口播稿）           | **思路重叠**，它的是通用版 + 更严的质检                       |
| `HyperFrames字卡工程/`（HTML → 视频）                   | Remotion 渲染 + 108 卡 + 镜头系统 | **不同引擎**，HyperFrames 是纯 HTML，Remotion 是 React |
| `1.数剧_制作_Remotion/`（已有 Remotion 4.0.250 工程）     | 4.0.519 + 统一 runtime       | **版本隔离**，软链机制不会污染你的工程                         |
| `koubo-script-to-video` skill（HyperFrames 竖屏口播） | Remotion 口播 + 工作台          | 两条并行路线，**画幅和风格不同**                            |
| `短剧介绍视频_视觉风格与动效参考_v1.0.md`                      | 108 张卡 + design-language   | **它的卡库就是你那套设计规范的工程化**                         |

**我的判断**：你已有的字卡链路（HyperFrames）跑通了、也出了片（`数剧_短剧榜单_2026-11-25.mp4`，69MB）。  
video-talkcraft 的**增量价值**在三点：

1. **镜头语言**——"七层反 PPT 系统"、极缓推拉相机、让位生命周期，解决你现有片子"像 PPT"的问题
2. **字级配音同步**——每个动效节拍锚在确切的字上（你的字卡引擎已有类似能力，但这个更成熟）
3. **108 张动效配方卡 + 工作台**——现成的动效词汇库 + 剪映式微调界面

**它不能替代的**：你的 HyperFrames 批量出片能力、你的榜单数据流水线、你的 TTS 选型。

---

## 4. 完整八步管线（人机分工）

```
① 口播稿        →  你给 / 或让 AI 起草（数字全汉字，13句≈95秒）
② 配音+时间戳   →  你给成品配音 / AI 用 Fish Audio 生成 / 本机 CPU 对齐
③ 素材          →  AI 找 B-roll、网页长截图、真人素材
④ SHOTBOOK 分镜 →  AI 写，人审（这是唯一的强制人工关卡）
⑤ 实现          →  AI 写 Remotion 代码（首镜先做→你确认→再做其余）
⑥ 渲染          →  AI 跑分段渲染
⑦ 三重验收      →  机器闸 + 独立审片子代理，你只看结论
⑧ 交付          →  打开工作台，你自己微调、导出
```

**你要亲自参与的只有 4 个点**：

1. 提供稿子 + 配音（或授权 AI 生成）
2. 审 SHOTBOOK 分镜（机器闸前唯一人工关）
3. 确认样板镜（第一镜，决定全片基线）
4. 交付时在工作台里微调

---

## 5. 上手三档（按你的时间预算选）

### 档 A：30 分钟验证（推荐先做这个）

**目标**：跑通一个最小样片，确认值得用。

**触发方式**：在 WorkBuddy 对话里直接说——

```
用 video-talkcraft skill 做一个 30 秒的测试片：
主题「短剧推荐号怎么起量」，我给稿子和配音，你走完整八步。
```

或者我直接给你建一个 demo 工程跑通，**只渲首镜 6 秒**，让你看看这个"反 PPT 镜头系统"长什么样，再决定要不要投人力。

### 档 B：半天建工程

1. `bash ~/.workbuddy/skills/video-talkcraft/runtime/check-runtime.sh` ✅ 已在装
2. 准备 `script.json`（口播稿）+ `audio/full.wav`（配音）
3. 跑 `timestamps_cpu.py` → `make_timing.py` → 字级时间戳
4. `semantic_annotate.py --init` → 语义标注
5. `card_match.py` → 选卡候选表
6. 写 SHOTBOOK → `preflight.py` 过闸
7. 实现样板镜 → 渲 6 秒给你看
8. 确认 → 做其余镜头 → 渲染 → 验收 → 工作台交付

### 档 C：直接接你的短剧推广线

你手上有 `短剧介绍视频_视觉风格与动效参考_v1.0.md` 和 `短剧文案/`。  
可以把 **108 张卡的 design-language 和你的视觉规范对齐**（换皮不改运动命门），然后批量给短剧文案出片。

---

## 6. 最容易踩的 5 个坑（我先替你记着）

1. **零实拍素材 → preflight 直接 FAIL**。纯字卡片过不了"实拍占比 ≥1/3"这条闸。短剧素材有版权风险，得用免署名源（Pexels/Pixabay/Mixkit/NASA）。
2. **工程目录里绝对不跑 `npm install`**——会写进共享 runtime 目录，污染所有工程。加包要改 `runtime/package.json`。
3. **制作中途不升级依赖**。母版段缓存按依赖指纹失效不了，混版本渲出来的段会不一致。
4. **每镜必须写「素材：」行**，写"待采"= FAIL。这行是机器读的，格式：`素材：V（public/broll/x.mp4）· 图（public/stills/y.jpg）· 文`。
5. **许可别拖**。商用前发一封邮件问清楚，成本为零。

---

## 7. 关键命令速查

```bash
SKILL=~/.workbuddy/skills/video-talkcraft

# 依赖体检 / 升级（每支新片开工第一件事）
bash $SKILL/runtime/check-runtime.sh

# 字级时间戳（本机 CPU）
pip install zhconv pypinyin sherpa-onnx soundfile numpy
python3 $SKILL/scripts/timestamps_cpu.py audio/full.wav script.json audio/timestamps.json
python3 $SKILL/scripts/make_timing.py audio/timestamps.json remotion/src/timing.json

# 语义标注 + 选卡
python3 $SKILL/scripts/semantic_annotate.py --init
python3 $SKILL/scripts/card_match.py --out qa/card-candidates.md

# SHOTBOOK 过闸
python3 $SKILL/scripts/preflight.py --shotbook SHOTBOOK.md --shots remotion/shots.json ...

# 首镜预览（6 秒带声音）
node $SKILL/scripts/render_shots.mjs --shots shots.json --only s01 --seg-audio --preview-dir out/preview

# 工作台（剪映式，交付面）
cd $SKILL/workbench && bash scripts/link-project.sh <工程根> && npm run dev &
open 'http://localhost:5199/?tracks'
```

---

## 8. 下一步选哪个

| 选项              | 我做什么                                              | 你要做什么                    |
| --------------- | ------------------------------------------------- | ------------------------ |
| **A. 最小验证（推荐）** | 建一个 30 秒 demo 工程，跑通 ②→⑦，只渲首镜 6 秒给你看               | 看一眼，判断"这个镜头系统我要不要"       |
| **B. 直接上你的片子**  | 拿你现有的短剧口播稿 + 配音走完整流程                              | 提供稿子和配音，审 SHOTBOOK，确认样板镜 |
| **C. 先对齐设计规范**  | 把 108 张卡的配色/字体/卡型映射到你的 `短剧介绍视频_视觉风格与动效参考_v1.0.md` | 审设计规范，确认视觉统一             |

**我的建议：选 A。** 理由：这个项目复杂度高（SKILL.md 497 行 + 多道机器闸），跑一次最小验证只要半小时，但能省掉你在正式项目里踩几天的坑。而且它和你现有 HyperFrames 路线是并行的——先用一条片子探清楚边界，再决定要不要把主力切过去。
