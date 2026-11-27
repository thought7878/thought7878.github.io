
## 一句话结论

**video-talkcraft 是一个「口播视频生产流水线」的 AI agent skill**——**给它一份口播稿 + 一条成品配音，它自动对齐字级时间戳、拆分镜、写 Remotion 代码、渲染带动效的解说成片**。不是剪辑软件，不是模板站，是**一套写给 Claude Code / Codex 的方法论 + 素材库 + 自动化闸门**。

数据：2533 star · 252 fork · 创建于 2026-08-22 · 最近推送 2026-11-25 · 仓库 67MB。

---

## 一、它解决什么问题

痛点很具体：*AI 生成的口播视频全是 PPT 感——画面静止、元素乱堆、字卡跟人声对不上*。

它的解法是*用"字级时间戳"当唯一真值，让每个动效节拍都锚在确切的字上*。核心指标：110 秒中英混合口播，字级偏差中位 20–40ms、最差 200ms，质检零误报。


<svg width="100%" viewBox="0 0 680 480" xmlns="http://www.w3.org/2000/svg" role="img">
<title>video-talkcraft 八步生产管线</title>
<desc>从口播稿到交付成片的八个步骤，标出三重验收关卡位置</desc>
<defs>
<marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
<path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</marker>
</defs>

<text x="40" y="30" font-family="var(--font-sans)" font-size="14" font-weight="500" fill="var(--color-text-primary)">video-talkcraft 八步管线</text>

<g>
<rect x="40" y="48" width="140" height="56" rx="8" fill="#E6F1FB" stroke="#378ADD" stroke-width="0.5"/>
<text x="110" y="68" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#0C447C" text-anchor="middle" dominant-baseline="central">① 口播稿</text>
<text x="110" y="86" font-family="var(--font-sans)" font-size="12" fill="#185FA5" text-anchor="middle" dominant-baseline="central">数字写成汉字</text>
</g>
<path d="M180 76 L222 76" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="226" y="48" width="150" height="56" rx="8" fill="#E6F1FB" stroke="#378ADD" stroke-width="0.5"/>
<text x="301" y="68" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#0C447C" text-anchor="middle" dominant-baseline="central">② 预剪 + 字级对齐</text>
<text x="301" y="86" font-family="var(--font-sans)" font-size="12" fill="#185FA5" text-anchor="middle" dominant-baseline="central">偏差中位 20–40ms</text>
</g>
<path d="M376 76 L418 76" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="422" y="48" width="130" height="56" rx="8" fill="#E1F5EE" stroke="#1D9E75" stroke-width="0.5"/>
<text x="487" y="68" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#085041" text-anchor="middle" dominant-baseline="central">③ 素材采集</text>
<text x="487" y="86" font-family="var(--font-sans)" font-size="12" fill="#0F6E56" text-anchor="middle" dominant-baseline="central">V/图/截图/文</text>
</g>

<path d="M487 104 L487 138" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="422" y="142" width="130" height="56" rx="8" fill="#E1F5EE" stroke="#1D9E75" stroke-width="0.5"/>
<text x="487" y="162" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#085041" text-anchor="middle" dominant-baseline="central">④ SHOTBOOK</text>
<text x="487" y="180" font-family="var(--font-sans)" font-size="12" fill="#0F6E56" text-anchor="middle" dominant-baseline="central">语义拍分镜</text>
</g>
<path d="M422 170 L380 170" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="226" y="142" width="150" height="56" rx="8" fill="#FAEEDA" stroke="#BA7517" stroke-width="0.5"/>
<text x="301" y="162" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#633806" text-anchor="middle" dominant-baseline="central">preflight 闸</text>
<text x="301" y="180" font-family="var(--font-sans)" font-size="12" fill="#854F0B" text-anchor="middle" dominant-baseline="central">素材对账，FAIL 挡</text>
</g>
<path d="M226 170 L184 170" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="40" y="142" width="140" height="56" rx="8" fill="#EEEDFE" stroke="#7F77DD" stroke-width="0.5"/>
<text x="110" y="162" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#3C3489" text-anchor="middle" dominant-baseline="central">⑤ 实现</text>
<text x="110" y="180" font-family="var(--font-sans)" font-size="12" fill="#534AB7" text-anchor="middle" dominant-baseline="central">首镜先做先确认</text>
</g>
<path d="M110 198 L110 232" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="40" y="236" width="140" height="56" rx="8" fill="#EEEDFE" stroke="#7F77DD" stroke-width="0.5"/>
<text x="110" y="256" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#3C3489" text-anchor="middle" dominant-baseline="central">⑥ 分段渲染</text>
<text x="110" y="274" font-family="var(--font-sans)" font-size="12" fill="#534AB7" text-anchor="middle" dominant-baseline="central">按镜头切段并行</text>
</g>

