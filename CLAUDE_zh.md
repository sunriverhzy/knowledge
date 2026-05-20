# AI 研究 Wiki —— Schema 与操作手册

> 本文件是本知识库的**唯一真相来源（single source of truth）**，
> 规定了 LLM 应该如何构建、维护和查询这个 Wiki，是整个 Wiki 的"大脑"。
>
> 指导哲学（源自 Andrej Karpathy 的 LLM Wiki 模式）：
> **在摄取时编译知识，查询时只读已编译的 Wiki —— 不再重复检索原始资料。**

---

## 🎯 目的

本 Wiki 是一个 AI 研究人员的个人知识库，覆盖以下领域的论文与概念：

- **LLM** —— 大语言模型（架构、训练、推理）
- **RL** —— 强化学习，以及 RLHF / RLAIF
- **AIGC** —— AI 生成内容（扩散模型、生成、水印）
- **多模态** —— VLM、视频理解、时序定位（grounding）
- **Agent** —— 基于 LLM 的智能体、多智能体系统、GUI Agent
- **奖励建模** —— 奖励模型、偏好学习、对齐
- **理论与方法** —— 优化、Scaling Law、评测

目标是：**在摄取时编译知识**、**查询时读取已编译的 Wiki**，
而不是每次都从原始文档中重新检索。

---

## 🏛️ 三层架构

```
┌──────────────────────────────────────────┐
│  第 3 层：Schema   （本文件 CLAUDE.md）  │  ← 规则、模板、工作流
├──────────────────────────────────────────┤
│  第 2 层：Wiki     （wiki/*）            │  ← LLM 生成、相互链接
├──────────────────────────────────────────┤
│  第 1 层：Sources  （sources/*, raw/*）  │  ← 原始资料，不可修改
└──────────────────────────────────────────┘
```

- **Sources** 不可修改，保存后就不要再编辑。
- **Wiki** 可从 Sources + Schema 完全重新生成。
- **Schema** 由人类持有者亲自维护。

---

## 📂 目录结构

| 路径 | 说明 |
|------|------|
| `CLAUDE.md` | 本 Schema 文件。 |
| `README.md` | 面向人类的快速上手指南。 |
| `raw/` | 原始论文归档（只读、预先存在）。 |
| `sources/` | 整理过的 source 层。 |
| `sources/papers/{domain}/` | 按领域组织的论文。 |
| `sources/inbox/` | 等待摄取的新论文暂存区。 |
| `wiki/` | 由 LLM 维护的知识库，其中所有文件都由 LLM 生成/更新。 |
| `wiki/index.md` | 内容目录 —— 导航中枢，每次摄取都要更新。 |
| `wiki/overview.md` | 领域全局综述与知识地图。 |
| `wiki/papers/{domain}/` | 每篇论文一个页面。 |
| `wiki/concepts/` | 每个概念或技术一个页面。 |
| `wiki/authors/` | 每位重要研究者一个页面。 |
| `wiki/venues/` | 每个会议/期刊一个页面。 |
| `wiki/comparisons/` | 方法/模型对比页面。 |
| `wiki/timelines/` | 各子领域按时间线排列的研究史。 |
| `wiki/reading-lists/` | 按主题策划的阅读路径。 |

**Domain 目录的可选值**（小写、kebab-case）：
`llm`、`rl`、`aigc`、`multimodal`、`agent`、`reward-modeling`、`other`

---

## 🔗 链接规则

- 所有内部交叉引用一律使用 `[[页面标题]]`（wikilink）。
- 每个**论文页**必须至少链接到一个概念页。
- 每个**概念页**必须至少链接到一篇论文页。
- 正文中引用一篇论文时，使用其 slug：`[[vaswani-2017-attention]]`。
- 当一个概念或命名实体在某个页面中**首次出现**时，必须加上链接。
- 有用时优先链接到特定小节：`[[页面标题#小节]]`。
- YAML 里的 `aliases` 允许把别名解析到规范页面。

---

## 🏷️ 打标签规则

所有页面**必须**包含 YAML frontmatter。标签应从下面的受控词汇表中选取。
**如果确有需要可以新增标签**，但必须在 `wiki/index.md` 的 **Tag Registry**
中登记。

