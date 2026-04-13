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
