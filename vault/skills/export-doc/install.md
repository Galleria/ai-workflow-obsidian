# Install prerequisites for `export-doc` skill

Run once per machine.

## 1. Pandoc (required)

### Windows
```powershell
winget install --id JohnMacFarlane.Pandoc
```
or download installer: https://pandoc.org/installing.html

**Note:** winget installs pandoc to `%LOCALAPPDATA%\Pandoc\` (e.g. `C:\Users\<you>\AppData\Local\Pandoc`). It should be on PATH after the install completes, but **existing shells won't see it** until restart — close and reopen your terminal / Git Bash / VS Code, or in the current session run:
```bash
export PATH="$LOCALAPPDATA/Pandoc:$PATH"   # Git Bash / WSL
# or PowerShell:
$env:PATH = "$env:LOCALAPPDATA\Pandoc;$env:PATH"
```

### macOS
```bash
brew install pandoc
```

### Linux
```bash
sudo apt install pandoc   # Debian/Ubuntu
sudo dnf install pandoc   # Fedora
```

Verify:
```bash
pandoc --version
```
Need ≥ 3.0.

---

## 2. mermaid-cli (required for Mermaid diagrams)

Needs Node.js (you already have it — `node -v`).

```bash
npm install -g @mermaid-js/mermaid-cli
```

Verify:
```bash
mmdc --version
```

Note: `mmdc` launches a headless Chromium on first run. On Windows/WSL it may need extra Chromium flags — see https://github.com/mermaid-js/mermaid-cli#known-issues.

---

## 3. Thai font (required for readable output)

Install **TH Sarabun New** (standard Thai government font) or **Noto Sans Thai**.

### Windows
Download TH Sarabun New from https://www.f0nt.com/release/th-sarabun-new/ — extract, right-click `.ttf` → Install.

Noto alternative:
```powershell
winget install --id Google.Noto   # or install specific Thai fonts from Google Fonts
```

### macOS
```bash
brew install --cask font-sarabun
# or
brew install --cask font-noto-sans-thai
```

### Linux
```bash
sudo apt install fonts-thai-tlwg fonts-noto
```

---

## 4. TeX engine (required ONLY for PDF export)

Skip if you only export to DOCX.

### Windows
Install MiKTeX: https://miktex.org/download
On first PDF export it will auto-install missing packages.

### macOS
```bash
brew install --cask mactex-no-gui   # smaller; ~3 GB
```

### Linux
```bash
sudo apt install texlive-xetex texlive-lang-other
```

Verify:
```bash
xelatex --version
```

---

## 5. PlantUML (optional)

Only needed if your diagrams use ```plantuml fenced blocks.

Needs Java (`java -version` to check).

### All platforms
Download `plantuml.jar` from https://plantuml.com/download and place somewhere on PATH, then create a wrapper:

`plantuml` (shell script on Linux/Mac or .cmd on Windows):
```bash
#!/bin/bash
java -jar /path/to/plantuml.jar "$@"
```

Or via package manager:
- macOS: `brew install plantuml`
- Linux: `sudo apt install plantuml`

---

## 6. Build a client reference DOCX (optional, one-time per client)

The reference DOCX controls fonts, colors, heading styles, margins in the exported Word file.

1. Generate a starting template:
   ```bash
   pandoc -o reference.docx --print-default-data-file reference.docx
   ```
2. Open `reference.docx` in Word → edit styles (Normal, Heading 1-3, Title, ...)
   - Set Thai font: TH Sarabun New 14pt for Normal; Heading 1 18pt bold, etc.
   - Apply client brand colors via Styles
3. Save as `vault/_assets/<client>-brand-reference.docx`
4. Reference in `project-config.md`:
   ```
   ## Export
   - Reference DOCX: `../_assets/<client>-brand-reference.docx`
   ```

---

## Quick smoke test

```bash
cd vault/projects/sample-loyalty
# DOCX
pandoc document/srs.md -o _export/srs-test.docx --toc
# PDF (needs xelatex)
pandoc document/srs.md -o _export/srs-test.pdf --pdf-engine=xelatex -V mainfont="TH Sarabun New" -V lang=th --toc
```

If both produce output, skill is ready. Note: without the pre-render step, Mermaid/PlantUML blocks appear as code in the DOCX/PDF — the skill handles the pre-render automatically; raw pandoc calls don't.
