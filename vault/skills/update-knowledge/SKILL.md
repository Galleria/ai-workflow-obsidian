---
name: update-knowledge
user-invocable: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebSearch, WebFetch, AskUserQuestion
description: Update existing knowledge notes with new information — ingest a source (URL / PDF / conversation note), rewrite affected items, surface contradictions, and refresh the topic index. Trigger when the user says "update knowledge on X", "ingest this into <topic>", "refresh <topic>", "add this to my second brain", "อัปเดต knowledge เรื่อง X".
---

# update-knowledge

Keeps the second-brain living. Takes new information (URL, PDF, conversation insight, or freeform notes), folds it into an existing `knowledge/<topic>/` folder, and refreshes the index.

Complements `research-outline` + `research-deep` (which *create* new topics). This skill *updates* topics that already exist.

## Inputs
- **Source** — one of:
  - URL to fetch
  - Path to local file (md, pdf, docx, …)
  - Freeform text the user pasted / dictated
  - `--from-diff` → pull recent vault changes (via `git diff`) and fold implications into relevant knowledge notes
- **Topic** — `knowledge/<topic-slug>/` that should receive the update (ask if ambiguous via `AskUserQuestion`)
- **Mode** (optional):
  - `append` (default) — add findings to existing items; don't rewrite
  - `merge` — rewrite affected sections, resolve contradictions
  - `refresh` — full re-synthesis of index.md (keeps items untouched)

## Process

1. **Locate topic** — glob `knowledge/*/index.md`; if multiple candidates for the user's intent, ask
2. **Read current state** — `outline.md` + `index.md` + existing `items/*.md`
3. **Ingest source**:
   - URL → `markitdown <url> -o knowledge/<topic>/_raw/<slug>-<date>.md` (or `WebFetch` if markitdown unavailable)
   - Local file → `markitdown` if non-md; else Read directly
   - Text blob → save to `knowledge/<topic>/_raw/note-<date>.md`
   - `--from-diff` → `git log --oneline -20` + `git diff HEAD~5..HEAD` scoped to relevant paths
4. **Analyze delta**:
   - Extract claims from source
   - Match against existing items → flag (a) **supports** existing claim, (b) **contradicts**, (c) **new angle not covered**
5. **Apply updates** per mode:
   - `append`: add new findings as dated subsection (e.g. `## Update 2026-04-13`) within existing item; don't touch prior text
   - `merge`: rewrite affected angle; if contradiction, preserve old text under `> [!warning] Previous understanding (superseded 2026-04-13)` with rationale
   - `refresh`: regenerate `index.md` TL;DR + cross-cutting findings + AI opinion, leaving items alone
6. **Update index.md**:
   - Bump `generated:` frontmatter date
   - Add to a `## Changelog` section (append-only log of what changed when)
7. **Report**:
   - Files touched (list)
   - Contradictions surfaced (with line refs)
   - New items that should be added via `research-deep` (if the source raised topics not covered)

## Output conventions

Follow [[obsidian-markdown]]:
- Updates add `> [!info] Updated YYYY-MM-DD` callout at top of modified item (or section)
- Superseded content wrapped in `> [!warning] Previous understanding (superseded YYYY-MM-DD)` — preserve history, don't silently delete
- New sources append to the item's `## Sources` footnotes block (don't renumber — add `[^N+1]`)
- Contradictions tagged `#contradiction` inline so searches find them

## Changelog format in index.md

```markdown
## Changelog

- **2026-04-13** (append) — Added POC implementation notes to [[items/obsidian-claude-patterns]] based on our `_CLAUDE.md` + markitdown integration work. Consolidated AI take updated with real-world trade-offs.
- **2026-04-10** (merge) — Updated [[items/evergreen-notes]] per Matuschak's 2026 essay; superseded 2-sentence definition with expanded principle list.
```

## Rules