<path d="M180 264 L222 264" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<rect x="226" y="222" width="404" height="84" rx="12" fill="#FCEBEB" stroke="#E24B4A" stroke-width="0.5"/>
<text x="242" y="242" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#791F1F">⑦ 三重验收</text>
<g>
<rect x="242" y="254" width="118" height="40" rx="8" fill="#F7C1C1" stroke="#A32D2D" stroke-width="0.5"/>
<text x="301" y="266" font-family="var(--font-sans)" font-size="12" font-weight="500" fill="#501313" text-anchor="middle" dominant-baseline="central">关卡1 机器闸</text>
<text x="301" y="282" font-family="var(--font-sans)" font-size="11" fill="#791F1F" text-anchor="middle" dominant-baseline="central">静止/音效/卡保真</text>
</g>
<g>
<rect x="370" y="254" width="118" height="40" rx="8" fill="#F7C1C1" stroke="#A32D2D" stroke-width="0.5"/>
<text x="429" y="266" font-family="var(--font-sans)" font-size="12" font-weight="500" fill="#501313" text-anchor="middle" dominant-baseline="central">关卡2 独立审片</text>
<text x="429" y="282" font-family="var(--font-sans)" font-size="11" fill="#791F1F" text-anchor="middle" dominant-baseline="central">全新上下文 subagent</text>
</g>
<g>
<rect x="498" y="254" width="118" height="40" rx="8" fill="#F7C1C1" stroke="#A32D2D" stroke-width="0.5"/>
<text x="557" y="266" font-family="var(--font-sans)" font-size="12" font-weight="500" fill="#501313" text-anchor="middle" dominant-baseline="central">关卡3 规则合规</text>
<text x="557" y="282" font-family="var(--font-sans)" font-size="11" fill="#791F1F" text-anchor="middle" dominant-baseline="central">逐镜核八条</text>
</g>

<path d="M428 306 L428 336" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>

<g>
<rect x="358" y="340" width="140" height="56" rx="8" fill="#EAF3DE" stroke="#639922" stroke-width="0.5"/>
<text x="428" y="360" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#27500A" text-anchor="middle" dominant-baseline="central">⑧ 交付</text>
<text x="428" y="378" font-family="var(--font-sans)" font-size="12" fill="#3B6D11" text-anchor="middle" dominant-baseline="central">响度归一 + 工作台</text>
</g>

<path d="M358 368 L318 368" fill="none" stroke="#888780" stroke-width="1.5" marker-end="url(#arrow)"/>
<g>
<rect x="168" y="340" width="146" height="56" rx="8" fill="#F1EFE8" stroke="#888780" stroke-width="0.5"/>
<text x="241" y="360" font-family="var(--font-sans)" font-size="13" font-weight="500" fill="#2C2C2A" text-anchor="middle" dominant-baseline="central">P0/P1 返修</text>
<text x="241" y="378" font-family="var(--font-sans)" font-size="12" fill="#5F5E5A" text-anchor="middle" dominant-baseline="central">只渲受影响段</text>
</g>
<path d="M241 340 L241 320 L110 320 L110 300" fill="none" stroke="#B4B2A9" stroke-width="1.5" stroke-dasharray="4 3" marker-end="url(#arrow)"/>

<path d="M40 424 L640 424" fill="none" stroke="var(--color-border-tertiary)" stroke-width="0.5"/>
<text x="40" y="444" font-family="var(--font-sans)" font-size="12" fill="var(--color-text-secondary)">审片循环最多 3 轮：机器闸全过 → 1 轮独立审片 → 修完 P0/P1 即交付</text>
<text x="40" y="464" font-family="var(--font-sans)" font-size="12" fill="var(--color-text-tertiary)">返修只重渲受影响段 ± 邻段，不整片重渲</text>
</svg>

