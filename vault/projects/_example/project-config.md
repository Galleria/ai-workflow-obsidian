---
project: _example
created: 2026-04-13
visibility: private
tags:
  - project-config
---

# Project config

Skills read this file to adapt output per project. Copy this folder as the starting point for a new project.

## Meta
- **Client:** TBD
- **Language:** th  (th | en | th+en)
- **Standard level:** pragmatic  (strict | pragmatic)
  - `strict` → follow IEEE 830 / BABOK / PMBOK sections rigorously, load standards notes
  - `pragmatic` → use template skeleton, skip sections that don't apply
- **Visibility:** private  (private | internal | public)
  - `private` → contains client-confidential data (SOW, pricing, stakeholder names) — gitignored in shared vault repos
  - `internal` → vendor-team-only (drafts, QA reviews, dev plans) — commit to private team repo; exclude from public publishing
  - `public` → generic/anonymized (reference architectures, pattern case studies) — safe for public repo / blog
  - Rule: a project's effective visibility is the **most restrictive** of its files; files can override via their own frontmatter `visibility:`

## Sign-off tracking
> Used by `plan-tasks` to decide between post-signoff vs provisional output.

| Doc | Version | Signed off? | Date | By whom |
|---|---|---|---|---|
| SOW | — | — | — | — |
| SRS | — | — | — | — |
| SDS | — | — | — | — |

## Deliverables (check what applies)
- [ ] SOW extract
- [ ] BRD
- [ ] SRS
- [ ] SDS / SAD
- [ ] Workflow diagram
- [ ] Sequence diagram
- [ ] Use-case diagram
- [ ] ERD
- [ ] Test plan / UAT
- [ ] WBS / Project plan

## Diagram tool per type
| Type | Tool |
|---|---|
| Workflow / BPMN | mermaid |
| Sequence | mermaid |
| Use-case | plantuml |
| ERD | mermaid |
| Component / Architecture | plantuml |
| Class | mermaid |
| Gantt / WBS | mermaid |

Override any row if the client has a preference.

## Folder convention inside this project
```
_input/                         client-submitted materials (SOW/TOR/brief)
├── raw/                          original PDFs/DOCX (private, gitignored)
└── extracted.md                  extract-sow output

research/                       research-outline/deep, scoped to this project
├── <topic>/outline.md
└── <topic>/items/

qa/                             review artifacts
├── pre-draft-review-<date>.md    pre-draft-review output (before drafting)
└── <doc>-review-<date>.md        qa-review output (after drafting)

mom/                            meeting records
├── source/                       raw notes, transcripts, agenda
├── result/                       structured MoM (from draft-doc mom-template)
└── assets/                       slides, whiteboard photos

document/                       all drafted docs (srs/brd/sds/wbs/test-plan/sow/tor/…)
├── <doctype>.md                  current version
├── <doctype>-v0.2.md             version series side-by-side (from update-doc)
└── change-log.md                 optional summary across versions

diagram/                        all diagrams (Mermaid .md / PlantUML .puml / draw.io)
└── <type>-<slug>.md

planning/                       post-signoff plans
├── dev-tasks.md                  plan-tasks output
└── sprint-notes/                 per-sprint notes

assets/                         shared project-level images, logos
_export/                        DOCX / PDF final deliverables (gitignored — regenerable)
```

**Naming conventions:**
- Prefix `_` for system/non-content folders (`_input`, `_export`) — keeps them pinned in Obsidian file tree
- Singular form (`document`, `diagram`) — not plural
- Doc versions live side-by-side: `srs.md` + `srs-v0.2.md` + `srs-v0.3.md` so reviewers can compare
- Date format `YYYY-MM-DD` in file names for chronological sort

## Export (for `export-doc` skill)
- **Reference DOCX:** [unset — uses pandoc default]  (path relative to vault/, e.g. `_assets/<client>-brand-reference.docx`)
- **Thai font:** `TH Sarabun New`
- **PDF engine:** `xelatex`
- **Pandoc extra args:** (none)

## Notes
Free-form context: stakeholders, deadlines, constraints, domain glossary.
