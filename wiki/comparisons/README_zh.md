# `wiki/comparisons/` —— 对比页

每对（或多方）方法、模型、做法之间的对比对应一个 Markdown 页面。

## 用途

一个对比页回答的是这样的问题：

- *"我什么时候该用 A、什么时候该用 B？"*
- *"方法 X、Y、Z 之间的取舍是什么？"*
- *"为什么这个领域从 A 转向 B？"*

这些页面是 Wiki **最核心的思考结晶** —— 它们的价值往往超过任何单个
论文页。

## 何时新建对比页

只要满足**任一**条件就新建对比页：

- 两个及以上方法在 **≥ 3 个论文页**中被互相对比。
- 你发现自己在多次问答中反复推导同样的对比。
- `lint` 报告提示存在"缺失对比页"警告。

## 页面格式

使用 `CLAUDE.md` 中的**模板 D**。必需小节：

1. YAML frontmatter（title、domain、papers、last_updated）。
2. `One-Line Summary` —— 一句话说明何时用 A、何时用 B。
3. `Comparison Table` —— 结构化的并排维度对比。
4. `Detailed Analysis` —— 每种方法的擅长之处，以及混合方法。
5. `Recommendation` —— 明确的"在什么情形下选用 X"的选型指引。

## Slug 约定

- 两两对比：`{方法A}-vs-{方法B}`
  - 例：`dpo-vs-ppo`、`rlhf-vs-dpo`
- 主题对比：`{主题}-comparison`
  - 例：`reward-model-comparison`、`video-grounding-comparison`

## 不要做

- 不要把跨 2 个以上论文的对比写在某个论文页里 —— 应该提升为独立对比页。
- 不要在对比页的 `papers:` frontmatter 字段中遗漏任何相关的 Wiki 论文。
