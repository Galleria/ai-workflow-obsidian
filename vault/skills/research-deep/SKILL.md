---
name: research-deep
user-invocable: true
allowed-tools: Read, Write, Glob, WebSearch, WebFetch, Task, AskUserQuestion
description: Expand a research outline into one atomic markdown note per item, filling the angles defined in the outline. Stage 2 of the researcher workflow. Trigger when the user says "deep research", "/research-deep", "expand outline", "fill research notes", "ลงลึก research", "ค้นต่อจาก outline".
---

# research-deep

Stage 2 of 2. Reads an outline produced by `research-outline`, researches each item via parallel `WebSearch`/`WebFetch` calls, and writes one atomic markdown note per item alongside an index.

Adapted from [Weizhena/Deep-Research-skills](https://github.com/Weizhena/Deep-Research-skills) — simplified: markdown per item instead of JSON, no validation script, uses built-in Claude web tools instead of Exa.

## Inputs
- **Outline path** — defaults to the most recent `outline.md` under `knowledge/` or `projects/*/10-research/` (ask if ambiguous)
- **Parallelism** — how many items to research concurrently (default: 3; ask if outline has >10 items)

## Process

1. **Locate outline** — glob for `*/outline.md`; if multiple candidates, use `AskUserQuestion` to pick
2. **Parse outline** — extract topic, items, angles, time range, language, visibility
3. **Resume check** — scan `<outline-dir>/items/` for existing `<item-slug>.md`; skip items already written (unless user says refresh)
4. **Batch** — group remaining items by `parallelism`, launch one `Task` sub-agent per item with the per-item prompt (template below)
5. **Wait for batch** → save each sub-agent's return as `items/<item-slug>.md`
6. **Write index** — `<outline-dir>/index.md` aggregating all item notes
7. **Report** — completed/skipped/failed counts + any items flagged `#uncertain`

## Per-item sub-agent prompt template

```
Research the item below and return a single atomic markdown note filling the angles from the outline.

Topic: {topic}
Item: {item_name}  (category: {item_category})
Why we chose it: {item_rationale}
Time range: {time_range}
Language: {language}

Angles to cover (from outline):
{angles_list_with_detail_levels}

Rules:
- Fill each angle with sourced content. Any claim that isn't common knowledge gets an inline citation [^1].
- If an angle can't be filled confidently, write the best-effort answer prefixed with `#uncertain` tag and explain the gap.
- Use 3-6 WebSearch / WebFetch calls total. Prefer primary sources (official docs, vendor blogs, standards bodies) over aggregators.
- **After the factual angles**, include a `> [!opinion]` callout with the AI's take — 2-4 sentences of judgment (when to adopt, what's the catch, how it compares to siblings). Clearly NOT fact; tagged `#opinion` so readers can filter. This is optional if the topic is purely definitional.
- Output structure:

---
type: research-item
topic: {topic}
item: {item_name}
category: {item_category}
parent: "[[outline]]"
researched: {YYYY-MM-DD}
tags:
  - research
  - research/deep
  - {topic_slug}
  - {category_slug}
---

# {item_name}

> [!summary]
> 2-3 sentence TL;DR.

## Basic info
...

## Core idea / mechanism
...

## Strengths
...

## Weaknesses
...

## <other angles from outline>
...

## Related
- [[items/<sibling-item-slug>]] — one-line relation
- [[outline]]

## Sources
[^1]: [<title>](<url>) — accessed {YYYY-MM-DD}
[^2]: ...

> [!opinion] AI take #opinion
> 2-4 sentences of judgment: when to use this vs alternatives, what's the real catch, what most write-ups miss. Flag clearly as model opinion, not fact.

Return ONLY the markdown. Do not write files; the caller writes it.
```

## Index file — `<outline-dir>/index.md`

```markdown
---
type: research-index
topic: <topic>
parent: "[[outline]]"
item-count: N
generated: <YYYY-MM-DD>
tags:
  - research
  - research/index
  - <topic-slug>
