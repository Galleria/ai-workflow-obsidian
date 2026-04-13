---
name: make-diagram
description: Generate a diagram (workflow/BPMN, sequence, use-case, ERD, component, class, Gantt) in Mermaid, PlantUML, or draw.io format. Trigger when the user asks to "diagram", "draw", "visualize", "flowchart", "sequence", "ERD", "use-case", "วาด/สร้าง diagram/flow", etc.
---

# make-diagram

Produces a single diagram file in the tool chosen by `project-config.md` (or user-specified override).

## Inputs
- **Project name** (optional; defaults to config lookup)
- **Diagram type** — one of: `workflow`, `sequence`, `use-case`, `erd`, `component`, `class`, `gantt`, `state`
- **Source** — extracted requirements, a functional requirement ID, or free-form description from the user
- **Tool override** (optional) — `mermaid` | `plantuml` | `drawio`

## Tool selection (default rules)

| Type | Default tool | Fallback | Why |
|---|---|---|---|
| workflow | mermaid flowchart | drawio BPMN (formal client deliverable) | renders in Obsidian natively |
| sequence | mermaid sequenceDiagram | plantuml | git-friendly |
| use-case | plantuml | drawio | Mermaid's use-case support is weak |
| erd | mermaid erDiagram | plantuml | lightweight, Obsidian preview works |
| component | plantuml | drawio | best syntax for arch diagrams |
| class | mermaid classDiagram | plantuml | readable |
| gantt | mermaid gantt | — | PM view in vault |
| state | mermaid stateDiagram-v2 | plantuml | simple states |

Override via `project-config.md` → Diagram tool table, or explicit user instruction.

## Process

1. Read `project-config.md` for tool preference (if project given)
2. Pick tool via table above + overrides
3. Generate the diagram code
4. Save to `projects/<project>/diagram/<type>-<slug>.md` with the fenced code block, OR return inline if user didn't name a project
5. If tool = `drawio`, save as `.drawio` XML instead and note that it needs the draw.io Obsidian plugin to render

## Output file format (Mermaid example)

```markdown
---
type: sequence
source: FR-012 (Login flow)
tool: mermaid
---

# Login sequence

\`\`\`mermaid
sequenceDiagram
  actor U as User
  participant W as Web
  participant A as AuthService
  participant D as DB
  U->>W: POST /login
  W->>A: verify(credentials)
  A->>D: SELECT user
  D-->>A: user row
  A-->>W: JWT
  W-->>U: 200 + cookie
\`\`\`
```

## Rules
- **One diagram per file** — keep diagrams atomic for reuse
- **Always include a frontmatter block** naming `type`, `source`, `tool`
- **Label actors/components using client domain terms**, not generic "User"/"System" (unless client SOW is that abstract)
- **Don't exceed ~15 nodes** — if the concept is bigger, split into multiple diagrams (layered: context → container → component)
- **For BPMN via drawio:** only use when client explicitly wants BPMN notation; otherwise Mermaid flowchart is enough
- **Check pattern library** (`patterns/`) for common flows (auth, payment, CRUD) before drawing from scratch

## Output conventions (Obsidian)

Follow [[obsidian-markdown]] conventions (see [skills/obsidian-markdown/SKILL.md](../obsidian-markdown/SKILL.md)):
- Frontmatter: add `tags: [<project-slug>, diagram, <type>]` alongside existing `type`, `source`, `tool`
- If `source` references a requirement (e.g. FR-012), link back with `source: "[[srs#FR-012]]"` so Obsidian renders a backlink
- For Mermaid flowcharts linking to notes, use `class NodeName internal-link;` to make nodes clickable in reading view
