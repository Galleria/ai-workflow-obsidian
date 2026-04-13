# How to build a branded reference DOCX for a client

Pandoc uses a "reference.docx" as a **style template** when converting Markdown → DOCX. Every style (Normal, Heading 1-3, Title, Table, Quote, etc.) in the reference DOCX becomes the style of the matching element in the output. Result: client-branded Word file with one-time setup.

**Time:** 20-30 min per client (one-time).

---

## Step 1 — Generate the starting template

Requires `pandoc` installed ([install guide](../skills/export-doc/install.md)).

```bash
cd vault/_assets/
pandoc -o <client>-brand-reference.docx --print-default-data-file reference.docx
```

This drops pandoc's default template at `_assets/<client>-brand-reference.docx`.

---

## Step 2 — Open in Word → edit styles

1. Open `<client>-brand-reference.docx` in Microsoft Word (or LibreOffice Writer — styles are named the same).
2. Show the Styles pane: **Home → Styles → Styles Pane** (Ctrl+Alt+Shift+S in Word).
3. You'll see a list. Edit these at minimum:

| Style | Typical tweaks for Thai doc |
|---|---|
| **Normal** | Font: TH Sarabun New 14pt; line spacing 1.15; justification left |
| **Title** | Font: TH Sarabun New 28pt bold; color = client primary brand color; center |
| **Heading 1** | 20pt bold; color = client primary; spacing-before 18pt |
| **Heading 2** | 18pt bold; color = client secondary; spacing-before 12pt |
| **Heading 3** | 16pt bold; color = dark gray |
| **Table** (or "Table Grid") | header row background = brand color lighten 20%; 12pt font |
| **Quote** | italic, left border 2pt brand color |
| **Code** (inline + block) | Consolas 10pt; light gray background |
| **Footer** | 10pt with "© <Client> <Year>" |
| **Header** | 10pt with client logo (optional) |

**To edit a style:** right-click the style in the pane → **Modify** → change font/size/color → OK.

**Brand colors:** Get HEX codes from the client brand guideline. In Word, Font Color → More Colors → Custom → enter HEX.

---

## Step 3 — (Optional) Add cover page + header/footer

- **Insert → Cover Page** → pick a layout → customize with client logo + project name placeholder.
- **Insert → Header** → add client logo (`_assets/<client>-logo.png`) + horizontal line.
- **Insert → Footer** → page number (right-aligned) + "Confidential" or doc version (left-aligned).

**Important:** delete all actual content text — leave only the *styles* + header/footer. Pandoc generates the content; this file only contributes styling.

---

## Step 4 — Verify Thai rendering

Type a quick Thai paragraph in each heading level to confirm the font renders correctly (no boxes, no Latin fallback). Common fix: if Thai looks wrong, the font may be set as "Latin only" — go to Modify style → Format → Font → ensure "Complex scripts" font is also TH Sarabun New.

Delete the test paragraphs before saving.

---

## Step 5 — Save and reference

1. **File → Save** (keep .docx format).
2. In the project that should use it, edit `project-config.md`:
   ```markdown
   ## Export
   - Reference DOCX: `../_assets/<client>-brand-reference.docx`
   ```

---

## Step 6 — Test the export

```bash
cd vault/projects/<client-project>/
pandoc document/srs.md -o _export/srs-test.docx \
  --reference-doc=../../_assets/<client>-brand-reference.docx \
  --toc
```

Open the resulting DOCX in Word. Every heading, table, and paragraph should now use the client's styles.

*(The `export-doc` skill does this automatically — including Mermaid pre-rendering.)*

---

## Tips

- **Iterate:** export a real doc → see what looks wrong → tweak reference.docx → re-export. 2-3 rounds usually converges.
- **One per client, not per project:** reuse across all that client's projects.
- **Version it:** commit the `.docx` to git. Word format is binary but small (~30KB) — worth versioning so teammates get the same branding.
- **Don't over-engineer:** match the client's standard internal doc look, not a design portfolio. Word users expect simple, scannable styling.