---

# <topic> — research index

> [!info]
> Generated from [[outline]] on <date>. See individual items below.

## Items

| # | Item | Category | Summary | Uncertain? |
|---|---|---|---|---|
| 1 | [[items/<slug>\|<name>]] | <cat> | <one-line from summary callout> | — or #uncertain count |
| 2 | ... | | | |

## Cross-cutting findings
<synthesis paragraph if patterns emerged across items — e.g. "all Thai loyalty platforms launched after 2024 use point-expiry mechanics"; skip if nothing to say>

## Open threads
- #uncertain items worth revisiting
- Follow-up topics that surfaced
```

## Output file layout

```
<outline-dir>/               (from research-outline, e.g. knowledge/pdpa-saas-2026/)
├── outline.md               (input — produced by research-outline)
├── index.md                 (NEW — summary + links to items)
└── items/
    ├── <item-1-slug>.md
    ├── <item-2-slug>.md
    └── ...
```

## Rules

- **One file per item** — atomic notes are the point; don't merge items into a mega-doc
- **Cite every non-trivial claim** — inline footnote pointing to a real URL
- **`#uncertain` is first-class** — better to flag a gap than confabulate; uncertain items should be re-run later
- **`> [!opinion] #opinion` is encouraged** — after the factual angles, 2-4 sentences of AI judgment (when to adopt, what most write-ups miss). Keep it clearly separated from fact so readers can filter.
- **Respect outline's angle list** — don't add angles the outline didn't authorize (drift breeds inconsistency across items)
- **Respect visibility** — if outline is `private`, pass that through to every item's frontmatter
- **Don't exceed parallelism** — WebSearch is rate-limited; 3 is a safe default, 5 is aggressive
- **Resume is default** — re-running `research-deep` on a half-complete outline picks up where it left off; pass `refresh: true` to regenerate everything

## Full-source capture (optional, via markitdown)

By default sub-agents use `WebSearch` + `WebFetch` (which returns a model-summarized markdown). If the user needs **full source text** (e.g. for long papers, legal docs, or to feed a separate extract pipeline), pass a URL to [markitdown](https://github.com/microsoft/markitdown) via Bash:

```bash
markitdown "https://example.com/paper.pdf" -o knowledge/<topic>/_sources/paper.md
```

Then Read the converted file. Markitdown also handles PDFs, HTML, YouTube transcripts, EPub — useful for pulling primary sources verbatim. Install: see [skills/extract-sow/install.md](../extract-sow/install.md) (shared dependency).

## Report format

```
Deep research complete: knowledge/pdpa-saas-2026/
Items: 8 total (7 written, 1 skipped existing, 0 failed)
Uncertain flags: 3 items have #uncertain (see index.md)
Index: knowledge/pdpa-saas-2026/index.md
Next: review uncertain items, or feed into extract-sow / draft-doc if project-scoped
```

## Output conventions (Obsidian)

Follow [[obsidian-markdown]] conventions (see [skills/obsidian-markdown/SKILL.md](../obsidian-markdown/SKILL.md)):
- Items link back to outline via `parent: "[[outline]]"` frontmatter — Obsidian renders a backlink automatically
- Index uses Dataview-friendly tags (`research/index`, `research/deep`) so a vault-wide dashboard query can aggregate across topics
- Sources use footnote syntax (`[^1]`) — renders cleanly in Obsidian reading view and survives pandoc export
- Cross-item "Related" links help the graph view show clusters within a topic

## When standalone vs project

- **Standalone (second brain)**: output under `knowledge/` — no project-config lookup; visibility defaults to `internal`
- **Project-scoped**: output under `projects/<name>/10-research/` — inherit language + visibility from `project-config.md`; optionally feed findings into the project's `extracted.md` or SRS draft manually (not auto)
