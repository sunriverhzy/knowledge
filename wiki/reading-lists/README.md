# `wiki/reading-lists/` — Curated Reading Paths

One Markdown page per *reading path*: an ordered list of papers and
concepts to read on a specific topic, at a specific difficulty level.

## Purpose

Reading lists are the **onboarding** layer of the wiki. They answer:

- *"I am new to topic X — where do I start?"*
- *"I know the basics of X — what should I read next to get to the frontier?"*
- *"I want to run a reading group on X for 8 weeks — what is the syllabus?"*

## When to create a reading list

- A user (you, or a future collaborator) asks for "how do I learn X?".
- During a query, you find yourself recommending ≥ 5 papers in a
  specific order — promote that recommendation into a reading list.
- A sub-domain has enough papers in the wiki to offer a structured path.

## Page format

Use **Template F** from `CLAUDE.md`. Required sections:

1. YAML frontmatter (title, domain, difficulty, last_updated).
2. `Purpose` — audience and outcome.
3. `Prerequisite Concepts` — link to concept pages.
4. `Stage 1 — Foundations`, `Stage 2 — Core Methods`, `Stage 3 — Frontier Research`.
5. `Key Concepts to Master Along the Way`.

## Slug convention

`{topic-kebab-case}[-difficulty]`

Examples:
- `rlhf-beginner`
- `llm-agent-intermediate`
- `video-grounding-advanced`

## Good reading lists

- Are **short** (5–15 papers per list).
- Mix **papers** and **concept pages**.
- Explain *why* each item is on the list — one line is enough.
- Have an **explicit difficulty target**.
- Point forward to other reading lists at higher difficulty.