已经全部拆完了。妹妹给哥哥一个结构化的完整拆解。

---

# video-talkcraft 完整拆解

## 一、它是什么 / 不是什么

| 是                                                                      | 不是                |
| ---------------------------------------------------------------------- | ----------------- |
| 一个 **agent skill**（Claude Code / Codex 技能包），本质是「**方法论 + 素材库 + 自动化脚本**」 | 不是剪辑软件，没有 GUI 主产品 |
| 给**口播稿 + 成品配音**，输出**带动效的解说成片**                                         | 不是模板站，卡片是代码不是 PSD |
| 底层用 **Remotion**（React 写视频）渲染                                          | 不是 AI 生成视频，不做画面生成 |

**数据**：2533 star · 252 fork · 4 个 open issue · 创建 2026-08-22 · 最近推送 2026-11-25 · 仓库 67MB · SKILL.md 497 行 / 62KB。

---

## 二、核心技术：三个真本事

**1. 字级配音同步（整个项目的地基）**

`scripts/timestamps_cpu.py` 把口播稿逐字对齐到音频，输出 `timestamps.json`（含每字 start/end）。默认后端 FireRedASR2-CTC int8（经 sherpa-onnx），备选 faster-whisper。

关键设计：
- **匹配键 = 繁简归一 + 无声调拼音**，所以同音字不算错（"的/得/地"能对上）
- 拉丁词各 ASR 常拼错 → 在 CJK 锚点之间插值
- *长音频按静音切 ≤75s 段*再加回偏移（整段喂会崩、内存平方涨）
- 实测：110s 中英混合，字级偏差**中位 20–40ms，最差 200ms**，质检零误报

**2. 108 张动效配方卡（真正的资产）**

`每张卡` = *意图 + 能量档 + 参数 + 已知坑 + 自包含 Remotion tsx（平均 180 行）+ 可跑 HTML demo*。已核实：磁盘上正好 108 个 `.tsx`，`demos/` 109 项，`references/cards/` 108 个。

主分类（实测计数，含跨批次标记）：

| 类别    | 数量  | *解决什么*          |
| ----- | --- | --------------- |
| 字幕花字  | 15  | 静音刷视频也能接住信息     |
| 强调标注  | 14  | 给听觉重音配视觉重音      |
| 数据信息图 | 14  | 让数字"长"出来而不是贴出来  |
| 素材呈现  | 28  | 证据入场仪式感 + 多素材同屏 |
| 转场结构  | 12  | 章节感（六式运动承接）     |
| 人物互动  | 10  | 真人出镜的信息层        |
| 运镜    | 10  | 让静态截图不死板        |

***选卡是两道过滤***：先按素材类型（V/图/截图/文）过一遍，再按语义（26 词封闭词表）过一遍。*这套机制专门治"一句一元素"的堆积型凌乱*。

**3. 七层反 PPT 系统（理念层）**

每场景一条*极缓推拉相机曲线* + *"让位"生命周期*（主角说完就退场，不堆满屏）+ *六式运动承接转场*。2026-09-04 做过减法：idle 微动 / 环境呼吸 / 脉冲全部默认关闭——**静止帧在结构上不可能出现**。

---

## 三、三重验收（这才是它跟普通模板站的区别）

| 关卡              | 内容                                                                                                                                                                |     |                                                                         |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- | ----------------------------------------------------------------------- |
| **关卡 1 · 机器闸**  | 6 条命令，全 PASS 才进下一关：`motion_check.py`（freezedetect 静止检测 + 重复帧签名查素材帧率病）、`sfx_check.py`（音效在场 ≥−45dBFS + 可听度掩蔽分级）、`card_lint.py`（卡片保真，查回弹/拍击/密度丢失）、`beat_lint.py`（词落点 | Δ   | ≤0.1s + 镜尾保护带）、`workbench_contract_lint.py`（工作台契约）、`preflight.py`（素材对账） |
| **关卡 2 · 独立审片** | 必须派**全新上下文**的 subagent，禁止制作者自评、禁止 fork 制作对话当评审（避免自证闭环）。出 P0/P1/P2 清单，修完 P0+P1 才算过                                                                                 |     |                                                                         |
| **关卡 3 · 规则合规** | 逐镜核八条 + 交付前终检（关调试 overlay、成片缩到 390px 宽还能读）                                                                                                                        |     |                                                                         |

