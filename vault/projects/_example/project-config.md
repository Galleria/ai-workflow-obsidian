---
project: _example
created: 2026-04-13
---

# Project config

Skills read this file to adapt output per project. Copy this folder as the starting point for a new project.

## Meta
- **Client:** TBD
- **Language:** th  (th | en | th+en)
- **Standard level:** pragmatic  (strict | pragmatic)
  - `strict` → follow IEEE 830 / BABOK / PMBOK sections rigorously, load standards notes
  - `pragmatic` → use template skeleton, skip sections that don't apply

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
00-sow/        raw SOW + extracted.md
10-brd/
20-srs/
30-sds/
40-diagrams/
50-test-plan/
60-wbs/
99-qa/         questions, decisions, next-plan logs
_export/       DOCX / PDF deliverables produced by export-doc (git-ignore optional)
```

## Export (for `export-doc` skill)
- **Reference DOCX:** [unset — uses pandoc default]  (path relative to vault/, e.g. `_assets/<client>-brand-reference.docx`)
- **Thai font:** `TH Sarabun New`
- **PDF engine:** `xelatex`
- **Pandoc extra args:** (none)

## Notes
Free-form context: stakeholders, deadlines, constraints, domain glossary.