**领域（domain）标签**
`llm`、`rl`、`aigc`、`multimodal`、`agent`、`reward-modeling`、`theory`、
`nlp`、`cv`、`robotics`、`alignment`、`efficiency`、`watermark`、`ocr`、`video`

**方法（method）标签**
`transformer`、`diffusion`、`rlhf`、`rlaif`、`ppo`、`dpo`、`grpo`、`sft`、
`pretraining`、`finetuning`、`inference`、`attention`、`moe`、`rag`、
`prompt-engineering`、`scaling-law`、`self-play`、`test-time`、
`trajectory-optimization`、`tool-use`、`planning`、`multi-agent`、
`gui-agent`、`web-agent`、`search-agent`、`reward-model`

**会议/期刊（venue）标签**
`neurips`、`icml`、`iclr`、`acl`、`emnlp`、`cvpr`、`iccv`、`eccv`、
`arxiv`、`nature`、`science`、`aaai`、`kdd`、`www`

**状态（仅论文）标签**
`read`（已读）、`skimmed`（略读）、`queued`（待读）、`key-paper`（关键论文）、
`revisit`（需重读）

---

## 📋 页面模板

### 模板 A —— 论文页

- **文件路径：** `wiki/papers/{domain}/{slug}.md`
- **Slug 格式：** `{第一作者姓氏}-{年份}-{关键词}`
  - 示例：`vaswani-2017-attention`、`ouyang-2022-instructgpt`

```markdown
---
title: "[论文全名]"
slug: "[第一作者姓氏]-[年份]-[关键词]"
authors:
  - "[作者 1]"
  - "[作者 2]"
year: [YYYY]
venue: "[会议/期刊 + 年份，例如 NeurIPS 2024]"
arxiv: "[arxiv ID 或 URL]"
domain: [llm | rl | aigc | multimodal | agent | reward-modeling | other]
tags: [tag1, tag2, ...]
status: [read | skimmed | queued | key-paper]
ingested: [YYYY-MM-DD]
---

# [论文全名]

## TL;DR
> [一句话：这篇论文做了什么，为什么重要。]

## Problem Statement（问题陈述）
[这篇论文具体解决了什么问题？填补了先前工作中的哪个空白？要精确。]

## Key Contributions（主要贡献）
1. [贡献 1]
2. [贡献 2]
3. [贡献 3]

## Method Overview（方法概述）
[核心方法。如果理解关键的话，写出核心公式。先给直觉，再写形式化内容。]

### Architecture / Algorithm（架构 / 算法）
[如适用：描述模型架构或算法。]

### Training Objective（训练目标）
[如适用：描述损失函数或训练流程。]

## Experiments & Results（实验与结果）
[使用的主要 benchmark、主要结果、对比的 baseline。]

| Benchmark | 本文 | 之前 SOTA | Δ |
|-----------|------|----------|---|
| [名称]    | [分数] | [分数]  | +X% |

## Strengths（优点）
- [这篇论文做得非常好的地方是什么？]

## Limitations & Weaknesses（局限 / 不足）
- [作者自己承认的局限]
- [阅读过程中识别出的局限]

## Open Questions（开放性问题）
- [这篇论文提出但未回答的问题]
- [未来工作的想法]

## Connections（关联）
- **Builds on（基于）：** [[概念或论文]], [[概念或论文]]
- **Compared with（对比）：** [[论文]]
- **Extended by（被扩展）：** [[论文]] *（随 wiki 增长填入）*
- **Related concepts（相关概念）：** [[概念]], [[概念]]
- **Same author's other work（同作者其他工作）：** [[论文]]

## Personal Notes（个人笔记）
[你自己的批注、批判性思考、由论文激发的想法。]

## BibTeX
```bibtex
@article{...}
```
```

---

### 模板 B —— 概念页

- **文件路径：** `wiki/concepts/{slug}.md`
- **Slug 格式：** `{概念名 kebab-case}`
  - 示例：`reinforcement-learning-from-human-feedback`

