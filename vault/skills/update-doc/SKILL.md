---
name: update-doc
description: Revise an existing project document (SRS / BRD / SDS / test-plan / WBS / etc.) using a source of changes — typically a Minutes of Meeting (MoM), client email, or explicit change list. Produces the next version of the doc with inline change markers, an updated Revision History, and bumped version number. Trigger when the user asks to "update / revise / bump version / อัปเดต / แก้ตาม MoM / apply changes to <doc>".
tags:
  - skill
  - update
  - revision
---

# update-doc

Closes the loop between `qa-review` (which produces client questions) and `export-doc` (which re-ships the doc for sign-off). Consumes a **change source** and applies those changes to an existing drafted doc.

## Inputs

- **Target doc path** — the doc to update. Fully path-agnostic; accepts any of:
  - `projects/<name>/20-srs/srs.md` (numbered-folder convention)
  - `projects/<name>/document/srs.md` (flat convention)
  - Any other path under the vault
- **Change source** — one of:
  - **MoM file path** — e.g. `projects/<name>/mom/result/2026-05-20-srs-review.md`
  - **Explicit change list** — pasted by the user in chat (bullet list of changes, decisions, resolved TBDs)
  - **QA-answers file** — a QA review file whose questions have been answered inline
  - **Email / free-form text** — user pastes client's email into chat
- **New version** (optional) — defaults to auto-bump (0.1→0.2 minor, 0.x→1.0 on first sign-off, 1.0→1.1 post-signoff). Allow override.

## Process

1. **Load target doc**
   - Read the doc
   - Parse frontmatter: grab current `version`
   - Parse Revision History table at the bottom — confirm version matches
2. **Load change source**
   - If MoM: extract the **decisions** and **resolved questions** sections (MoM template structure: agenda / decisions / action items / answers-to-questions)
   - If change list or free-form: parse bullets into `{section-ref, new-content, rationale}` tuples; if section-ref missing, infer by keyword match + ask user to confirm ambiguous mappings
3. **Check scope gate**
   - If the doc has `signed-off: true` in project-config.md sign-off table and user hasn't explicitly said "post-signoff change" / "change request", **warn + ask**:
     > "Target doc is signed off (v<ver>). Updating it will bump to v1.1 (post-signoff revision) and should be tracked via formal CR. Proceed?"
   - Otherwise continue
4. **Apply changes**
   - For each change: find the matching section(s) in the doc, revise content in place
   - Leave a **visible marker** at each changed section (Obsidian callout — see conventions below)
   - If a change **adds** a new section, create it at the logical position + mark as new
   - If a change **removes** a section, convert to a strikethrough placeholder + mark removed (don't fully delete — preserve reviewer's ability to see what left)
   - Resolve `[TBD: ...]` and `[ต้องยืนยัน: ...]` placeholders that are now answered — remove marker, fill content, tag the paragraph with a "changed" callout
5. **Bump version + update Revision History**
   - Increment version per policy (or use override)
   - Append a row to the Revision History table: version, date, author, summary of changes, source reference (MoM path or "client email 2026-05-20")
   - Update `version:` in frontmatter
6. **Save strategy**
   - **Default:** save as `<original-name>-v<new-version>.md` in the same folder (preserves previous version side-by-side for Obsidian browsing)
   - **Override:** if user says "overwrite" or if project-config has `update-strategy: overwrite`, replace in place (relies on git for history)
   - Never modify the previous version file
7. **Report** summary: version jump, N sections changed, questions resolved, outstanding TBDs remaining

## Inline change markers (Obsidian callouts — see [[obsidian-markdown]])

At the top of each materially changed section, add an Obsidian callout:

```markdown
## 3.2 Functional Requirements

> [!note]+ Changed in v0.2
> - FR-004 tier criteria resolved per MoM 2026-05-20: ยอดซื้อสะสม 12 เดือน
> - Added FR-013 loyalty partnership hook (phase 2 placeholder)
> Source: [[2026-05-20-srs-review|MoM 2026-05-20]]

### FR-004 Tier management
- **Priority:** M
- **เกณฑ์ upgrade/downgrade:** ใช้ยอดซื้อสะสม 12 เดือน rolling window (เปลี่ยนจาก [TBD] v0.1)
  - Silver → Gold: ≥ 50,000 THB/year
  - ...
```

Tag newly added content inline with `#changed-v0.2` so Dataview can surface all changed items:

