# `wiki/concepts/` — Concept Pages

One Markdown page per concept, technique, or method.

A concept page is **standalone**: a reader should be able to understand
the concept without going back to the original papers.

## When to create a concept page

Create a concept page when any of the following is true:

- The concept is mentioned in **≥ 2 paper pages**.
- The concept is central to one paper and is likely to recur (e.g., a
  new RL algorithm, a new model architecture).
- The concept is referenced by a `[[wikilink]]` from any paper page.

## Page format

Use **Template B** from `CLAUDE.md`. Required sections:

1. YAML frontmatter (title, aliases, domain, tags, source_count, last_updated).
2. `Definition` — precise, one paragraph.
3. `Intuition`.
4. `Historical Context`.
5. `Key Variants / Subtypes`.
6. `Technical Details` — equations / algorithms as needed.
7. `Key Papers` — a table listing every wiki paper that discusses this concept.
8. `Comparison with Related Concepts`.
9. `Current Research Frontiers`.
10. `See Also` — links to related concepts.

## Slug convention

`{concept-name-kebab-case}`

Examples:
- `reinforcement-learning-from-human-feedback`
- `direct-preference-optimization`
- `chain-of-thought`
- `mixture-of-experts`

## Aliases

Use the `aliases` YAML field so that abbreviations and synonyms resolve
correctly. E.g. a page titled `Reinforcement Learning from Human Feedback`
should declare `aliases: ["RLHF"]`.

## Updating vs. creating

When ingesting a new paper that discusses an existing concept:
- **Update** the concept page — add the new paper to `Key Papers`,
  enrich `Current Research Frontiers` if needed.
- **Do not** duplicate the page just because the new paper uses a slightly
  different name. Add the name as an alias instead.