```markdown
---
title: "[概念名称]"
aliases: ["[别名 1]", "[别名 2]"]
domain: [llm | rl | aigc | multimodal | agent | reward-modeling | theory]
tags: [tag1, tag2]
source_count: [N]   # Wiki 中讨论该概念的论文数量
last_updated: [YYYY-MM-DD]
---

# [概念名称]

## Definition（定义）
[清晰、精确的定义，一段话。面向专家读者，但不依赖原论文即可理解。]

## Intuition（直觉）
[该概念背后的核心直觉；如果有帮助，可以用比喻。]

## Historical Context（历史背景）
[这个概念是什么时候、以什么方式出现的？它当时在解决什么问题？]

## Key Variants / Subtypes（关键变体 / 子类型）
- **[变体 A]：** [描述]
- **[变体 B]：** [描述]

## Technical Details（技术细节）
[公式、算法或形式化定义，按需撰写。]

## Key Papers（关键论文）
*（按重要性或时间顺序排列）*

| Paper | Year | Contribution |
|-------|------|-------------|
| [[paper-slug]] | YYYY | [这篇论文对该概念的贡献] |

## Comparison with Related Concepts（与相关概念的对比）

|               | 本概念 | [[相关概念 A]] | [[相关概念 B]] |
|---------------|--------|----------------|----------------|
| [维度 1]      |        |                |                |
| [维度 2]      |        |                |                |

## Current Research Frontiers（当前研究前沿）
[该概念的开放问题与活跃研究方向。]

## See Also（参见）
- [[related-concept-1]]
- [[related-concept-2]]
```

---

### 模板 C —— 作者页

- **文件路径：** `wiki/authors/{slug}.md`
- **Slug 格式：** `{firstname-lastname}`（小写，kebab-case）
  - 示例：`ilya-sutskever`

```markdown
---
title: "[全名]"
affiliation: "[当前机构]"
domain: [主要研究方向的标签]
homepage: "[主页 URL（如已知）]"
---

# [全名]

## Research Focus（研究方向）
[主要研究兴趣与贡献，2–3 句。]

## Papers in This Wiki（本 Wiki 中的论文）
- [[paper-slug]] —— [一句话描述]
- [[paper-slug]] —— [一句话描述]

## Notable Contributions to the Field（对领域的显著贡献）
[本 Wiki 之外，他们广为人知的贡献是什么？]

## Key Collaborators (in this wiki)（关键合作者 —— 本 Wiki 范围内）
- [[author-name]]

## Research Group / Lab（研究组 / 实验室）
[实验室名称及链接（如适用）]
```

---

### 模板 D —— 对比页

- **文件路径：** `wiki/comparisons/{slug}.md`
- **Slug 格式：** `{方法A}-vs-{方法B}` 或 `{主题}-comparison`

```markdown
---
title: "[方法 A] vs [方法 B]"
domain: [领域标签]
papers: ["[[paper-a]]", "[[paper-b]]"]
last_updated: [YYYY-MM-DD]
---

# [方法 A] vs [方法 B]

## One-Line Summary（一句话结论）
[一句话说明：什么情况下用 A，什么情况下用 B。]

## Comparison Table（对比表）

| 维度               | [[方法 A]] | [[方法 B]] |
|--------------------|-----------|-----------|
| 核心思想           |           |           |
| 架构               |           |           |
| 训练数据           |           |           |
| 计算成本           |           |           |
| Benchmark 表现     |           |           |
| 可扩展性           |           |           |
| 关键局限           |           |           |

## Detailed Analysis（详细分析）

### Method A 擅长之处
[...]

### Method B 擅长之处
[...]

### 混合或后续方法
- [[paper]] —— [描述]

## Recommendation（建议）
[明确的选型指引：什么情况下选哪个。]
```

---

### 模板 E —— 会议/期刊页

- **文件路径：** `wiki/venues/{slug}.md`
- **Slug 格式：** `{会议缩写}`（例如 `neurips`、`iclr`）