```markdown
### FR-013 External loyalty partnership hook #changed-v0.2 #phase-2
...
```

Strikethrough for removed content (don't delete immediately — reviewer confirms):

```markdown
> [!warning]+ Removed in v0.2
> Section "3.4.2 Hardware Interfaces" deprecated — N/A for cloud-hosted deployment (MoM 2026-05-20 §decisions)

~~### 3.4.2 Hardware Interfaces~~
~~[original content...]~~
```

When v0.2 is accepted by reviewer, a later `update-doc` (or manual cleanup) can remove the strikethrough/markers. For now, visible markers make review fast.

## Revision History table update

Append (don't overwrite) a row:

| เวอร์ชัน | วันที่ | ผู้แก้ไข | รายการเปลี่ยนแปลง | แหล่งอ้างอิง |
|---|---|---|---|---|
| 0.1 | 2026-04-13 | 2Smooth | Initial draft | SOW v1 |
| 0.2 | 2026-05-21 | 2Smooth | แก้ FR-004 tier criteria, เพิ่ม FR-013, ลบ 3.4.2 | [[2026-05-20-srs-review\|MoM 2026-05-20]] |

## Version bump policy (default, overridable)

| Current | Trigger | Next |
|---|---|---|
| 0.1 | any update before sign-off | 0.2, 0.3, ... |
| 0.x | sign-off reached | 1.0 |
| 1.0 | post-signoff minor revision | 1.1, 1.2, ... |
| 1.x | major rework / new scope added (CR) | 2.0 |

User can override by asking: "update SRS to v0.3" or "bump to 1.0".

## Rules

- **Never silently discard content.** Strikethrough > delete. Let the reviewer confirm removal in the next cycle.
- **Always link back to source of change** (MoM wikilink, or "client email 2026-05-20" in the history row).
- **Don't re-run `draft-doc`** on an existing doc — that regenerates from scratch and loses review traceability. `update-doc` is for surgical changes.
- **Language:** match the target doc's language (most likely Thai).
- **If change source contradicts SOW:** flag as an internal finding ("change extends scope beyond SOW §X — CR needed?"), don't silently apply. Echo to the user for confirmation.
- **If MoM has unresolved decisions:** apply what's decided, keep the rest as TBD with the specific question verbatim — next meeting's MoM can resolve.

## Common scenarios

| Scenario | How to handle |
|---|---|
| MoM answers 3 of 5 QA questions | Apply 3; leave 2 as TBD with MoM reference in §"Open questions" |
| Client email requests UI change | Find UI section in SRS §3.1.1 (or wherever); update; cite email |
| Workshop decides new FR | Insert at logical position (after last existing FR in same module); call out as `#new` and `#changed-v0.2` |
| Tier criteria decided (was TBD) | Replace `[ต้องยืนยัน: ...]` marker inline + add the change callout at section header |
| Scope creep via client ask | Warn user about SOW deviation; if they confirm, apply + log in internal findings (could surface later in `qa-review` as CR discussion) |

## Report format

```
Updated: projects/<name>/document/srs-v0.2.md
Previous: projects/<name>/document/srs.md (kept)
Version: 0.1 → 0.2
Source:  projects/<name>/mom/result/2026-05-20-srs-review.md
Sections changed: 4 (§2.5, §3.2 FR-004, §3.2 new FR-013, §3.4 removed hw iface)
Questions resolved: 3 of 5 (Q1, Q2, Q5 from QA review v0.1)
Still open:       2 (Q3, Q4 — left as TBD with MoM reference)
Scope warnings:   1 (FR-013 partnership hook extends SOW §3 "out of scope phase 2" — review with PM)
Next: qa-review on v0.2 if changes are material, then export-doc
```

## Output conventions (Obsidian)

Follow [[obsidian-markdown]] conventions (already referenced by all other skills):
- Change callouts use `> [!note]+ Changed in v<ver>` and `> [!warning]+ Removed in v<ver>`
- Inline tags `#changed-v0.2`, `#new`, `#removed` on flagged items so Dataview aggregates them
- Source references to MoM / email use wikilinks: `[[<mom-note-name>]]`
- Preserve frontmatter keys; only update `version` field

## Where to run

This skill consumes project-config.md's sign-off table and writes back into the same project folder. Works with any folder convention:
- Numbered (`20-srs/srs.md`, `99-qa/...`)
- Flat (`document/srs.md`, `mom/result/...`)
- Mixed

No changes to project-config.md are required.
