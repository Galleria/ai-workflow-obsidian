---
type: inbox-hub
tags:
  - knowledge
  - knowledge/inbox
---

# Inbox — working memory tier

Fleeting captures that haven't been processed into a topic yet. This is the **working memory** tier of [LLM Wiki v2](../living-knowledge-base/items/llm-wiki-v2.md) memory hierarchy — short-lived, not compound.

## What goes here

- Links/quotes/screenshots captured mid-thought ("interesting, look at this later")
- Conversation snippets worth remembering but not yet a full research question
- Raw observations from experiments or reading
- "I should research this someday" topic seeds

## What does NOT go here

- Fully-researched topics → they live in `knowledge/<topic>/` (semantic tier)
- Client-specific insights → `projects/<name>/research/` (project tier)
- Daily session notes → (episodic tier; currently not scaffolded — add later if needed)

## File format (minimal)

```markdown
---
type: inbox-note
captured: 2026-04-13
source: <URL or "conversation" or "own thought">
tags:
  - knowledge/inbox
  - <rough-topic>
---

# <One-phrase title>

Body: 1-10 lines. No structure required.
```

## Lifecycle

1. **Capture** — drop a note into this folder fast. Don't over-think title or frontmatter beyond the minimum above.
2. **Age** — inbox notes shouldn't live longer than ~30 days. If still here after that, either promote, discard, or consciously defer.
3. **Process** — weekly-ish review:
   - **Promote**: relevant to an existing topic → run `/update-knowledge <topic> <inbox-note-path>` in `append` or `merge` mode, then delete the inbox note
   - **Seed a new topic**: material enough → run `/research <topic>` using the inbox note as starting context, then delete
   - **Discard**: no longer relevant → delete (git history preserves it)
   - **Defer consciously**: still valuable but not ready → move to a `knowledge/inbox/_deferred/` subfolder with a note about why

## Current inbox

```dataview
TABLE WITHOUT ID
  file.link AS "Note",
  captured AS "Captured",
  source AS "Source",
  file.mtime AS "Last touched"
FROM "knowledge/inbox"
WHERE type = "inbox-note"
SORT captured ASC
```

### Aging (older than 30 days — process now)

```dataview
LIST
FROM "knowledge/inbox"
WHERE type = "inbox-note" AND date(captured) < date(today) - dur(30 days)
```

## Rules

- **Don't polish inbox notes** — fast capture is the point. Polishing happens at promotion time.
- **Delete after promoting** — inbox is transient, not an archive. Git history is the archive.
- **Don't let inbox grow past ~20 notes** — signal that you're capturing faster than processing; slow intake until you catch up.
- **Typed relations are optional** here — they get added during promotion, not capture.
