# `wiki/authors/` — Author Pages

One Markdown page per notable researcher.

## When to create an author page

Create an author page when **any** of the following is true:

- The author has **≥ 2 papers** in this wiki.
- The author is a well-known figure in the field whose work you are
  actively following, even if only one paper is ingested so far.
- You find yourself wanting to cross-reference "other work by this author"
  from a paper page.

## Page format

Use **Template C** from `CLAUDE.md`. Required sections:

1. YAML frontmatter (title, affiliation, domain, homepage).
2. `Research Focus`.
3. `Papers in This Wiki` — list with `[[paper-slug]]` links.
4. `Notable Contributions to the Field`.
5. `Key Collaborators (in this wiki)`.
6. `Research Group / Lab`.

## Slug convention

`{firstname-lastname}` (lowercase, kebab-case, ASCII only).

Examples:
- `ilya-sutskever`
- `andrej-karpathy`
- `chelsea-finn`

For non-ASCII names, use a romanized form and add the original name
inside the page itself.

## Do NOT

- Do not create an author page just because a paper was ingested —
  wait until the threshold rule above is met.
- Do not duplicate information that belongs on paper pages.
  Author pages are a *navigation* layer, not a content archive.
