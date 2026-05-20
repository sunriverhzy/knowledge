# `wiki/reading-lists/` —— 策划的阅读路径

每份*阅读路径*对应一个 Markdown 页面：一份针对特定主题、特定难度的
有序论文与概念阅读清单。

## 用途

阅读清单是 Wiki 的**入门层**。它回答：

- *"我是新手，想学 X —— 从哪里开始？"*
- *"我已经懂 X 的基础 —— 接下来读什么可以冲到前沿？"*
- *"我想组织一个 8 周的 X 方向读书会 —— 大纲是什么？"*

## 何时新建阅读清单

- 有人（你自己或未来的合作者）问"怎么学 X？"
- 在一次查询中你发现自己正按特定顺序推荐了 ≥ 5 篇论文 ——
  把这次推荐固化为一份阅读清单。
- 某个子领域已经有足够多的 Wiki 论文可以给出一条有结构的路径。

## 页面格式

使用 `CLAUDE.md` 中的**模板 F**。必需小节：

1. YAML frontmatter（title、domain、difficulty、last_updated）。
2. `Purpose` —— 目标读者与预期收获。
3. `Prerequisite Concepts` —— 链到前置概念页。
4. `Stage 1 — Foundations`、`Stage 2 — Core Methods`、`Stage 3 — Frontier Research`。
5. `Key Concepts to Master Along the Way`。

## Slug 约定

`{主题-kebab-case}[-难度]`

示例：
- `rlhf-beginner`
- `llm-agent-intermediate`
- `video-grounding-advanced`

## 什么样的阅读清单是好的

- 要**短**（每份 5–15 篇论文）。
- **同时包含**论文和概念页。
- 为每一项**说明它为什么在清单里** —— 一行字足够。
- 明确**难度目标**。
- 承上启下，指向下一份更高难度的阅读清单。
