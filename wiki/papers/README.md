# `wiki/papers/` — Paper Pages

One Markdown page per paper, organized by domain.

## Layout

```
papers/
├── llm/              # Large Language Model papers
├── rl/               # Reinforcement Learning papers
├── aigc/             # AI-Generated Content papers
├── multimodal/       # VLM / video / multimodal papers
├── agent/            # LLM-agent papers
├── reward-modeling/  # Reward model / preference learning papers
└── other/            # Papers that don't fit cleanly above
```

## Page format

Use **Template A** from `CLAUDE.md`. Every paper page MUST include:

1. YAML frontmatter (title, slug, authors, year, venue, tags, status, ingested).
2. `TL;DR` — a one-sentence summary.
3. `Problem Statement`, `Key Contributions`, `Method Overview`.
4. `Experiments & Results`.
5. `Strengths`, `Limitations & Weaknesses`, `Open Questions`.
6. `Connections` — with at least one `[[wikilink]]` to a concept page.
7. `Personal Notes`.
8. `BibTeX`.

## Slug convention

`{first-author-lastname}-{year}-{keyword}`

Examples:
- `vaswani-2017-attention`
- `ouyang-2022-instructgpt`
- `rafailov-2023-dpo`

If two papers collide on the same slug, append a short disambiguator:
`smith-2024-rl-agents-a`, `smith-2024-rl-agents-b`.

## Do NOT

- Do not put the raw PDF here — raw files live in `sources/` or `raw/`.
- Do not create a paper page without also updating `wiki/index.md`.
- Do not skip the `Connections` section — it is what turns isolated notes into a wiki.
