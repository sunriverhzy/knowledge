# `wiki/timelines/` —— 研究时间线

讲述一个研究子领域*演化史*的年代页面。

## 用途

如果说 `comparisons/` 回答的是*"该用哪个？"*，那么时间线页回答的是
*"我们是怎么一步步走到今天的？"*。它们是 Wiki 的叙事主干。

## 何时新建时间线

- 该子领域在 Wiki 中有 **≥ 5 篇论文**。
- 论文覆盖 **≥ 3 年**（或者与自然年份无关，但能清晰地划分出若干"代"）。
- 这个研究故事有清晰的里程碑 / 转折点。

## 页面格式

```markdown
---
title: "Timeline: [子领域]"
domain: [领域标签]
period: "[YYYY]–[YYYY]"
last_updated: [YYYY-MM-DD]
---

# Timeline: [子领域]

## Summary（综述）
[2–3 段的发展脉络叙述。]

## Milestones（里程碑）

### [YYYY] —— [里程碑名称]
- [[paper-slug]] —— *[为什么是里程碑]*

### [YYYY] —— [里程碑名称]
- [[paper-slug]]

## Turning Points（转折点）
[领域方向改变的时刻，以及原因。]

## Current Frontier（当前前沿）
[活跃研究位于哪里，指向最新论文。]

## Open Questions（开放问题）
[仍然未解决的问题。]

## See Also（参见）
- [[concept-slug]]
- [[reading-list-slug]]
```

## Slug 约定

`{子领域-kebab-case}`（不写年份，年代范围放在 frontmatter 中）。

示例：
- `llm-alignment-timeline`
- `llm-agent-timeline`
- `video-grounding-timeline`
