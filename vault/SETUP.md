# Setup guide — new machine

Steps to get this vault + AI workflow running on a fresh laptop.

---

## 1. Clone the repository

```bash
git clone <repo-url> <path>/ai-workflow-obsidian
cd <path>/ai-workflow-obsidian
```

If the repo isn't hosted remotely yet, copy the folder from the source machine.

---

## 2. Install Obsidian

- Download: https://obsidian.md/download
- Open Obsidian → **"Open folder as vault"** → select the `vault/` subfolder (not the repo root)

### Recommended community plugins

Settings → Community plugins → **Turn on** → Browse → install & enable:

| Plugin | Purpose | Required? |
|---|---|---|
| **Templater** | Use `library/templates/` as Obsidian-native inserts | Recommended |
| **Dataview** | Query across projects (e.g., list all open TBDs, all deliverables due this month) | Recommended |
| **PlantUML** | Render `plantuml` fenced blocks (for use-case / component diagrams) | If using PlantUML |
| **Diagrams** | Draw.io integration for BPMN / formal client diagrams | Optional |
| **Excalidraw** | Quick sketches during workshops | Optional |
| **Pandoc Plugin** | GUI alternative to the `export-doc` skill | Optional |

### Core plugin settings
- **Graph view:** optional, nice for visualizing links between projects/patterns/standards
- **Outgoing links, Backlinks:** on (default)
- **Templates (core):** set template folder to `library/templates/` if you want Obsidian's built-in template inserter

### Obsidian preferences
- **Files & Links** → Default location for new notes: `vault root` (or a scratch folder if you prefer)
- **Appearance** → Font: anything that renders Thai well (TH Sarabun New, Noto Sans Thai, IBM Plex Sans Thai)
- **Editor** → turn off auto-pair markdown syntax if it interferes with code fences

---

## 3. Install Claude Code

- Install from https://claude.ai/code (follow instructions for your OS)
- Sign in with your Anthropic account
- Open the repo folder (`ai-workflow-obsidian/`, not `vault/`) as the working directory — Claude Code will pick up any `.claude/` config

### Using the skills in this vault

Skills live at `vault/skills/<name>/SKILL.md`. They are **not auto-loaded** — invoke by telling Claude:

> "Use the skill at `vault/skills/extract-sow/SKILL.md` to extract the SOW for `<project>`."

Or paste the relevant SKILL.md content when asking Claude to do the task. See [vault/README.md](README.md) for the full skill list.

*(Optional upgrade later: create `.claude/skills/` symlink or move skills there for auto-discovery.)*

---

## 4. Install export tooling (optional — needed for `export-doc` skill)

Full guide: [skills/export-doc/install.md](skills/export-doc/install.md)

Quick summary:
```bash
# Windows
winget install --id JohnMacFarlane.Pandoc
npm install -g @mermaid-js/mermaid-cli

# macOS
brew install pandoc
npm install -g @mermaid-js/mermaid-cli
brew install --cask font-sarabun
```

For PDF export (optional):
- Windows: MiKTeX (https://miktex.org/download)
- macOS: `brew install --cask mactex-no-gui`
- Linux: `sudo apt install texlive-xetex texlive-lang-other`

Install **TH Sarabun New** font if not already present (required for Thai output).

Verify:
```bash
pandoc --version
mmdc --version
xelatex --version   # only if PDF
```

---

## 5. Configure git (if contributing)

```bash
git config user.name "Your Name"
git config user.email "you@example.com"
```

Respect the repo's `.gitignore` — it excludes `_export-build/` (regenerable) and optionally `_export/` (deliverables).

---

## 6. Smoke test

1. In Obsidian, open `vault/projects/sample-loyalty/document/srs.md` — Mermaid diagram in §2.1 should render
2. Open `vault/projects/sample-loyalty/diagram/erd-loyalty.md` — ERD visible
3. (If export tooling installed) from repo root:
   ```bash
   cd vault/projects/sample-loyalty
   pandoc document/srs.md -o _export/srs-test.docx --toc
   ```
   Open the DOCX in Word — should have headings, TOC, and tables. (Mermaid blocks will appear as code — run via the `export-doc` skill for pre-rendered diagrams.)

---

## 7. Start a real project

```bash
cp -r vault/projects/_example vault/projects/<client-slug>
```

Edit `project-config.md`, drop the real SOW / TOR into `_input/raw/`, and invoke skills:

1. "Extract the SOW for `<client-slug>`" → `extract-sow` produces `extracted.md`
2. "Draft SRS for `<client-slug>`" → `draft-doc` fills the template
3. "Diagram the points accrual flow for `<client-slug>`" → `make-diagram` outputs Mermaid
4. "QA review the SRS for `<client-slug>`" → `qa-review` produces client questions + risk register
5. "Export SRS for `<client-slug>` as docx" → `export-doc` produces DOCX for client

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Mermaid blocks show as code in Obsidian | Update Obsidian to latest; Mermaid support is built-in since v0.14 |
| Thai text broken in exported PDF | Must use `--pdf-engine=xelatex` + install TH Sarabun New |
| `mmdc` hangs or errors | Headless Chromium issue — see mermaid-cli README, may need `--puppeteerConfigFile` |
| DOCX styles look generic | Create a client reference DOCX; see `skills/export-doc/install.md` §6 |
| Claude Code doesn't know about the skills | Expected — reference them manually in chat, or move to `.claude/skills/` |