```markdown
---
title: "[会议/期刊全名]"
abbreviation: "[缩写]"
type: [conference | journal | workshop]
domain: [主要领域]
---

# [会议/期刊全名]

## Overview（概述）
[简要描述该会议/期刊的范围与地位。]

## Papers in This Wiki (by year)（按年份列出本 Wiki 中的论文）

### [年份]
- [[paper-slug]] —— [一句话描述]

## Notable Trends（值得关注的趋势）
[从本 Wiki 中观察到的重复出现的主题或趋势。]
```

---

### 模板 F —— 阅读清单页

- **文件路径：** `wiki/reading-lists/{topic}.md`

```markdown
---
title: "Reading List: [主题]"
domain: [领域标签]
difficulty: [beginner | intermediate | advanced]
last_updated: [YYYY-MM-DD]
---

# Reading List: [主题]

## Purpose（目的）
[这份阅读清单是给谁看的？读完会有什么收获？]

## Prerequisite Concepts（前置概念）
- [[concept-1]]
- [[concept-2]]

## Stage 1 —— Foundations（第一阶段 —— 基础）
1. [[paper-slug]] —— *[为什么从这里开始]*
2. [[paper-slug]] —— *[它增加了什么]*

## Stage 2 —— Core Methods（第二阶段 —— 核心方法）
1. [[paper-slug]] —— *[为什么要读]*

## Stage 3 —— Frontier Research（第三阶段 —— 前沿研究）
1. [[paper-slug]]

## Key Concepts to Master Along the Way（一路上要掌握的关键概念）
- [[concept-1]]
- [[concept-2]]
```

---

## ⚙️ Ingest 工作流 —— 逐步说明

当被要求摄取一篇新论文（通常位于 `sources/inbox/` 或 `raw/`）时，
严格按照以下流程执行：

1. **完整阅读**源文件（PDF 文本、摘要或已转换好的 Markdown）。
2. **创建**论文页面 `wiki/papers/{domain}/{slug}.md`，使用**模板 A**。
3. **识别**论文中提到的所有关键概念。对每个概念：
   - 如果 `wiki/concepts/{concept-slug}.md` 已存在 → **更新**它
     （把本论文加入 *Key Papers* 表，如有可能丰富其内容）。
   - 如果不存在 → 使用**模板 B** **创建**新页。
4. **识别**所有作者。对于在本 Wiki 已有 2 篇及以上论文的作者：
   - 如果作者页已存在 → 把本论文加入其列表。
   - 如果不存在 → 使用**模板 C** 创建作者页。
5. **更新** `wiki/venues/{venue-slug}.md`，把本论文列入。
6. **添加交叉引用：** 扫描现有 Wiki 页面中提及本论文关键概念的地方，
   在合适处插入 `[[wikilink]]`。
7. **更新** `wiki/index.md`：在正确的领域小节中登记新论文。
8. **判断**是否需要更新 `wiki/overview.md`
   （仅对里程碑论文或重大新方向更新）。
9. **移动**源文件：从 `sources/inbox/` 移动到 `sources/papers/{domain}/`。
10. **双语镜像** —— 对第 2–8 步中**新建或更新**的**每一个** Markdown
    文件（论文页、概念页、作者页、会议页、对比页、`index.md`、
    `overview.md`），都要同时新建/更新其中文对应文件
    `<filename>_zh.md`。中文版是英文版的**忠实翻译**：保留所有 YAML
    frontmatter、所有 `[[wikilink]]`、所有 slug、所有公式、所有表格
    完全一致 —— 只翻译自然语言散文。**英文文件是 source of truth。**
11. **汇报**所有创建/更新的页面，格式如下：

```
✅ "[论文标题]" 摄取完成
   • 新建：wiki/papers/{domain}/{slug}.md           (+ {slug}_zh.md)
   • 更新：wiki/concepts/{c1}.md                    (+ {c1}_zh.md)
   • 新建：wiki/concepts/{c3}.md                    (+ {c3}_zh.md)
   • 更新：wiki/index.md                            (+ wiki/index_zh.md)
   • 移动：sources/inbox/x.pdf → sources/papers/{domain}/x.pdf
```

---

## 🔍 查询行为

回答 Wiki 相关问题时：

