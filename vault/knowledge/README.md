---
type: knowledge-hub
generated: 2026-04-13
tags:
  - knowledge
  - knowledge/hub
---

# Knowledge hub — second brain

Entry point for all topic research. **Claude reads this file first** for any knowledge query (before loading a topic's `index.md`).

Memory tiers (per [LLM Wiki v2](living-knowledge-base/items/llm-wiki-v2.md)):
- **Working** → [[inbox/README|knowledge/inbox/]] — fleeting captures, ~30 day lifespan
- **Semantic** → topics below (compound knowledge, long-lived)
- **Procedural** → [[../_CLAUDE|vault/skills/]] — how-to knowledge
- **Episodic** — *not scaffolded yet* (add if daily-log patterns emerge)

Queries below render live via the **Dataview** plugin. If you see raw code blocks, open the file in Obsidian (not a plain markdown previewer).

---

## Topics catalog

```dataview
TABLE WITHOUT ID
  file.link AS "Topic",
  item-count AS "Items",
  confidence AS "Conf",
  last-updated AS "Updated",
  review-after AS "Review by",
  visibility AS "Vis"
FROM "knowledge"
WHERE type = "research-index"
SORT last-updated DESC
```

---

## Needs attention

### 🟡 Low-confidence items (<0.7) — worth corroborating

```dataview
TABLE WITHOUT ID
  file.link AS "Item",
  topic AS "Topic",
  confidence AS "Conf",
  last-accessed AS "Last seen"
FROM "knowledge"
WHERE type = "research-item" AND confidence < 0.7
SORT confidence ASC
```

### 🕰️ Stale items (review-after past due)

```dataview
TABLE WITHOUT ID
  file.link AS "Item",
  topic AS "Topic",
  confidence AS "Conf",
  review-after AS "Was due"
FROM "knowledge"
WHERE review-after AND review-after < date(today)
SORT review-after ASC
```

### ⚠️ Open contradictions

```dataview
LIST rows.file.link
FROM "knowledge"
WHERE contains(file.tags, "#contradiction") OR contains(file.content, "#contradiction")
GROUP BY topic
```

---

## Recent activity

### Last 10 items touched

```dataview
TABLE WITHOUT ID
  file.link AS "Item",
  topic AS "Topic",
  last-accessed AS "Touched",
  confidence AS "Conf"
FROM "knowledge"
WHERE type = "research-item"
SORT last-accessed DESC
LIMIT 10
```

---

## Cross-topic structure (typed graph)

Scans items for inline typed relations (`[[A]] #<type> [[B]]`) — surfaces graph edges without a dedicated engine.

```dataview
LIST
FROM "knowledge"
WHERE type = "research-item"
FLATTEN file.lists AS L
WHERE regexmatch("\[\[.+\]\] #(causes|supports|contradicts|part-of|implements|supersedes|builds-on|refines|automates|mitigates) \[\[.+\]\]", L.text)
GROUP BY file.link
```

---

## How to navigate (for humans)

1. Pick a topic from the catalog above
2. Open `<topic>/index.md` — TL;DR + synthesis + opinion
3. Dig into `<topic>/items/*.md` only when a specific angle matters
4. Source provenance lives in `<topic>/_raw/`

## How to add a new topic

1. Run `/research <topic>` (invokes `research-outline` skill) — agrees on scope
2. Confirm outline with user
3. Run `/research-deep` to expand into items + generate `index.md`
4. Topic appears in catalog automatically (Dataview picks it up from frontmatter)

## How to update an existing topic

- New source (URL/PDF/text): run `/update-knowledge <topic> <source>`
  - `append` mode — adds a dated subsection, no rewrites
  - `merge` mode — rewrites affected sections; contradictions preserved under superseded callouts
  - `refresh` mode — regenerates `index.md` synthesis only

## Conventions (condensed)

- One folder per topic: `knowledge/<topic-slug>/`
- Inside: `outline.md` (phase 1) + `index.md` (synthesis, Dataview source) + `items/*.md` + `_raw/` (immutable sources)
- Frontmatter required on every item: `type`, `topic`, `confidence`, `last-accessed`, `review-after`, `tags`, `visibility`
- Opinion blocks (`> [!opinion]`) flag AI judgment separately from fact
- Typed relations (`[[A]] #refines [[B]]`) encode semantic edges — parseable by Dataview
- Visibility defaults to `internal`; set `public` for topics safe to share

Full spec: `[[_CLAUDE]]` (core conventions section) + `[[obsidian-markdown]]` (syntax).

## Cross-project pollination

When project research (`projects/<name>/research/<topic>/`) produces insights generic enough to reuse:
1. Copy or move the relevant items to `knowledge/<topic>/`
2. Anonymize client specifics
3. Change `visibility` to `internal` or `public` as appropriate
4. Leave a `[[knowledge/<topic>]]` backlink in the project's research folder
