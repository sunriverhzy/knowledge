# `wiki/venues/` — Venue Pages

One Markdown page per conference, journal, or workshop.

## Purpose

Venue pages let you:

- See all papers ingested from a given venue, grouped by year.
- Spot trends in what a venue is accepting.
- Quickly navigate the wiki by publication source.

## Page format

Use **Template E** from `CLAUDE.md`. Required sections:

1. YAML frontmatter (title, abbreviation, type, domain).
2. `Overview` — short description of scope and prestige.
3. `Papers in This Wiki (by year)` — grouped list.
4. `Notable Trends` — themes observed across wiki papers from this venue.

## Slug convention

Use the **lowercase abbreviation** of the venue.

Examples:
- `neurips`
- `icml`
- `iclr`
- `cvpr`
- `arxiv` (use this for unpublished arXiv preprints)

## When to create

Create a venue page the **first time** a paper is ingested from that venue.
Every subsequent ingest from the same venue should update the existing page.
