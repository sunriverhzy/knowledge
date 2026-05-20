# `wiki/comparisons/` — Comparison Pages

One Markdown page per pairwise or multi-way comparison of methods,
models, or approaches.

## Purpose

A comparison page answers questions of the form:

- *"When should I use A vs. B?"*
- *"What are the tradeoffs between approach X, Y, and Z?"*
- *"Why did the field move from A to B?"*

These pages are the **distilled wisdom** of the wiki — they are often
more valuable than any individual paper page.

## When to create a comparison page

Create a comparison page when **any** of the following is true:

- Two or more methods are contrasted across **≥ 3 paper pages**.
- You catch yourself re-deriving the same comparison in multiple Q&A sessions.
- A `lint` pass flags a missing-comparison warning.

## Page format

Use **Template D** from `CLAUDE.md`. Required sections:

1. YAML frontmatter (title, domain, papers, last_updated).
2. `One-Line Summary` — when to use A vs B in one sentence.
3. `Comparison Table` — structured side-by-side dimensions.
4. `Detailed Analysis` — where each method excels, plus hybrid approaches.
5. `Recommendation` — concrete "choose X when..." guidance.

## Slug convention

- Pairwise: `{method-a}-vs-{method-b}`
  - Example: `dpo-vs-ppo`, `rlhf-vs-dpo`
- Topic-based: `{topic}-comparison`
  - Example: `reward-model-comparison`, `video-grounding-comparison`

## Do NOT

- Do not put a comparison inside a paper page if it spans more than 2
  papers — promote it to its own comparison page.
- Do not write a comparison page without citing all relevant wiki papers
  in the `papers:` frontmatter field.
