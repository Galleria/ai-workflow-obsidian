# AI Workflow Vault

Obsidian vault + Claude Code skills for document-partner workflow:
**extract SOW → draft docs → diagram → Q&A → export → [client sign-off] → plan dev tasks.**

## Layout

```
vault/
├── skills/       Claude Code skills (auto-invoked on matching requests)
├── templates/    Fillable skeletons per doc type (Thai-first)
├── standards/    Reference notes: IEEE 830, BABOK, BPMN, PMBOK, ...
├── patterns/     Reusable chunks (auth flow, payment sequence, ...)
├── knowledge/    Second-brain: topic research not tied to a project (from research-outline/deep)
└── projects/     Per-project folders; each has project-config.md
```

## Core skills — canonical workflow order

| # | Skill | When to run | Triggered by |
|---|---|---|---|
| 0a | `research-outline` | optional — before SOW extract (industry/compliance/pattern research) or for standalone second-brain use | "/research <topic>", "research X for this project", "วิจัยเรื่อง X" |
| 0b | `research-deep` | after `research-outline`, expands the outline into per-item atomic notes | "/research-deep", "expand outline", "ลงลึก research" |
| 1 | `extract-sow` | after receiving SOW | "extract / parse this SOW or requirement doc" |
| 1a | `pre-draft-review` | after extract, **before** drafting — surfaces gaps that must be resolved for a reliable draft | "pre-draft review / ตรวจก่อน draft / what's missing before drafting" |
| 2 | `draft-doc` | after SOW extracted (and pre-draft gaps resolved) | "draft SRS / BRD / SDS / test-plan / WBS for <project>" |
| 3 | `make-diagram` | during/after drafting (for any doc needing a visual) | "diagram this flow / sequence / ERD" |
| 4 | `qa-review` | after a draft is finished | "review / ask questions / next plan for <doc>" |
| 5 | `export-doc` | before sending a doc to the client | "export / convert to docx / pdf for client delivery" |
| 5a | `update-doc` | after client meeting / MoM / email brings changes — bumps version, adds change markers, updates Revision History | "update / revise / bump / แก้ตาม MoM / apply changes to <doc>" |
| — | **⏸ CLIENT SIGN-OFF GATE** | **wait for client to confirm SRS / SDS** | — |
| 6 | `plan-tasks` | **only after sign-off** (or "provisional" for internal sizing) | "break down tasks / dev plan / sprint plan / แตก task" |

`update-doc` is the revision loop — runs as many times as needed between initial draft and sign-off (or post-signoff for CRs). Typical cycle: `qa-review` → client meeting → MoM → `update-doc` → repeat until accepted.

`pre-draft-review` runs a similar gap analysis **before** drafting starts — separating pre-draft questions (info PM needs to draft reliably) from post-draft questions (info client needs to confirm in the draft). Optional research-mode can invoke `research-outline`/`research-deep` to back §5 Suggested additions with cited sources.

`research-outline` + `research-deep` are **independent** of the document workflow — they can be used standalone (output lands in `knowledge/<topic>/`) for personal knowledge-base / second-brain needs, or project-scoped (`projects/<name>/research/<topic>/`) to inform SOW extraction / SRS NFR drafting.

Formatting skills (reference-only, invoked by other skills):
- `obsidian-markdown` — wikilinks, callouts, frontmatter, embeds syntax
- `obsidian-bases` — `.base` files for sortable/filterable views (optional)

**Why `plan-tasks` sits after the sign-off gate:** it produces an internal dev plan (estimates, dependencies, sprint allocation) derived from the SRS. If the SRS changes after client review, the dev plan has to be regenerated — running it too early wastes planning effort. Use `plan-tasks --provisional` terminology only for early SOW-sizing needs, and expect to re-run after sign-off.

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
3. Drop the SOW / TOR into `projects/<name>/_input/raw/` (binary originals; raw PDFs/DOCX stay gitignored)
4. Invoke skills conversationally in order:
   - "extract the SOW in `<name>`"
   - "draft SRS for `<name>`"
   - "diagram the points accrual flow for `<name>`" (as needed)
   - "QA review the SRS for `<name>`" → get client questions
   - "export SRS for `<name>` as docx" → send to client
5. **Wait for client sign-off** on SRS (and SDS, if applicable)
6. Only after sign-off: "plan dev tasks for `<name>`" → internal backlog + sprints

## Language

Default: Thai. Override per project in `project-config.md`.
