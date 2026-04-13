---
name: draft-doc
description: Draft a project deliverable document (SRS, BRD, SDS, test plan, WBS, etc.) by filling the matching template with the project's extracted requirements. Trigger when the user asks to "draft", "write", or "create" a named document for a specific project, e.g. "draft SRS for acme-crm", "เขียน SRS ให้โปรเจค X".
---

# draft-doc

One skill, all document types. Template + config drive the output — not hard-coded per-doc logic.

## Inputs (from user request)
- **Project name** — matches a folder under `projects/`
- **Document type** — one of: `brd`, `srs`, `sds`, `test-plan`, `wbs`, or any other type with a template in `templates/`
- **Source** (optional) — path to extracted requirements; defaults to `projects/<name>/00-sow/extracted.md`

## Process

1. **Load context**
   - Read `projects/<project>/project-config.md` → language, standard level, diagram tool preferences
   - Read `templates/<type>-template.md` → section skeleton
   - Read source requirements (SOW extract or user-provided)
   - If `standard level = strict` and a matching note exists in `standards/`, load it (e.g. `standards/ieee-830-srs.md` for SRS)

2. **Fill the template**
   - Walk sections in template order
   - For each section:
     - Info present in requirements → write content
     - Missing but reasonably inferable → draft + mark `[ต้องยืนยัน: <what]` (Thai) or `[TBD-confirm: <what>]` (English)
     - Missing and not inferable → leave placeholder `[TBD: <what's needed>]`
   - Respect `language` from config (default Thai)
   - If section calls for a diagram, emit a `make-diagram` request inline or include a stub fenced block using the tool from config

3. **Save**
   - Path: `projects/<project>/<NN>-<type>/<type>.md` (use folder convention from project-config)
   - If file exists, create numbered version `<type>-v2.md` rather than overwrite

4. **Report**
   - Count of sections filled vs. TBD
   - List open TBDs + inferred items needing confirmation
   - Suggest invoking `qa-review` next

## Output format
```
Drafted: projects/<project>/20-srs/srs.md
Sections: 14 filled, 3 inferred, 2 TBD
TBDs:
  - Section 3.2 Performance: no NFR data in SOW
  - Section 5.1 Deployment: environment not specified
Inferred (please confirm):
  - User roles = [admin, staff, customer] — guessed from use-case list
Next: run qa-review on this doc
```

## Rules
- **Never invent requirements.** If the SOW doesn't say it, mark TBD.
- **Preserve template section order and numbering** — standards tools expect it.
- **Language consistency:** all sections in the same language unless config says `th+en`.
- **Diagrams:** don't inline huge ASCII — emit Mermaid/PlantUML code fence or a reference to `projects/<name>/40-diagrams/<name>.md`.
