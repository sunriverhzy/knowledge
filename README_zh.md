# AI 研究 Wiki

一个由 LLM 维护的 AI 研究个人知识库，覆盖
**LLM**、**RL**、**AIGC**、**多模态**、**Agent**、**奖励建模**等方向。

按照 Andrej Karpathy 的 **LLM Wiki** 模式构建：
> **在摄取时编译知识，查询时只读已编译的 Wiki —— 不再重复检索原始资料。**

---

## 为什么不直接用普通 RAG？

普通 RAG 在查询时从原始文本中检索"片段"。而 Wiki 是由 LLM 在**摄取时**
**预先编译**好的：概念被合并、对比被绘出、交叉引用被插入。

一个摄取了 50 篇 Transformer 论文的 Wiki，回答相关问题时会有**复利级**
**的深度** —— 这是同样 50 篇论文上的 RAG 系统达不到的，因为 Wiki **整合**
了知识，而 RAG 只能**检索**最相似的片段。

---

## 三层架构

```
┌──────────────────────────────────────────┐
│  第 3 层：Schema  （CLAUDE.md）          │  ← 规则、模板、工作流
├──────────────────────────────────────────┤
│  第 2 层：Wiki    （wiki/*）             │  ← LLM 生成、相互链接
├──────────────────────────────────────────┤
│  第 1 层：Sources （sources/*, raw/*）   │  ← 原始资料，不可修改
└──────────────────────────────────────────┘
```

- **Sources** —— 不可修改的原始论文，保存后不再编辑。
- **Wiki** —— 完全可重新生成；由 LLM 撰写和维护。
- **Schema** —— 即 `CLAUDE.md`，由你亲自维护。

---

## 目录结构

```
paper_research/
├── CLAUDE.md                   # 🧠 Schema：LLM 操作手册
├── README.md                   # 本文件
│
├── raw/                        # 原有的原始论文归档（只读）
│
├── sources/                    # 第 1 层：不可修改的原始资料
│   ├── inbox/                  # 把新论文放在这里等待摄取
│   └── papers/
│       ├── llm/
│       ├── rl/
│       ├── aigc/
│       ├── multimodal/
│       ├── agent/
│       ├── reward-modeling/
│       └── other/
│
└── wiki/                       # 第 2 层：LLM 维护的知识库
    ├── index.md                # 导航中枢（每次摄取都要更新）
    ├── overview.md             # 领域全局综述
    ├── papers/                 # 每篇论文一个页面，按领域组织
    ├── concepts/               # 每个概念一个页面
    ├── authors/                # 每位重要研究者一个页面
    ├── venues/                 # 每个会议/期刊一个页面
    ├── comparisons/            # 方法间对比页面
    ├── timelines/              # 子领域研究时间线
    └── reading-lists/          # 策划的阅读路径
```

---

## 四大核心操作

| 操作 | 触发方式 | 作用 |
|------|----------|------|
| **Init（初始化）** | 首次运行 | 生成目录骨架和占位文件。读到本文件说明你已完成这一步。 |
| **Ingest（摄取）** | "请摄取 `sources/inbox/` 中的论文" | 对每篇新论文执行 `CLAUDE.md` 中的 10 步工作流。 |
| **Query（查询）** | 任意提问 | 先读 `wiki/index.md`，再加载相关 Wiki 页面（不读原始源文件），并引用页面作答。 |
| **Lint（巡检）** | "请对 Wiki 做 lint" | 报告断链、孤立页、陈旧论断、缺失对比页等问题。 |

---

## 快速上手

### 第 1 步 —— 在一个有能力的 LLM 工具中打开本项目

推荐：**Claude Code**、**Cursor** 或 **Codebuddy** —— 任何能读写本地文件、
且能完整读入 `CLAUDE.md` 的工具。

### 第 2 步 —— 加入你的第一篇论文

把一个 PDF 或 Markdown 文件丢进 `sources/inbox/`，然后问 LLM：

> 请阅读 `CLAUDE.md`，按照其中的 Ingest Workflow
> 摄取 `sources/inbox/` 中的论文。