审片循环封顶 3 轮，且**返修只重渲受影响段 ± 邻段**，不整片重渲。

---

## 四、附带一个完整工作台

`workbench/` 是剪映式界面（191 个 ts/tsx 文件，Vite + React 19）：
- 多轨时间线 + 素材库四 tab（素材 / 动效库 108 张 / 音效 33 个 / 背景）
- **108 张卡 100% 参数化**（*文案、颜色、字号、位置可调*），但*动效节奏命门 FIXED 不暴露*
- 变速 0.25×–4×、裁入点、不透明度缩放位移
- 一键把成片拆成*字幕 / 转场 / 环境 / 镜头 / 配音 / 音效多轨*
- 右键导出透明通道（ProRes 4444 / VP9 alpha），给剪映 / PR / AE 当叠加素材
- 实时看板：阶段栏 + 进度轨（*占位/已实现/已渲/已过闸/过期/P0-P1*）

---

## 五、上手成本与硬门槛

```bash
# 最省事：把链接丢给 agent
"帮我安装这个 skill：https://github.com/Vincentwei1021/video-talkcraft"

# 或
npx skills add Vincentwei1021/video-talkcraft
```

**依赖**：Node 18+ · Python 3.10+ · ffmpeg · `pip install zhconv pypinyin sherpa-onnx soundfile numpy`（首次下 **767MB** FireRed 模型；或用 `--backend whisper` 免手动下）· `pip install requests python-dotenv`（走 Fish Audio 生成配音时）

**统一依赖设计**：`runtime/node_modules` 一份软链给所有工程（760MB），省掉"每支片各装一份、六个工程 3.9GB"。新片开工先跑 `bash runtime/check-runtime.sh --upgrade`。

---

## 六、⚠️ 三个必须知道的坑

**1. 许可卡在商业用途上** — 仓库用 **PolyForm Noncommercial 1.0.0**：个人/教育/研究免费，**任何商业使用需事先获得作者授权**（vincentwei1021@gmail.com）。做出来的视频归你，但**用它做商业内容要先拿授权**。

**2. README 自称 108 张卡，实际磁盘 108 张，但仓库 og 描述写的是 109** — 文档数字没同步，小瑕疵，说明作者迭代很勤。

**3. 别在制作中途升级依赖** — 母版段缓存不认依赖版本，混版本渲出的段会不一致（作者自己写进 SKILL.md 的硬规则）。

---

## 七、对哥哥的实际价值判断

**直接相关。** 哥哥在做的短剧推广二创解说视频，正好是这套 skill 的靶心场景。

但有三处要改：
1. **画幅**：skill 默认横屏 1920×1080，短剧要竖屏 1080×1920 → 需在 ⓪ 开工体检时改
2. **许可**：短剧推广是商业用途，**必须先找作者授权**
3. **素材**：短剧素材是剧集视频，属于 V 类；skill 硬性要求 V/图 镜头占比 ≥1/3，否则 preflight 直接 FAIL——哥哥的剧集素材反而是优势

**适用度最高的两个模块**：`evidence-scroll-tour` 类证据巡游卡（放大镜 / 巡游 / 划重点），和 `voice_trim.py` 配音预剪（剪口水词后重算时间戳）——后者对哥哥的口播文案迭代很有用。

**能省多少**：作者实测 201 秒竖屏片，首渲 13min → 9min，改一个镜头出有声新片从整渲 53s → **43 张静帧抽样 11min → ~1min**，评审读 QA 帧从 16 万 token → 4 万。

---

需要妹妹做哪一步？可以给你：
- **A** 把 skill 装到本机（`~/.workbuddy/skills/`），跑通一条竖屏短剧样片
- **B** 只精读某个模块（时间戳对齐 / 卡片体系 / 工作台 / 三重验收），出一份带行号引用的源码解读
- **C** 对比姊妹项目 `video-shotcraft`，看哪些卡该移植过来