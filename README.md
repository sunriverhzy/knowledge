# AI Research Wiki

A personal, LLM-maintained knowledge base for AI research — covering
**LLM**, **RL**, **AIGC**, **Multimodal**, **Agents**, and **Reward Modeling**.

Built following Andrej Karpathy's **LLM Wiki** pattern:
> **Compile knowledge at ingest time. Query the compiled wiki — not the raw sources.**

---

## Why not plain RAG?

A RAG system retrieves *chunks* of raw text at query time. A wiki,
instead, is *pre-compiled* by the LLM at ingest time: concepts are
merged, comparisons are drawn, cross-references are inserted.

A wiki that has ingested 50 Transformer papers will answer questions
with **compounded depth** that a RAG over the same 50 papers cannot match —
because the wiki has *integrated* the knowledge, while the RAG can only
*fetch* the most similar pieces.

---

## Three-layer architecture

```
┌──────────────────────────────────────────┐
│  Layer 3: Schema  (CLAUDE.md)            │  ← Rules, templates, workflows
├──────────────────────────────────────────┤
│  Layer 2: Wiki    (wiki/*)               │  ← LLM-generated, interlinked
├──────────────────────────────────────────┤
│  Layer 1: Sources (sources/*, raw/*)     │  ← Immutable raw inputs
└──────────────────────────────────────────┘
```

- **Sources** — immutable original papers. Never edited after saving.
- **Wiki** — fully regenerable; the LLM writes and maintains it.
- **Schema** — `CLAUDE.md`, hand-curated by you.

---

## Directory layout

```
paper_research/
├── CLAUDE.md                   # 🧠 Schema: LLM operating instructions
├── README.md                   # This file
│
├── raw/                        # Pre-existing raw paper archive (read-only)
│
├── sources/                    # Layer 1: immutable raw inputs
│   ├── inbox/                  # Drop new papers here for ingest
│   └── papers/
│       ├── llm/
│       ├── rl/
│       ├── aigc/
│       ├── multimodal/
│       ├── agent/
│       ├── reward-modeling/
│       └── other/
│
└── wiki/                       # Layer 2: LLM-maintained knowledge base
    ├── index.md                # Navigation hub (updated on every ingest)
    ├── overview.md             # Field-level narrative
    ├── papers/                 # One page per paper, by domain
    ├── concepts/               # One page per concept
    ├── authors/                # One page per notable researcher
    ├── venues/                 # One page per conference / journal
    ├── comparisons/            # Side-by-side method comparisons
    ├── timelines/              # Chronological sub-domain histories
    └── reading-lists/          # Curated reading paths
```

---

## Four core operations

| Op | Trigger | What it does |
|-----|--------|-------------|
| **Init** | First run | Build directory skeleton and stubs. Already done if you're reading this. |
| **Ingest** | "ingest papers in `sources/inbox/`" | Run the 10-step workflow in `CLAUDE.md` for each new paper. |
| **Query** | Any question | Read `wiki/index.md`, then load relevant wiki pages (not raw sources), answer with citations. |
| **Lint** | "lint the wiki" | Report broken links, orphan pages, stale claims, missing comparisons, etc. |

---

## Quick start

### Step 1 — Open the project in a capable LLM tool

Recommended: **Claude Code**, **Cursor**, or **Codebuddy** — anything that
can read and write local files and fully ingest `CLAUDE.md`.

### Step 2 — Add your first paper

Drop a PDF or Markdown file into `sources/inbox/`. Then ask the LLM:

> Please read `CLAUDE.md` and ingest the papers in `sources/inbox/`
> following the Ingest Workflow.

### Step 3 — Query the wiki

Once you have a few papers in the wiki, start asking real questions:

> Summarize how reward modeling has evolved. Cite wiki pages.

> Build a reading list for someone new to LLM agents.

> Compare DPO and PPO for LLM alignment — what's in the wiki?

### Step 4 — Lint periodically

Every dozen ingests or so, ask:

> Please `/lint` the wiki and output a severity-sorted report.

Fix 🔴 Critical findings immediately; triage 🟡 and 🟢 at your leisure.

---

## Recommended toolchain

| Layer | Tool | Purpose |
|------|------|---------|
| File editing | **Obsidian** | Native `[[wikilink]]` rendering + graph view |
| LLM engine | **Claude Code / Cursor / Codebuddy** | Read/write local files, execute ingest/lint |
| Version control | **Git (private)** | Diff every ingest, roll back bad ingests |
| PDF → Markdown | **marker**, **pypdf2**, **pymupdf** | Pre-process PDFs into cleaner text |
| (optional) Graph analysis | **InfraNodus** | Concept clustering, blind-spot detection |

---

## Using the existing `raw/` folder

Your pre-existing `raw/` directory (containing ~57 PDFs) is left
untouched. You have two options:

1. **Gradual migration** — copy papers from `raw/` into
   `sources/inbox/` in small batches, ingesting them one at a time.
   This is safer; you get time to tune tag vocabulary and conventions.
2. **Bulk ingest** — point the LLM at `raw/` directly and ask for a
   mass ingest. Fast, but review the output carefully.

Recommended: **start with option 1**, pick 2–3 closely related papers
to ingest first so the concept-page skeleton gets built thoughtfully.

---

## File naming convention — paper slugs

`{first-author-lastname}-{year}-{keyword}`

Examples:
- `vaswani-2017-attention`
- `ouyang-2022-instructgpt`
- `rafailov-2023-dpo`

Keep source filenames aligned where possible:

```
sources/papers/llm/ vaswani-2017-attention-is-all-you-need.pdf
wiki/papers/llm/    vaswani-2017-attention.md
```

---

## Bilingual documentation

**Every Markdown file in this wiki has a Chinese counterpart whose
filename ends with `_zh`.** This includes schema files, READMEs,
the index, and **every wiki page** — paper pages, concept pages,
comparison pages, etc.

For example:

- `CLAUDE.md` ↔ `CLAUDE_zh.md`
- `README.md` ↔ `README_zh.md`
- `wiki/index.md` ↔ `wiki/index_zh.md`
- `wiki/papers/reward-modeling/hong-2025-think-rm.md` ↔ `…_zh.md`
- `wiki/concepts/reward-variance.md` ↔ `wiki/concepts/reward-variance_zh.md`

The English files are the **source of truth**; the `_zh` files are
faithful translations. **If you update one side, update the other.**

When ingesting a new paper, the LLM must therefore create:

1. The English page (`{slug}.md`) — primary content drawn from the
   source paper.
2. A Chinese counterpart (`{slug}_zh.md`) — faithful translation.
3. Same rule applies for any new concept / author / venue / comparison
   page created during ingest.

---

## Learn more about the LLM Wiki pattern

- Andrej Karpathy's original gist — *[llm-wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)*
- *Beyond RAG: How Andrej Karpathy's LLM Wiki Pattern Builds Knowledge That Actually Compounds* — Level Up Coding
- *How I Took Karpathy's LLM Wiki and Built an AI-Powered Second Brain in Obsidian* — AI Maker Substack

---

## License

Personal knowledge base — all rights reserved unless you say otherwise.
Papers in `sources/` and `raw/` retain their original licenses.
