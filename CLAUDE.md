# AI Research Wiki — Schema & Operating Instructions

> This file is the **single source of truth** for how the LLM should build,
> maintain, and query this knowledge base. It is the "brain" of the wiki.
>
> Philosophy (after Andrej Karpathy's LLM Wiki pattern):
> **Compile knowledge at ingest time. Query the compiled wiki — not the raw sources.**

---

## 🎯 Purpose

This wiki is a personal knowledge base for an AI researcher, covering
papers and concepts in the following domains:

- **LLM** — Large Language Models (architecture, training, inference)
- **RL** — Reinforcement Learning and RLHF / RLAIF
- **AIGC** — AI-Generated Content (diffusion, generation, watermarking)
- **Multimodal** — VLM, video understanding, grounding
- **Agent** — LLM-based agents, multi-agent systems, GUI agents
- **Reward Modeling** — reward models, preference learning, alignment
- **Theory & Methods** — optimization, scaling laws, evaluation

The goal is to **compile knowledge at ingest time** and **query the
compiled wiki** — not to re-discover from raw documents every time.

---

## 🏛️ Three-Layer Architecture

```
┌──────────────────────────────────────────┐
│  Layer 3: Schema  (this file: CLAUDE.md) │  ← Rules, templates, workflows
├──────────────────────────────────────────┤
│  Layer 2: Wiki    (wiki/*)               │  ← LLM-generated, interlinked
├──────────────────────────────────────────┤
│  Layer 1: Sources (sources/*, raw/*)     │  ← Immutable raw inputs
└──────────────────────────────────────────┘
```

- **Sources** are immutable. Never edit them after saving.
- **Wiki** is fully regenerable from Sources + Schema.
- **Schema** is hand-curated by the human owner.

---

## 📂 Directory Structure

| Path | Description |
|------|-------------|
| `CLAUDE.md` | This schema file. |
| `README.md` | Human-facing quick-start guide. |
| `raw/` | Original paper archive (read-only, pre-existing). |
| `sources/` | Curated source layer. |
| `sources/papers/{domain}/` | Papers organized by domain. |
| `sources/inbox/` | Drop zone for new papers waiting to be ingested. |
| `wiki/` | LLM-maintained knowledge base. All files here are generated/updated by LLM. |
| `wiki/index.md` | Content catalog — navigation hub. Updated on every ingest. |
| `wiki/overview.md` | High-level field synthesis and knowledge map. |
| `wiki/papers/{domain}/` | One page per paper. |
| `wiki/concepts/` | One page per concept or technique. |
| `wiki/authors/` | One page per notable researcher. |
| `wiki/venues/` | One page per conference / journal. |
| `wiki/comparisons/` | Side-by-side comparison pages. |
| `wiki/timelines/` | Chronological research history by sub-domain. |
| `wiki/reading-lists/` | Curated reading paths by topic. |

**Domain folder values** (use lowercase, kebab-case):
`llm`, `rl`, `aigc`, `multimodal`, `agent`, `reward-modeling`, `other`

---

## 🔗 Linking Rules

- Use `[[PageTitle]]` (wikilinks) for all internal cross-references.
- Every **paper page** MUST link to at least one concept page.
- Every **concept page** MUST link to at least one paper page.
- When referencing a paper inline, use the slug: `[[vaswani-2017-attention]]`.
- When a concept or named entity is mentioned for the first time in a page, link it.
- Prefer linking to specific section headings when useful: `[[PageTitle#Section]]`.
- Aliases in frontmatter allow `[[Alias]]` to resolve to the canonical page.

---

## 🏷️ Tagging Rules

All pages MUST include YAML frontmatter. Tags should be chosen from
the controlled vocabulary below. You MAY add new tags if necessary,
but record them in `wiki/index.md` under **Tag Registry**.

**Domain tags**
`llm`, `rl`, `aigc`, `multimodal`, `agent`, `reward-modeling`, `theory`,
`nlp`, `cv`, `robotics`, `alignment`, `efficiency`, `watermark`, `ocr`, `video`

**Method tags**
`transformer`, `diffusion`, `rlhf`, `rlaif`, `ppo`, `dpo`, `grpo`, `sft`,
`pretraining`, `finetuning`, `inference`, `attention`, `moe`, `rag`,
`prompt-engineering`, `scaling-law`, `self-play`, `test-time`,
`trajectory-optimization`, `tool-use`, `planning`, `multi-agent`,
`gui-agent`, `web-agent`, `search-agent`, `reward-model`

**Venue tags**
`neurips`, `icml`, `iclr`, `acl`, `emnlp`, `cvpr`, `iccv`, `eccv`,
`arxiv`, `nature`, `science`, `aaai`, `kdd`, `www`

**Status tags (papers only)**
`read`, `skimmed`, `queued`, `key-paper`, `revisit`

---

## 📋 Page Templates

### Template A — Paper Page

- **File path:** `wiki/papers/{domain}/{slug}.md`
- **Slug format:** `{first-author-lastname}-{year}-{keyword}`
  - Example: `vaswani-2017-attention`, `ouyang-2022-instructgpt`

```markdown
---
title: "[Full Paper Title]"
slug: "[first-author-lastname]-[year]-[keyword]"
authors:
  - "[Author 1]"
  - "[Author 2]"
year: [YYYY]
venue: "[Conference/Journal + Year, e.g., NeurIPS 2024]"
arxiv: "[arxiv ID or URL]"
domain: [llm | rl | aigc | multimodal | agent | reward-modeling | other]
tags: [tag1, tag2, ...]
status: [read | skimmed | queued | key-paper]
ingested: [YYYY-MM-DD]
---

# [Full Paper Title]

## TL;DR
> [One sentence: what this paper does and why it matters.]

## Problem Statement
[What specific problem does this paper address? What gap in prior work
does it fill? Be precise.]

## Key Contributions
1. [Contribution 1]
2. [Contribution 2]
3. [Contribution 3]

## Method Overview
[Core methodology. Include key equations if central to understanding.
Explain intuition before formalism.]

### Architecture / Algorithm
[If applicable: describe the model architecture or algorithm.]

### Training Objective
[If applicable: describe the loss function or training procedure.]

## Experiments & Results
[Key benchmarks used, main results, comparison baselines.]

| Benchmark | This Paper | Prior SOTA | Δ |
|-----------|-----------|-----------|---|
| [Name]    | [Score]   | [Score]   | +X% |

## Strengths
- [What does this paper do exceptionally well?]

## Limitations & Weaknesses
- [Known limitations acknowledged by authors]
- [Limitations identified upon reading]

## Open Questions
- [Questions this paper raises but does not answer]
- [Ideas for future work]

## Connections
- **Builds on:** [[concept-or-paper]], [[concept-or-paper]]
- **Compared with:** [[paper]]
- **Extended by:** [[paper]]  *(fill in as wiki grows)*
- **Related concepts:** [[concept]], [[concept]]
- **Same author's other work:** [[paper]]

## Personal Notes
[Your own annotations, critical thoughts, ideas sparked by this paper.]

## BibTeX
```bibtex
@article{...}
```
```

---

### Template B — Concept Page

- **File path:** `wiki/concepts/{slug}.md`
- **Slug format:** `{concept-name-kebab-case}`
  - Example: `reinforcement-learning-from-human-feedback`

```markdown
---
title: "[Concept Name]"
aliases: ["[Alternative Name 1]", "[Alternative Name 2]"]
domain: [llm | rl | aigc | multimodal | agent | reward-modeling | theory]
tags: [tag1, tag2]
source_count: [N]   # number of papers in this wiki that discuss this concept
last_updated: [YYYY-MM-DD]
---

# [Concept Name]

## Definition
[Clear, precise definition. One paragraph. Written for an expert reader
but accessible without reading the original papers.]

## Intuition
[The core intuition behind this concept. Use analogies if helpful.]

## Historical Context
[When and how did this concept emerge? What problem was it solving?]

## Key Variants / Subtypes
- **[Variant A]:** [description]
- **[Variant B]:** [description]

## Technical Details
[Equations, algorithms, or formal definitions as needed.]

## Key Papers
*(ordered by importance or chronology)*

| Paper | Year | Contribution |
|-------|------|-------------|
| [[paper-slug]] | YYYY | [What this paper contributed to this concept] |

## Comparison with Related Concepts

|              | This Concept | [[Related Concept A]] | [[Related Concept B]] |
|--------------|--------------|-----------------------|-----------------------|
| [Dimension 1]|              |                       |                       |
| [Dimension 2]|              |                       |                       |

## Current Research Frontiers
[Open problems and active research directions for this concept.]

## See Also
- [[related-concept-1]]
- [[related-concept-2]]
```

---

### Template C — Author Page

- **File path:** `wiki/authors/{slug}.md`
- **Slug format:** `{firstname-lastname}` (lowercase, kebab-case)
  - Example: `ilya-sutskever`

```markdown
---
title: "[Full Name]"
affiliation: "[Current Institution]"
domain: [primary research area tags]
homepage: "[URL if known]"
---

# [Full Name]

## Research Focus
[Primary research interests and contributions, 2–3 sentences.]

## Papers in This Wiki
- [[paper-slug]] — [one-line description]
- [[paper-slug]] — [one-line description]

## Notable Contributions to the Field
[What are they broadly known for, beyond the papers in this wiki?]

## Key Collaborators (in this wiki)
- [[author-name]]

## Research Group / Lab
[Lab name and link if applicable]
```

---

### Template D — Comparison Page

- **File path:** `wiki/comparisons/{slug}.md`
- **Slug format:** `{method-a}-vs-{method-b}` or `{topic}-comparison`

```markdown
---
title: "[Method A] vs [Method B]"
domain: [domain tags]
papers: ["[[paper-a]]", "[[paper-b]]"]
last_updated: [YYYY-MM-DD]
---

# [Method A] vs [Method B]

## One-Line Summary
[When to use A vs B, in one sentence.]

## Comparison Table

| Dimension              | [[Method A]] | [[Method B]] |
|------------------------|--------------|--------------|
| Core idea              |              |              |
| Architecture           |              |              |
| Training data          |              |              |
| Compute cost           |              |              |
| Benchmark performance  |              |              |
| Scalability            |              |              |
| Key limitation         |              |              |

## Detailed Analysis

### Where Method A excels
[...]

### Where Method B excels
[...]

### Hybrid or follow-up approaches
- [[paper]] — [description]

## Recommendation
[Concrete guidance on when to choose which.]
```

---

### Template E — Venue Page

- **File path:** `wiki/venues/{slug}.md`
- **Slug format:** `{venue-abbreviation}` (e.g., `neurips`, `iclr`)

```markdown
---
title: "[Conference/Journal Full Name]"
abbreviation: "[ABBR]"
type: [conference | journal | workshop]
domain: [primary domains]
---

# [Conference/Journal Full Name]

## Overview
[Brief description of the venue's scope and prestige.]

## Papers in This Wiki (by year)

### [Year]
- [[paper-slug]] — [one-line description]

## Notable Trends
[Recurring themes or trends observed from papers in this wiki.]
```

---

### Template F — Reading List Page

- **File path:** `wiki/reading-lists/{topic}.md`

```markdown
---
title: "Reading List: [Topic]"
domain: [domain tags]
difficulty: [beginner | intermediate | advanced]
last_updated: [YYYY-MM-DD]
---

# Reading List: [Topic]

## Purpose
[Who is this reading list for and what will they gain?]

## Prerequisite Concepts
- [[concept-1]]
- [[concept-2]]

## Stage 1 — Foundations
1. [[paper-slug]] — *[Why start here]*
2. [[paper-slug]] — *[What this adds]*

## Stage 2 — Core Methods
1. [[paper-slug]] — *[Why read this]*

## Stage 3 — Frontier Research
1. [[paper-slug]]

## Key Concepts to Master Along the Way
- [[concept-1]]
- [[concept-2]]
```

---

## ⚙️ Ingest Workflow — Step by Step

When asked to ingest a new paper (typically sitting in `sources/inbox/`
or `raw/`), follow this exact procedure:

1. **Read** the source file fully (PDF text, abstract, or converted Markdown).
2. **Create** a paper page at `wiki/papers/{domain}/{slug}.md` using **Template A**.
3. **Identify** all key concepts mentioned. For each:
   - If `wiki/concepts/{concept-slug}.md` exists → **update** it
     (add this paper to *Key Papers* table, enrich content if possible).
   - If it does NOT exist → **create** it using **Template B**.
4. **Identify** all authors. For each author with 2+ papers in the wiki:
   - If `wiki/authors/{slug}.md` exists → add this paper.
   - If it does NOT exist → create it using **Template C**.
5. **Update** `wiki/venues/{venue-slug}.md` to list the new paper.
6. **Add cross-references:** scan existing wiki pages for mentions of
   this paper's key concepts and insert `[[wikilink]]` where appropriate.
7. **Update** `wiki/index.md`: add the new paper under the correct domain section.
8. **Consider** whether `wiki/overview.md` needs updating
   (only for landmark papers or significant new directions).
9. **Move** the source file from `sources/inbox/` to `sources/papers/{domain}/`.
10. **Bilingual mirroring** — for **every** Markdown file created or
    updated in steps 2–8 (paper page, concept pages, author pages,
    venue page, comparison page, `index.md`, `overview.md`), also
    create / update its Chinese counterpart `<filename>_zh.md`.
    The Chinese version is a **faithful translation** of the English
    version: keep all YAML frontmatter, all `[[wikilinks]]`, all slugs,
    all equations, and all tables exactly the same — only translate
    natural-language prose. The English file is the **source of truth**.
11. **Report** a summary of all pages created / updated, in this format:

```
✅ Ingest complete for "[Paper Title]"
   • Created:  wiki/papers/{domain}/{slug}.md           (+ {slug}_zh.md)
   • Updated:  wiki/concepts/{c1}.md                    (+ {c1}_zh.md)
   • Created:  wiki/concepts/{c3}.md                    (+ {c3}_zh.md)
   • Updated:  wiki/index.md                            (+ wiki/index_zh.md)
   • Moved:    sources/inbox/x.pdf → sources/papers/{domain}/x.pdf
```

---

## 🔍 Query Behavior

When answering a question from the wiki:

1. **Load `wiki/index.md` first** to understand what's available.
2. **Load relevant wiki pages** (papers, concepts, comparisons) — NOT raw sources.
3. **Answer from the synthesized wiki content.**
4. If a good synthesized answer requires cross-referencing 3+ wiki pages,
   **offer to save it** as a new page in `wiki/reading-lists/` or `wiki/comparisons/`.
5. **Always cite specific wiki pages:** "According to [[paper-slug]]..."
6. If the wiki lacks sufficient information, **say so explicitly** and
   suggest which paper or concept page would fill the gap.

### Query → Wiki Write-back Rule

> "Good answers can be filed back into the wiki as new pages."
> — Andrej Karpathy, LLM Wiki pattern

**When to write back:**
- The answer contains a **novel analysis, comparison, or connection** not already in the wiki.
- The answer synthesizes **3+ wiki pages** into a coherent narrative.
- The user explicitly asks to **"record this"** or **"save this to wiki"**.
- The answer identifies a **new concept cluster** (like "Agentic RL") that deserves its own page.

**How to write back:**
- Create a new concept page, comparison page, or reading-list page.
- Include proper YAML frontmatter, `[[wikilinks]]`, and bilingual (`_zh`) counterpart.
- Append an entry to `wiki/log.md` recording the query and write-back.
- Update `wiki/index.md` and `wiki/index_zh.md`.

**What NOT to write back:**
- Operational requests ("download this PDF", "push to git") — these are transient.
- Questions already fully answered by existing wiki pages — just cite them.
- Raw chat history — the wiki stores **compiled knowledge**, not conversations.

### Operations Log

All operations (init, ingest, query write-backs, lint) are recorded in
`wiki/log.md` as an append-only timeline. Format:

```
## [YYYY-MM-DD] operation | description
```

---

## 🩺 Lint Rules

When asked to lint the wiki (`/lint`), check for the following and
output a report organized by severity — 🔴 Critical / 🟡 Warning / 🟢 Suggestion:

1. **🔴 Broken links** — `[[wikilink]]` pointing to non-existent pages.
2. **🔴 Incomplete paper pages** — missing required sections
   (TL;DR, Key Contributions, Connections).
3. **🔴 Index desync** — papers in `wiki/papers/` not listed in `wiki/index.md`.
4. **🟡 Orphan pages** — wiki pages with no inbound `[[links]]` from other pages.
5. **🟡 Missing concept pages** — concepts mentioned inline in paper pages
   but lacking their own page.
6. **🟡 Stale claims** — paper pages whose *Current Research Frontiers*
   sections may be outdated given newer ingested papers.
7. **🟢 Unlinked authors** — papers whose authors have 2+ papers in the
   wiki but no author page.
8. **🟢 Missing comparisons** — pairs of papers / methods compared in
   3+ paper pages but lacking a dedicated comparison page.

---

## 📊 `wiki/index.md` Canonical Structure

```markdown
# AI Research Wiki — Index

*Last updated: [YYYY-MM-DD] | Papers: N | Concepts: N | Authors: N*

---

## Papers

### LLM
| Slug | Title | Year | Venue | Tags | Status |
|------|-------|------|-------|------|--------|
| [[slug]] | Title | 2024 | NeurIPS | tag1, tag2 | read |

### Reinforcement Learning
...

### AIGC
...

### Multimodal
...

### Agent
...

### Reward Modeling
...

---

## Concepts
| Slug | Title | Domain | Source Count |
|------|-------|--------|--------------|

## Authors
| Slug | Name | Affiliation | Paper Count |
|------|------|-------------|-------------|

## Comparisons
| Slug | Title | Domains |
|------|-------|---------|

## Reading Lists
| Slug | Title | Difficulty |
|------|-------|-----------|

## Tag Registry
| Tag | Description | Page Count |
|-----|-------------|-----------|
```

---

## 🧭 Four Core Operations — Quick Reference

| Operation | Trigger | Action |
|-----------|---------|--------|
| **Init**   | First run | Read `CLAUDE.md`, create directory skeleton + empty `index.md` / `overview.md`. |
| **Ingest** | `/ingest` or "ingest X" | Run the 10-step workflow above for each paper in `sources/inbox/`. |
| **Query**  | Any question | Follow the Query Behavior rules; cite wiki pages. |
| **Lint**   | `/lint` | Run all Lint Rules and output a severity-sorted report. |

---

## 📝 Operating Principles

1. **Never edit files in `sources/` or `raw/`.**
2. **Always include YAML frontmatter** on wiki pages.
3. **Prefer updating existing pages over creating duplicates.**
4. **Every claim in a paper page should be traceable** to the source paper.
5. **Every concept page should be readable standalone** — don't require
   the user to have read the source papers.
6. **Keep `index.md` in sync** — it's the entry point for every query.
7. **Commit after every ingest** (if using git) so knowledge evolution is diff-able.
8. **When in doubt, be concise.** A short, correct wiki page beats a long, hand-wavy one.
