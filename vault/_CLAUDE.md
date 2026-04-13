# Vault operating manual

Always-loaded context for Claude when working in this vault. Keep short — every token here is paid on every turn.

## How this vault is organized

```
vault/
├── _CLAUDE.md          YOU ARE HERE — operating manual
├── README.md           human-facing overview (don't re-read unless asked)
├── SETUP.md            Obsidian plugin setup (skip unless troubleshooting)
├── dashboard.md        live Dataview queries (read on request only)
├── skills/             Claude Code skills (read the one being invoked, not all)
├── templates/          doc skeletons — read only when draft-doc is active
├── standards/          reference notes (IEEE 830, etc) — load on demand
├── patterns/           reusable chunks — load on demand
├── knowledge/          topic research (second brain) — START with index.md per topic
└── projects/           per-project folders — read project-config.md first
```

## Read-order rules (token discipline)

**When the user asks about a knowledge topic:**
1. Read `knowledge/README.md` — catalog (1-line per topic)
2. Read `knowledge/<topic>/index.md` — TL;DR + cross-cutting synthesis
3. Only load `knowledge/<topic>/items/<slug>.md` when the user digs into that specific angle

**When the user asks about a project:**
1. Read `projects/<name>/project-config.md` — always
2. Read `projects/<name>/00-sow/extracted.md` if present
3. Load specific deliverables (srs.md, sds.md, etc.) only when relevant

**When the user invokes a skill:**
1. Read only that skill's `SKILL.md`
2. Skill may reference `[[obsidian-markdown]]` for output conventions — load only if producing vault-output that needs it

**Never:**
- Load all files in a directory to "get oriented" — use `index.md` / `README.md` entry points
- Read templates unless `draft-doc` is the active skill
- Re-read this file or `vault/README.md` once they're in context

## Core conventions (condensed)

- **Wikilinks** `[[target]]` for vault-internal refs; standard markdown links for URLs
- **Frontmatter required** on every non-trivial note: `type`, `tags`, `visibility`, doc-specific keys
- **Visibility**: `private` (client-confidential) | `internal` (team) | `public` (shareable). Default = inherit from project or `internal`
- **Callouts for semantics**: `> [!summary]` TL;DR, `> [!opinion]` AI judgment (not fact), `> [!warning]` blockers, `> [!question]` for client, `> [!quote]` for raw source excerpts
- **Tags hierarchy**: `research/outline`, `research/deep`, `research/index`; `pkm/method`, `pkm/anti-pattern`; `risk/high`, `risk/medium`, `risk/low`
- Full syntax in `[[obsidian-markdown]]` — load only when writing vault content

## Skill catalog (one-line each)

| Skill | Purpose |
|---|---|
| `research-outline` | Stage 1: outline items + angles on a topic |
| `research-deep` | Stage 2: expand outline into per-item atomic notes (parallel web search) |
| `extract-sow` | Parse SOW/requirement doc → normalized `extracted.md` |
| `draft-doc` | Fill a template (SRS/BRD/SDS/…) from extracted requirements |
| `make-diagram` | Generate Mermaid/PlantUML/drawio diagram |
| `qa-review` | Review a draft → client questions + risks + next plan |
| `export-doc` | MD → DOCX/PDF via pandoc + mmdc |
| `update-doc` | Apply revisions (from MoM, client feedback) → bump version, mark changes, update Revision History |
| `plan-tasks` | SRS → developer task backlog + Gantt (post-signoff only) |
| `obsidian-markdown` | Reference skill for Obsidian syntax (wikilinks, callouts, frontmatter) |
| `obsidian-bases` | Reference skill for `.base` sortable views (optional) |

## When in doubt

Ask the user. Don't scan the vault top-to-bottom to figure out intent — that burns tokens and usually misses nuance.
