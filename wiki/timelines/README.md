# `wiki/timelines/` — Research Timelines

Chronological pages that tell the *story* of a research sub-domain.

## Purpose

Where `comparisons/` answers *"which should I use?"*, timelines answer
*"how did we get here?"*. They are the narrative backbone of the wiki.

## When to create a timeline

Create a timeline when:

- A sub-domain has **≥ 5 papers** in the wiki.
- The papers span **≥ 3 years** (or are clearly organized into
  "generations" regardless of calendar year).
- The research story has clear milestones / turning points.

## Page format

```markdown
---
title: "Timeline: [Sub-Domain]"
domain: [domain tags]
period: "[YYYY]–[YYYY]"
last_updated: [YYYY-MM-DD]
---

# Timeline: [Sub-Domain]

## Summary
[2–3 paragraph narrative of the arc.]

## Milestones

### [YYYY] — [Milestone name]
- [[paper-slug]] — *[why this is a milestone]*

### [YYYY] — [Milestone name]
- [[paper-slug]]

## Turning Points
[Moments where the field shifted direction, and why.]

## Current Frontier
[Where active research sits today, pointing to the latest papers.]

## Open Questions
[What remains unsolved.]

## See Also
- [[concept-slug]]
- [[reading-list-slug]]
```

## Slug convention

`{sub-domain-kebab-case}` (no year — the time range goes in frontmatter).

Examples:
- `llm-alignment-timeline`
- `llm-agent-timeline`
- `video-grounding-timeline`