- **Never silently overwrite** — preserve prior claims under `superseded` callouts so the note keeps provenance. Deletion only if user explicitly says "remove"
- **One source → one atomic update** — don't fold 5 sources at once; batch separately so git diff stays reviewable
- **Dated updates** — every modified section gets a YYYY-MM-DD stamp so readers can trace evolution (bi-temporal tracking lite)
- **Touch `last-accessed: <today>`** on every item you read or modify (supports forgetting-curve decay)
- **`_raw/` is immutable** — once a source is saved to `_raw/`, never rewrite it; if you need a cleaned version, save alongside as `<slug>-cleaned.md`
- **Respect visibility** — if topic is `public`, check incoming source doesn't leak private content; if incoming is private, flag and ask
- **Don't trigger re-research** — if a source introduces a whole new item, don't expand it inline; report to user so they can run `research-deep` with full discipline

## Confidence updates

Every modified item gets a `confidence` recalculation (frontmatter field, 0.0-1.0):

| Situation | Action |
|---|---|
| Source **corroborates** existing claim with independent evidence | +0.05 to +0.1 (cap at 0.95) |
| Source **adds new angle** not previously covered | no change (new content is default 0.7) |
| Source **contradicts** existing claim | -0.1 to -0.2 pending resolution; tag `#contradiction` |
| Source is **primary/authoritative** (official docs, original author) | may push above 0.9 if ≥2 primary sources agree |
| Source is low-quality (unsourced blog, AI-generated) | small or no raise (+0 to +0.03) |

Update the `confidence/low`, `confidence/medium`, `confidence/high` tag accordingly (low: <0.6, medium: 0.6-0.8, high: >0.8).

## Contradiction resolution protocol

When a new source contradicts an existing claim, apply in order:

1. **Detect** — identify the specific claim (quote both versions).
2. **Preserve both** — wrap old claim in `> [!warning] Previous understanding (superseded <date>)` callout with source ref; write new claim as primary.
3. **Tag** — add `#contradiction` inline at the disagreement point AND to the item's frontmatter tags array.
4. **Classify** the contradiction:
   - **Version drift** (old claim was correct at time T1, new claim correct at T2) → mark old as `historical`, new as current. Keep both, no further action.
   - **Authority conflict** (two sources disagree, both current) → preserve both with source attribution; DO NOT resolve unilaterally. Report to user.
   - **Clear override** (new primary source from the original author directly refutes old secondary source) → supersede cleanly; note the asymmetry.
5. **Drop confidence** by -0.1 to -0.2 until user reviews (or until a third source tips the balance).
6. **Report** to user with: file path, line refs, claim quotes, suggested classification. User makes final call on unresolved conflicts.

Exception: if the contradiction is trivial (typo, numeric precision, synonym) and the new source is clearly more authoritative, resolve silently and note in changelog.

## Forgetting-curve decay

Knowledge doesn't delete — it demotes. Per note:

- `last-accessed` bumps on any read/edit via this skill
- `review-after` = `last-accessed + N days` where N depends on confidence:
  - High (>0.8) → 180 days (stable knowledge, revisit less)
  - Medium (0.6-0.8) → 90 days (default)
  - Low (<0.6) → 30 days (speculative, worth re-checking)
- When `review-after` < today, note is **stale** — surfaced in dashboard Dataview queries for review
- Stale ≠ wrong; just needs a fresh look. On review, user either touches `last-accessed` (re-affirm) or runs `update-knowledge refresh` on the topic
- **Never auto-demote or auto-delete** — the user reviews and decides

## When to use which mode

| Situation | Mode |
|---|---|
| New blog post that confirms existing knowledge | `append` |
| New paper that overturns prior claim | `merge` (with supersede block) |
| Just realized the synthesis needs re-thinking (no new source) | `refresh` |
| Implementing something from the research, learned real trade-offs | `append` → add "Real-world notes" subsection |
| Multiple small inputs across a week | Run once per input in `append` mode; weekly `refresh` to resynthesize index |

## Report format

```
Updated: knowledge/living-knowledge-base/
Mode: append
Source: vault changes since 2026-04-13 (git diff)
Files touched: 2
  - items/obsidian-claude-patterns.md — added POC implementation subsection
  - index.md — bumped changelog, consolidated opinion refined
Contradictions: 0
New items suggested: 1 (`markitdown-ingestion-pattern`) — run research-deep if desired
```
