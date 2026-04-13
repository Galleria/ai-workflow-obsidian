# AI Workflow Vault

Obsidian vault + Claude Code skills for document-partner workflow:
**extract SOW → summarize → diagram → draft docs → Q&A / next plan.**

## Layout

```
vault/
├── skills/       Claude Code skills (auto-invoked on matching requests)
├── templates/    Fillable skeletons per doc type (Thai-first)
├── standards/    Reference notes: IEEE 830, BABOK, BPMN, PMBOK, ...
├── patterns/     Reusable chunks (auth flow, payment sequence, ...)
└── projects/     Per-project folders; each has project-config.md
```

## Core skills (phase 1)

| Skill | Triggered by |
|---|---|
| `extract-sow` | "extract / parse this SOW or requirement doc" |
| `draft-doc` | "draft SRS / BRD / SDS / test-plan / WBS for <project>" |
| `make-diagram` | "diagram this flow / sequence / ERD" |
| `qa-review` | "review / ask questions / next plan for <doc>" |
| `plan-tasks` | "break down tasks / dev plan / sprint plan / แตก task" |
| `export-doc` | "export / convert to docx / pdf for client delivery" |

One `draft-doc` skill handles all document types — it reads the project's
`project-config.md`, picks the matching template from `templates/`, optionally
loads the relevant `standards/` note, and fills sections from extracted
requirements.

`export-doc` requires local install of `pandoc` + `mmdc` (mermaid-cli) — see
[skills/export-doc/install.md](skills/export-doc/install.md). PDF export
additionally needs XeLaTeX and a Thai font (TH Sarabun New recommended).

## Adding a new project

1. `cp -r projects/_example projects/<name>`
2. Edit `projects/<name>/project-config.md` — set language, standard level, diagram tools, deliverables
3. Drop the SOW into `projects/<name>/00-sow/`
4. Invoke skills conversationally, e.g. "extract the SOW in acme-crm", then "draft SRS for acme-crm"

## Language

Default: Thai. Override per project in `project-config.md`.
