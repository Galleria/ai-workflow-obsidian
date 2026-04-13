# extract-sow — prerequisites

`extract-sow` reads source SOW files and produces normalized `extracted.md`. For PDF sources Claude's built-in Read tool works directly. For **DOCX / PPTX / XLSX / HTML / EPub** sources, install **markitdown** to convert them to markdown first.

## markitdown (Microsoft, MIT)

GitHub: https://github.com/microsoft/markitdown — lightweight Python utility that converts many formats → markdown suitable for LLM analysis.

### Install

Requires **Python 3.10+**.

```bash
# All supported formats (recommended for this POC)
pip install 'markitdown[all]'

# Or minimal (add extras as needed)
pip install markitdown                    # base
pip install 'markitdown[docx,pptx,xlsx]'  # office only
pip install 'markitdown[pdf]'             # PDF only
```

Verify:

```bash
markitdown --version
markitdown sample.docx -o sample.md
```

### Supported formats

PDF, DOCX, PPTX, XLSX, HTML, CSV, JSON, XML, ZIP, EPub, images (with optional OCR plugin), audio (with transcription), YouTube URLs.

### Known caveats

- **Thai PDF**: not explicitly tested by upstream — verify on a real client PDF before committing. Fallback: use Claude's Read tool directly (handles Thai PDF).
- **OCR** for scanned images requires the `markitdown-ocr` plugin + an LLM client (OpenAI-compatible endpoint).
- **High-fidelity formatting** (tables with merged cells, complex layouts) may not round-trip cleanly — markitdown is optimized for LLM consumption, not human-readable reproduction.

### Quirks observed in smoke tests (markitdown 0.1.5, 2026-04-13)

Tested with synthetic Thai SOW (DOCX + PPTX + XLSX). Thai text preservation: 100% across all three formats. Structural quirks to handle downstream:

**DOCX**
- **Level-0 heading (document title)** → emitted as plain text, NOT `#`. If the Word doc uses the "Title" style for the document name, `extract-sow` must infer the title from context (first paragraph, filename, or frontmatter) rather than expecting a `#` line.
- **Level-1 heading** → emitted as `#` (not `##`). Heading nesting is shifted up by one. `extract-sow` should treat `#` as §1-level, not as document title.
- **Empty table header row** → Word tables without a styled header produce a leading empty row `|  |  |  |` before the real first row. Detect + strip when parsing.
- **Inline text attributes** (bold, highlight, colors) — preserved as markdown bold/plain; styling beyond bold/italic is lost.

**PPTX**
- **Slide numbers** emitted as `<!-- Slide number: N -->` HTML comments — useful for LLM context; preserve when quoting excerpts.
- **Bullet lists** flatten to consecutive paragraphs (bullet structure lost). If the deck relies on nested bullets to convey scope/non-scope, `extract-sow` must not assume bullet-list semantics.
- **Titles** → `#` per slide (fine).

**XLSX**
- **Sheet name** → `## <name>` heading (good).
- **Each sheet** becomes its own section in order — single markdown file with `##` per sheet. If the workbook has 10+ sheets, output can be long; consider `-s <sheet>` flag per sheet if supported (or filter by `awk` before feeding to `extract-sow`).

**Cross-format**
- **Non-ASCII punctuation** (`≥`, `<`, `→`, ฯลฯ) preserved correctly — no escaping needed.
- **YouTube transcripts** fail with `no element found` on videos with disabled captions/region restrictions — not a markitdown bug, a YouTube API limit. Fall back to manual transcript.

## When extract-sow uses markitdown

The skill now detects source file type:

| Source format | Path |
|---|---|
| `.md` / `.txt` | Read directly |
| `.pdf` | Prefer Claude's Read tool (supports Thai); fall back to `markitdown file.pdf -o file.md` if Read fails or diff-stable output needed |
| `.docx` / `.pptx` / `.xlsx` / `.html` / `.epub` | `markitdown file.ext -o file.md` → then Read the output |
| scanned image / audio | Requires markitdown extras; warn user before attempting |

Output convention: save the converted markdown to `projects/<name>/00-sow/_converted/<filename>.md`. Git-ignore `_converted/` (regenerable from source).

## Troubleshooting

| Issue | Fix |
|---|---|
| `markitdown: command not found` | `pip install 'markitdown[all]'` inside the active Python env |
| Python < 3.10 | upgrade Python; markitdown requires 3.10+ |
| Garbled Thai in output | fall back to Read tool for that file; report the markitdown miss |
| PPTX slide order wrong | known limitation; re-check numbering manually |