### 第 3 步 —— 查询 Wiki

当 Wiki 中有了几篇论文以后，就可以问真实的问题了：

> 总结一下奖励建模的演进历程，并引用 Wiki 页面。

> 为一个刚入门 LLM Agent 的人制定一份阅读清单。

> 对比 DPO 和 PPO 在 LLM 对齐中的差异 —— Wiki 里有什么？

### 第 4 步 —— 定期 Lint

大约每摄取十来篇论文做一次：

> 请对 Wiki 执行 `/lint`，并按严重程度输出报告。

🔴 严重 的问题立刻修掉；🟡 警告 和 🟢 建议 可从容处理。

---

## 推荐工具链

| 层次 | 工具 | 用途 |
|------|------|------|
| 文件编辑 | **Obsidian** | 原生支持 `[[wikilink]]`，有关系图谱 |
| LLM 引擎 | **Claude Code / Cursor / Codebuddy** | 读写本地文件，执行 ingest/lint |
| 版本控制 | **Git（私有仓库）** | 每次摄取 diff 可查，出错可回滚 |
| PDF → Markdown | **marker**、**pypdf2**、**pymupdf** | 把 PDF 预处理成清洁文本 |
| （可选）图谱分析 | **InfraNodus** | 概念聚类、盲点检测 |

---

## 如何使用原有的 `raw/` 目录

你原先的 `raw/` 目录（里面有约 57 个 PDF）保持不动。你有两种选择：

1. **渐进迁移** —— 小批量地把论文从 `raw/` 拷贝到 `sources/inbox/`，
   一篇一篇地摄取。这种方式更稳妥；可以一边走一边调整标签词汇和规范。
2. **批量摄取** —— 直接让 LLM 指向 `raw/` 做大批量摄取。速度快，但要认真
   审查产出。

建议：**从方式 1 开始**，挑 2–3 篇主题相近的论文先摄取，让概念页骨架
被"慢慢搭好"。

---

## 文件命名约定 —— 论文 slug

`{第一作者姓氏}-{年份}-{关键词}`

示例：
- `vaswani-2017-attention`
- `ouyang-2022-instructgpt`
- `rafailov-2023-dpo`

尽量让源文件名与 slug 对齐：

```
sources/papers/llm/ vaswani-2017-attention-is-all-you-need.pdf
wiki/papers/llm/    vaswani-2017-attention.md
```

---

## 双语文档

**本 Wiki 中的每个 Markdown 文件都有一个对应的中文版本，文件名以
`_zh` 结尾。** 这包括 Schema 文件、README、index，**以及每一个 Wiki
页面** —— 论文页、概念页、对比页等等。

例如：

- `CLAUDE.md` ↔ `CLAUDE_zh.md`
- `README.md` ↔ `README_zh.md`
- `wiki/index.md` ↔ `wiki/index_zh.md`
- `wiki/papers/reward-modeling/hong-2025-think-rm.md` ↔ `…_zh.md`
- `wiki/concepts/reward-variance.md` ↔ `wiki/concepts/reward-variance_zh.md`

以**英文文件为真相来源（source of truth）**，`_zh` 文件是忠实翻译。
**如果更新一方，请同时更新另一方。**

因此，摄取一篇新论文时，LLM 必须创建：

1. 英文页面（`{slug}.md`）—— 来自源论文的主体内容。
2. 对应的中文版（`{slug}_zh.md`）—— 忠实翻译。
3. 摄取过程中新建的任何 概念 / 作者 / 会议 / 对比 页面也遵循同样规则。

---

## 延伸阅读 —— 了解 LLM Wiki 模式

- Andrej Karpathy 的原始 gist —— *[llm-wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)*
- *Beyond RAG: How Andrej Karpathy's LLM Wiki Pattern Builds Knowledge That Actually Compounds* —— Level Up Coding
- *How I Took Karpathy's LLM Wiki and Built an AI-Powered Second Brain in Obsidian* —— AI Maker Substack

---

## 许可

个人知识库 —— 除非你另有说明，保留所有权利。
`sources/` 和 `raw/` 中的论文保留其原有许可证。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
