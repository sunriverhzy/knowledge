# `wiki/papers/` —— 论文页

每篇论文对应一个 Markdown 页面，按领域组织。

## 目录结构

```
papers/
├── llm/              # 大语言模型论文
├── rl/               # 强化学习论文
├── aigc/             # AI 生成内容论文
├── multimodal/       # VLM / 视频 / 多模态论文
├── agent/            # 基于 LLM 的 Agent 论文
├── reward-modeling/  # 奖励模型 / 偏好学习论文
└── other/            # 不适合归入以上任何分类的论文
```

## 页面格式

使用 `CLAUDE.md` 中的**模板 A**。每个论文页**必须**包含：

1. YAML frontmatter（title、slug、authors、year、venue、tags、status、ingested）。
2. `TL;DR` —— 一句话总结。
3. `Problem Statement`、`Key Contributions`、`Method Overview`。
4. `Experiments & Results`。
5. `Strengths`、`Limitations & Weaknesses`、`Open Questions`。
6. `Connections` —— 至少有一个 `[[wikilink]]` 指向概念页。
7. `Personal Notes`。
8. `BibTeX`。

## Slug 约定

`{第一作者姓氏}-{年份}-{关键词}`

示例：
- `vaswani-2017-attention`
- `ouyang-2022-instructgpt`
- `rafailov-2023-dpo`

如果两篇论文 slug 相同，加上一个短的区分后缀：
`smith-2024-rl-agents-a`、`smith-2024-rl-agents-b`。

## 不要做

- 不要把原始 PDF 放在这里 —— 原始文件放在 `sources/` 或 `raw/`。
- 不要在创建论文页时漏掉对 `wiki/index.md` 的更新。
- 不要省略 `Connections` 小节 —— 它正是把孤立笔记变成 Wiki 的关键。