1. **首先加载 `wiki/index.md`**，了解有哪些内容。
2. **加载相关的 Wiki 页面**（论文页、概念页、对比页）—— **不要**读原始源文件。
3. **基于已综合的 Wiki 内容作答。**
4. 如果一个好答案需要跨 3 个或以上 Wiki 页面的组合，
   **主动提议**把它存为新页面，放进 `wiki/reading-lists/` 或 `wiki/comparisons/`。
5. **始终引用具体页面：** "根据 [[paper-slug]] ……"。
6. 如果 Wiki 中信息不足，**明确说明**，并指出哪篇论文页或概念页可以填补空白。

---

## 🩺 Lint 规则

当被要求对 Wiki 做 lint（`/lint`）时，按以下项目检查，
并按严重程度分类输出报告 —— 🔴 严重 / 🟡 警告 / 🟢 建议：

1. **🔴 断链** —— `[[wikilink]]` 指向不存在的页面。
2. **🔴 论文页不完整** —— 缺少必需小节（TL;DR、Key Contributions、Connections）。
3. **🔴 索引失同步** —— `wiki/papers/` 中存在但未登记到 `wiki/index.md` 的论文。
4. **🟡 孤立页面** —— 没有任何其他页面用 `[[链接]]` 指向它的 Wiki 页面。
5. **🟡 缺失概念页** —— 在论文页中被正文提到，但没有自己独立页面的概念。
6. **🟡 陈旧论断** —— 某些论文页的 *Current Research Frontiers* 小节
   可能因新摄取的论文而过时。
7. **🟢 未建作者页** —— 本 Wiki 中已有 2 篇及以上论文但未建作者页的作者。
8. **🟢 缺失对比页** —— 在 3 个及以上论文页中被互相对比，但没有独立对比页
   的方法对。

---

## 📊 `wiki/index.md` 标准结构

```markdown
# AI Research Wiki —— 索引

*最后更新：[YYYY-MM-DD] ｜ 论文：N ｜ 概念：N ｜ 作者：N*

---

## 论文

### LLM
| Slug | 标题 | 年份 | 会议 | 标签 | 状态 |
|------|------|------|------|------|------|
| [[slug]] | 标题 | 2024 | NeurIPS | tag1, tag2 | read |

### 强化学习
...

### AIGC
...

### 多模态
...

### Agent
...

### 奖励建模
...

---

## 概念
| Slug | 标题 | 领域 | 来源数量 |
|------|------|------|----------|

## 作者
| Slug | 姓名 | 单位 | 论文数 |
|------|------|------|--------|

## 对比
| Slug | 标题 | 领域 |
|------|------|------|

## 阅读清单
| Slug | 标题 | 难度 |
|------|------|------|

## 标签登记处（Tag Registry）
| Tag | 说明 | 页面数 |
|-----|------|--------|
```

---

## 🧭 四大核心操作 —— 速查表

| 操作 | 触发方式 | 动作 |
|------|----------|------|
| **Init（初始化）** | 首次运行 | 阅读 `CLAUDE.md`，建好目录骨架，生成空的 `index.md` / `overview.md`。 |
| **Ingest（摄取）** | 输入 `/ingest` 或 "ingest X" | 对 `sources/inbox/` 中的每篇论文执行上面的 10 步流程。 |
| **Query（查询）** | 任意提问 | 按"查询行为"规则作答；引用 Wiki 页面。 |
| **Lint（巡检）** | 输入 `/lint` | 执行所有 Lint 规则，按严重程度输出报告。 |

---

## 📝 操作原则

1. **永远不要修改 `sources/` 和 `raw/` 下的文件。**
2. **Wiki 页面必须有 YAML frontmatter。**
3. **优先更新已有页面，不要创建重复页面。**
4. **论文页中的每个断言都应能回溯到源论文。**
5. **概念页必须可以独立阅读** —— 读者不必先去读原论文。
6. **`index.md` 必须同步** —— 它是每次查询的入口。
7. **每次摄取后 commit 一次**（如使用 Git），让知识演化可 diff。
8. **如不确定，就简洁。** 一篇短而正确的 Wiki 页面，胜过一篇长而含糊的。
