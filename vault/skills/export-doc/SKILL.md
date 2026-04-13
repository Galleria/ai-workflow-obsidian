---
name: export-doc
description: Convert a vault Markdown document (SRS/BRD/SDS/QA review/etc.) to DOCX (default) or PDF for client delivery, pre-rendering Mermaid/PlantUML diagrams to images and applying Thai font + client brand template if configured. Trigger when the user asks to "export", "convert", "render", "output", "ส่งออก", "แปลงเป็น word/docx/pdf" a document.
---

# export-doc

Wraps **Pandoc** + **mermaid-cli** so clients get Word/PDF while we keep Markdown as source of truth.

## Prerequisites

See `install.md` in this skill folder. Required on PATH:
- `pandoc` (≥ 3.0)
- `mmdc` (mermaid-cli, for diagram → PNG)
- (optional, PDF only) a TeX engine — XeLaTeX recommended for Thai
- (optional) `plantuml.jar` + `java` — for PlantUML diagrams

Before running, **verify** each tool:
```bash
pandoc --version
mmdc --version
```
If missing, stop and tell the user to run the `install.md` steps.

## Inputs
- **Source md path** — e.g. `projects/sample-loyalty/20-srs/srs.md`
- **Target format** — `docx` (default) | `pdf`
- **Project name** (auto-detected from path if under `projects/<name>/...`)

## Process

1. **Load config**
   - Read `projects/<name>/project-config.md` → `export` block (see schema below)
   - Resolve: reference-docx path, Thai font, PDF engine
2. **Pre-render diagrams**
   - Create `<source-dir>/_export-build/` working dir
   - For each fenced ```mermaid ... ``` block in the source md:
     - Extract to `diagram-NN.mmd`
     - Run `mmdc -i diagram-NN.mmd -o diagram-NN.png -w 1600 -b transparent`
     - Replace the fenced block with `![](_export-build/diagram-NN.png)` in a **copy** of the source (don't modify original)
   - For ```plantuml blocks: same approach with `plantuml` CLI (skip if not installed, emit warning)
3. **Run pandoc**
   - `docx`:
     ```bash
     pandoc <prepared.md> -o <output.docx> \
       --reference-doc="<reference-docx>" \
       --toc --toc-depth=3 \
       --from=markdown+yaml_metadata_block \
       --resource-path=.
     ```
   - `pdf` (xelatex, Thai-capable):
     ```bash
     pandoc <prepared.md> -o <output.pdf> \
       --pdf-engine=xelatex \
       -V mainfont="<thai-font>" \
       -V lang=th \
       --toc --toc-depth=3
     ```
4. **Save output**
   - Path: `projects/<name>/_export/<doc>-<YYYYMMDD>.<ext>`
   - Create `_export/` if missing
5. **Cleanup**
   - Keep `_export-build/` for re-use (git-ignore it); delete `prepared.md`
6. **Report**
   - Output path, size, diagram count rendered, warnings (skipped PlantUML etc.)

## `project-config.md` export block schema (add to existing config)

```markdown
## Export
- **Reference DOCX:** `../_assets/acme-brand-reference.docx`  (optional; path relative to vault/)
- **Thai font:** `TH Sarabun New` (for PDF) — or `Noto Sans Thai`, `Sarabun`
- **PDF engine:** `xelatex`
- **Pandoc extra args:** `--number-sections`
```

If the block is missing, use built-in defaults (plain pandoc reference docx + `TH Sarabun New`).

## Output file layout

```
projects/<name>/
├── 20-srs/
│   ├── srs.md                       ← source (edited here)
│   └── _export-build/               ← git-ignore; cached diagram PNGs
│       ├── diagram-01.png
│       └── diagram-02.png
└── _export/                         ← deliverables for client
    ├── srs-20260413.docx
    └── srs-review-20260413.docx
```

Suggested `.gitignore` for vault repo:
```
_export-build/
_export/
```
(or commit `_export/` if you want versioned deliverables — your call)

## Rules

- **Never mutate the source md.** Work on a copy in `_export-build/`.
- **Preserve heading levels** — don't auto-promote or collapse.
- **Fail loudly** if a diagram fails to render — don't silently ship a broken doc.
- **Thai PDF:** XeLaTeX is mandatory; pdflatex will produce boxes for Thai glyphs.
- **Reference DOCX:** create once with the client's style (headers, colors, font) using `pandoc -o reference.docx --print-default-data-file reference.docx` as starting point, then customize in Word and save to `vault/_assets/`.

## Report format

```
Exported: projects/sample-loyalty/_export/srs-20260413.docx (1.8 MB)
Source:   projects/sample-loyalty/20-srs/srs.md
Diagrams: 3 Mermaid rendered, 0 PlantUML skipped
Warnings: none
```

## Common errors & fixes

| Error | Likely cause | Fix |
|---|---|---|
| `mmdc: command not found` | mermaid-cli not installed | `npm i -g @mermaid-js/mermaid-cli` |
| `pandoc: xelatex not found` (PDF) | no TeX engine | install MiKTeX (Win) / MacTeX (Mac) / TeX Live (Linux) |
| Thai text appears as boxes in PDF | font missing or wrong engine | install TH Sarabun New + use `--pdf-engine=xelatex` |
| DOCX styling wrong | no / bad reference-docx | create a proper `reference.docx` with client's styles |
| Diagram overflows page | image too large | add `-w 1200` to mmdc, or scale in reference-docx Normal image style |

## Anti-patterns

- **Don't commit `_export-build/`** to Obsidian vault — bloats history
- **Don't hand-edit the DOCX and sync back** — source is the .md; re-export when content changes
- **Don't inline base64 images** in md for diagrams — breaks Obsidian preview; use file refs

## Obsidian syntax handling during export

The source md uses [[obsidian-markdown]] conventions (see [skills/obsidian-markdown/SKILL.md](../obsidian-markdown/SKILL.md)). Before pandoc, in the `_export-build/` copy:
- **Wikilinks** `[[Note]]` and `[[Note|Alias]]` → flatten to plain text (the alias, or the note name). Pandoc doesn't understand wikilinks.
- **Embeds** `![[image.png]]` / `![[image.png|600]]` → rewrite to `![](image.png){width=600px}`
- **Note embeds** `![[Note#Section]]` → inline the referenced section's content (or at minimum, plain-text the reference)
- **Callouts** `> [!note] Title\n> body` → pandoc markdown blockquote with bolded title on first line
- **Highlights** `==text==` → `**text**` (bold) for DOCX, since ==..== is not standard
- **Hidden comments** `%%...%%` → strip entirely
- **Inline tags** `#tag` at line start → leave as-is (pandoc renders literal); mid-sentence tags → strip

Frontmatter custom keys (`tags`, `review-of`, etc.) are ignored by pandoc's default metadata handling, which is fine — they're vault-side only.
