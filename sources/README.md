# `sources/` — Immutable Raw Inputs

This directory is **Layer 1** of the three-layer architecture.

> **Rule:** never edit files in `sources/` after saving them.
> If a paper changes (v2, v3 etc.), add the new version as a separate file.

## Layout

```
sources/
├── inbox/                 # Drop zone: new papers to be ingested
└── papers/                # Organized archive (post-ingest)
    ├── llm/
    ├── rl/
    ├── aigc/
    ├── multimodal/
    ├── agent/
    ├── reward-modeling/
    └── other/
```

## Two ways to add a paper

### Option 1 — Drop into `inbox/`

1. Place the PDF (or Markdown extract) in `sources/inbox/`.
2. Ask the LLM: *"please ingest the papers in `sources/inbox/`"*.
3. The LLM runs the 10-step ingest workflow defined in `CLAUDE.md`.
4. After ingest, the file is moved into `sources/papers/{domain}/`.

### Option 2 — Batch-ingest from an existing archive

If you already have papers organized (e.g. in `raw/` at project root),
you can either:

- **Reference** them in-place: give the LLM the absolute paths and
  ask it to ingest them without moving.
- **Migrate** them: move/copy into `sources/papers/{domain}/` first,
  then run a bulk ingest.

The pre-existing `raw/` folder at project root is allowed to remain
as an un-curated archive — it simply won't be touched by the ingest
workflow unless you explicitly point the LLM at it.

## Naming hints

Filenames in `sources/papers/` should be readable but need not match
the wiki slug. Example:

```
sources/papers/llm/  vaswani-2017-attention-is-all-you-need.pdf
wiki/papers/llm/     vaswani-2017-attention.md
```

Keeping the **slug prefix** (`vaswani-2017-`) consistent makes it
trivial to jump between a source and its wiki page.

## What does NOT live here

- Extracted notes, summaries, highlights → live in `wiki/papers/`.
- Your own commentary → lives in `wiki/papers/<paper>.md` under
  `Personal Notes`.
- Re-organized / curated narrative → lives in `wiki/overview.md`,
  `wiki/timelines/`, `wiki/reading-lists/`.

## Formats accepted

- `.pdf` — will be read directly by a capable LLM / PDF-reading agent.
- `.md` — preferred; use this if you've already converted a paper
  with `marker`, `pypdf`, or a similar tool.
- `.txt` — plain text extracts are fine.
- `.html` — OK for blog posts and technical reports.

## Ingest checklist

Before asking the LLM to ingest, make sure:

- [ ] The file is in `sources/inbox/` (or a path you'll give explicitly).
- [ ] You know which `domain` the paper belongs to (or will let the LLM decide).
- [ ] `wiki/index.md` exists (run `init` first, if needed).
