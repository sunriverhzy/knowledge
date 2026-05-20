# `sources/` —— 不可修改的原始资料

本目录是三层架构的**第 1 层**。

> **规则：保存后不再编辑 `sources/` 中的文件。**
> 如果一篇论文有更新（v2、v3 等），就把新版本作为独立文件加入。

## 目录结构

```
sources/
├── inbox/                 # 暂存区：等待摄取的新论文
└── papers/                # 归档目录（摄取完成后）
    ├── llm/
    ├── rl/
    ├── aigc/
    ├── multimodal/
    ├── agent/
    ├── reward-modeling/
    └── other/
```

## 加入一篇论文的两种方式

### 方式 1 —— 丢入 `inbox/`

1. 把 PDF（或 Markdown 抽取版）放到 `sources/inbox/`。
2. 让 LLM 执行：*"请摄取 `sources/inbox/` 中的论文"*。
3. LLM 按照 `CLAUDE.md` 中定义的 10 步摄取流程执行。
4. 摄取完成后，文件会被移动到 `sources/papers/{domain}/`。

### 方式 2 —— 从已有归档做批量摄取

如果你已经把论文组织好了（例如项目根目录下的 `raw/`），可以：

- **原地引用**：把绝对路径告诉 LLM，要求它**不移动**、只基于这些文件摄取。
- **迁移**：先把文件移动/拷贝到 `sources/papers/{domain}/`，再做批量摄取。

项目根目录下既有的 `raw/` 目录可以保留作未整理的归档 —— 除非你明确指示，
否则摄取流程不会碰它。

## 命名建议

`sources/papers/` 下的文件名应当可读，但**不必**与 Wiki slug 完全一致。
例如：

```
sources/papers/llm/  vaswani-2017-attention-is-all-you-need.pdf
wiki/papers/llm/     vaswani-2017-attention.md
```

保留同样的 **slug 前缀**（`vaswani-2017-`）在源文件与 Wiki 页面之间
跳转就非常方便。

## 什么东西**不**应该放在这里

- 抽取的笔记、摘要、高亮 → 放进 `wiki/papers/`。
- 你自己的批注 → 放进 `wiki/papers/<paper>.md` 的 `Personal Notes` 小节。
- 重组过的、带叙事性的内容 → 放进 `wiki/overview.md`、
  `wiki/timelines/`、`wiki/reading-lists/`。

## 接受的格式

- `.pdf` —— 一个能读 PDF 的 LLM / Agent 可以直接处理。
- `.md` —— **首选**。如果你已用 `marker`、`pypdf` 或同类工具把论文
  转成 Markdown，请用这种格式。
- `.txt` —— 纯文本抽取也可以。
- `.html` —— 适合博客文章和技术报告。

## 摄取前检查清单

在请求 LLM 摄取之前，请确认：

- [ ] 文件已在 `sources/inbox/`（或你将显式提供其路径）。
- [ ] 你知道该论文属于哪个 `domain`（或交给 LLM 判断）。
- [ ] `wiki/index.md` 已存在（如有必要，先执行 `init`）。
