# _assets/

Shared binary / non-markdown resources referenced by the vault.

## What goes here

| Type | Example filename | Purpose |
|---|---|---|
| Client brand reference DOCX | `acme-brand-reference.docx` | pandoc style template for `export-doc` — gives client-branded Word output |
| Client logos | `acme-logo.png` | embedded in diagrams or doc headers |
| Shared images | `architecture-principles.png` | cross-project visuals |
| Font samples | `thsarabun-preview.pdf` | reference only |

## Naming convention

- Brand reference: `<client-slug>-brand-reference.docx`
- Logo: `<client-slug>-logo.<ext>`
- One-off: prefix with date or purpose

## How clients reference these from project-config

```markdown
## Export
- Reference DOCX: `../_assets/acme-brand-reference.docx`
```

Path is relative to the vault root, starting with `../` from inside `projects/<name>/`.

## Building a brand reference DOCX

See [HOW-TO-BRAND-DOCX.md](HOW-TO-BRAND-DOCX.md).
