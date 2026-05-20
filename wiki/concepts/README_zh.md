# `wiki/concepts/` —— 概念页

每个概念、技术、方法对应一个 Markdown 页面。

概念页是**自包含的**：读者不回去看原论文，也能理解这个概念。

## 何时新建概念页

只要满足下列任一条件就新建概念页：

- 该概念在 **≥ 2 个论文页**中被提及。
- 该概念对某一篇论文是核心内容，并且很可能会反复出现
  （例如一种新的 RL 算法、一种新的模型架构）。
- 任一论文页中出现了指向该概念的 `[[wikilink]]`。

## 页面格式

使用 `CLAUDE.md` 中的**模板 B**。必需小节：

1. YAML frontmatter（title、aliases、domain、tags、source_count、last_updated）。
2. `Definition` —— 精确的一段话定义。
3. `Intuition`。
4. `Historical Context`。
5. `Key Variants / Subtypes`。
6. `Technical Details` —— 公式 / 算法，按需撰写。
7. `Key Papers` —— 列出 Wiki 中所有讨论该概念的论文的表格。
8. `Comparison with Related Concepts`。
9. `Current Research Frontiers`。
10. `See Also` —— 链到相关概念。

## Slug 约定

`{概念名-kebab-case}`

示例：
- `reinforcement-learning-from-human-feedback`
- `direct-preference-optimization`
- `chain-of-thought`
- `mixture-of-experts`

## 别名

在 YAML 的 `aliases` 字段中填入缩写和同义词，使它们能正确解析。
例如标题为 `Reinforcement Learning from Human Feedback` 的页面，
应声明 `aliases: ["RLHF"]`。

## 更新 vs 创建

当摄取一篇提及已有概念的新论文时：
- **更新**该概念页 —— 在 `Key Papers` 表中加入新论文，
  有必要时丰富 `Current Research Frontiers`。
- **不要**因为新论文对该概念使用了略微不同的名字就重复建页。
  应当把新名字作为 alias 加入现有页面。
