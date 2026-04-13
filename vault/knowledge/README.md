---
type: knowledge-catalog
generated: 2026-04-13
tags:
  - knowledge
  - knowledge/catalog
---

# Knowledge catalog (second-brain index)

Entry point for topic research not tied to a specific project. **Read this file first** before diving into any `<topic>/` folder — it's cheaper than scanning items.

## How to navigate

1. Find topic of interest in the table below
2. Read `<topic>/index.md` for TL;DR + cross-cutting synthesis
3. Load specific `<topic>/items/*.md` only on demand

## Topics

| Topic | Slug | Summary | Last updated | Items | Confidence |
|---|---|---|---|---|---|
| Living knowledge base (PKM / second brain) | [[living-knowledge-base/index\|living-knowledge-base]] | 4 คลื่น + LLM Wiki v2 spec; POC implementation notes; confidence/forgetting/typed-graph conventions | 2026-04-13 | 6 | 0.8 |

## How to add a new topic

1. Run `/research <topic>` (invokes `research-outline` skill)
2. Confirm outline with user
3. Run `/research-deep` to expand into items
4. Append a row to the table above

## Conventions

- One folder per topic under `knowledge/<topic-slug>/`
- Inside: `outline.md` (phase 1) + `index.md` (TL;DR synthesis) + `items/*.md` (atomic notes)
- Each item note has `> [!summary]` (fact TL;DR) and optional `> [!opinion]` (AI's take, flagged as judgment not fact)
- Visibility defaults to `internal`; set `public` for topics safe to share

## Cross-project pollination

When a project research (`projects/<name>/10-research/<topic>/`) produces insights generic enough to reuse, **promote** it:
- Copy or move the relevant items to `knowledge/<topic>/`
- Anonymize client specifics
- Change visibility to `internal` or `public` as appropriate
- Leave a `[[knowledge/<topic>]]` backlink in the project's research folder
