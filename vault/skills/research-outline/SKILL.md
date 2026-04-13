---
name: research-outline
user-invocable: true
allowed-tools: Read, Write, Glob, WebSearch, WebFetch, AskUserQuestion
description: Produce a research outline for a topic — items to investigate + angles (fields) to cover. First phase of the researcher workflow; feed output to research-deep. Trigger when the user says "research <topic>", "/research", "plan research on X", "วางแผน research", "วิจัย/ค้นคว้า เรื่อง X".
---

# research-outline

Stage 1 of 2 in the researcher workflow. Produces a human-readable outline (items + angles) that `research-deep` then expands into per-item notes.

Adapted from [Weizhena/Deep-Research-skills](https://github.com/Weizhena/Deep-Research-skills) — simplified to markdown-only output and Obsidian conventions; no YAML/JSON files.

## Inputs
- **Topic** — free text (e.g. "Thai loyalty platform competitors", "PDPA requirements for SaaS 2026", "event-driven architecture patterns")
- **Mode** — inferred or asked:
  - `project:<name>` → ties output to a project under `projects/<name>/10-research/`
  - `standalone` → second-brain mode, output to `knowledge/<topic-slug>/`
- **Time range** (optional) — "since 2025", "last 6 months", "unlimited". Defaults to "last 2 years" for tech topics, "unlimited" for foundational/academic.

## Process

1. **Clarify scope** via `AskUserQuestion` if not provided:
   - Project or standalone?
   - Time range?
   - Language for output? (default: match project-config, else Thai)
2. **Draft outline from model knowledge** — initial items + angles, no web yet.
3. **Web-search supplement** — one `WebSearch` call to catch items released/changed within the time range that the model might have missed. Quote cautiously; cite sources.
4. **Confirm with user** — show draft, ask:
   - Add/remove items?
   - Adjust angles?
   - Approve → save
5. **Save** to `<output-dir>/outline.md`

## Output location

```
project:<name>  →  vault/projects/<name>/10-research/<topic-slug>/outline.md
standalone      →  vault/knowledge/<topic-slug>/outline.md
```

Create directory if missing. `<topic-slug>` = kebab-case of topic.

## Output structure — `outline.md`

```markdown
---
type: research-outline
topic: <topic>
mode: project | standalone
project: <name>          # only in project mode
time-range: <as agreed>
language: <th|en>
created: <YYYY-MM-DD>
visibility: internal     # private|internal|public — inherit from project or default internal
tags:
  - research
  - research/outline
  - <topic-slug>
---

# Research outline — <topic>

> [!info] Scope
> Items: N · Angles: M · Time range: <range>
> Next: run `/research-deep` pointing at this outline to generate per-item notes.

## Items to investigate

Each item becomes its own note under `items/` when `research-deep` runs.

| # | Item | Category | Why include |
|---|---|---|---|
| 1 | <item> | <group> | <one-line rationale> |
| 2 | ... | | |

## Angles (fields to cover per item)

For each item, `research-deep` will try to fill these angles. Mark `detail: brief|moderate|detailed` per angle to control depth.

- **Basic info** (detailed): name, origin/author, first-release/year
- **Core idea / mechanism** (detailed): what it is, how it works
- **Strengths & weaknesses** (moderate)
- **Adoption / notable users** (brief)
- **Cost / licensing** (brief, if applicable)
- **Alternatives / overlaps** (moderate)
- **Recent changes** (within time range) (brief)
- **Sources** (detailed): 2-5 authoritative links per item

## Scope boundaries (what we're NOT covering)
- <out-of-scope 1>
- <out-of-scope 2>

## Open questions / assumptions to verify
- [ ] ...

## Sources consulted for this outline
- [<title>](<url>) — <one-line note>
- ...
```

## Rules

- **Model knowledge first, web second** — don't spam WebSearch for items the model already knows well; web fills the recency + edge cases.
- **Cite every non-trivial claim** — at minimum in §Sources; items or angles invented from web get inline citations.
- **Don't exceed ~20 items** — if the topic is broader, break into sub-topics; run the skill per sub-topic.
- **Don't exceed ~10 angles** — detail explodes in deep phase; trim ruthlessly.
- **Language:** all content in the user's language setting except proper nouns and technical IDs.
- **Visibility default:** inherit from project-config; standalone defaults to `internal`.

## Report format

```
Outline saved: knowledge/pdpa-saas-2026/outline.md
Topic: PDPA requirements for SaaS (2026)
Mode: standalone
Items: 8 | Angles: 6 | Time range: since 2024
Sources consulted: 5
Next: /research-deep knowledge/pdpa-saas-2026/outline.md
```

## Output conventions (Obsidian)

Follow [[obsidian-markdown]] conventions (see [skills/obsidian-markdown/SKILL.md](../obsidian-markdown/SKILL.md)):
- Frontmatter tags use hierarchy: `research/outline`, `research/deep` so Dataview can filter per stage
- Items reference future item notes as `[[items/<slug>]]` (links resolve after `research-deep` writes them)
- Use `> [!info]` / `> [!question]` callouts for scope and open questions
