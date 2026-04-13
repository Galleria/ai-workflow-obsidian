---
name: extract-sow
description: Parse a Statement of Work (SOW) or raw requirement document into structured sections — scope, deliverables, functional/non-functional requirements, stakeholders, timeline, constraints, assumptions. Trigger when the user asks to "extract", "parse", "summarize", or "analyze" a SOW/requirement doc, e.g. "extract the SOW for acme-crm", "วิเคราะห์ SOW ไฟล์นี้".
---

# extract-sow

Turns a raw SOW (PDF/DOCX/MD/plain text) into a normalized `extracted.md` that downstream skills (`draft-doc`, `qa-review`) can consume.

## Inputs
- **Project name** — folder under `projects/`
- **Source file(s)** — defaults to whatever is in `projects/<project>/_input/raw/` or `projects/<project>/_input/` (other than `extracted.md`)

## Process

1. **Load** the source SOW — path-dispatch on extension:
   - `.md` / `.txt` → Read directly
   - `.pdf` → Read tool (handles Thai PDF natively)
   - `.docx` / `.pptx` / `.xlsx` / `.html` / `.epub` → **preprocess with markitdown** (see `install.md`):
     ```bash
     markitdown <path> -o projects/<project>/_input/_converted/<name>.md
     ```
     then Read the converted `.md`. Save originals untouched; `_converted/` is git-ignored.
   - If markitdown is not installed and source is not PDF/MD → stop and direct user to `install.md`.
   - Multiple files → merge in filename order.
2. **Load** `projects/<project>/project-config.md` for language preference.
3. **Extract** into the canonical structure below. Every field either has content or `[TBD: <what's missing>]` — never skip.
4. **Save** to `projects/<project>/_input/extracted.md`.
5. **Report** counts + top gaps + which source files went through markitdown vs Read.

## Canonical output structure (`extracted.md`)

```markdown
---
source: <filename(s)>
project: <name>
extracted-at: <YYYY-MM-DD>
language: <from config>
---

# SOW Extract — <project>

## 1. เมตาดาต้า (Meta)
- ลูกค้า (Client):
- ชื่อโครงการ (Project name):
- ประเภทสัญญา (Contract type): time-and-materials / fixed-price / retainer / [TBD]
- มูลค่าสัญญา (Value): [TBD หรือ ระบุ]
- ระยะเวลา (Duration): [start] → [end]

## 2. วัตถุประสงค์และขอบเขต (Objectives & Scope)
### In scope
- ...
### Out of scope
- ...
### สมมติฐาน (Assumptions)
- ...

## 3. ผู้มีส่วนได้เสีย (Stakeholders)
| บทบาท | ชื่อ / องค์กร | ความรับผิดชอบ |
|---|---|---|

## 4. Deliverables
| # | ชื่อ deliverable | รูปแบบ | เจ้าของ | กำหนดส่ง |
|---|---|---|---|---|

## 5. Functional Requirements (high-level)
- FR-001: ...
- FR-002: ...
> (รายละเอียดแต่ละข้อจะถูกขยายใน SRS ขั้นตอนถัดไป)

## 6. Non-Functional Requirements
| หมวด | ข้อกำหนด (ถ้ามี) |
|---|---|
| Performance | |
| Security | |
| Availability | |
| Scalability | |
| Compliance | |
| Usability | |

## 7. Timeline & Milestones
| Milestone | วันที่ | เงื่อนไขการส่งมอบ |
|---|---|---|

## 8. ข้อจำกัด (Constraints)
- เทคโนโลยี:
- กฎหมาย / compliance:
- งบประมาณ:
- ทรัพยากร:

## 9. เงื่อนไขการรับมอบและชำระเงิน (Acceptance & Payment)
- เกณฑ์การรับมอบงาน (Acceptance criteria):
- การรับประกัน (Warranty):
- งวดชำระ:

## 10. Open questions / Gaps
- [ ] ...
- [ ] ...

## 11. Raw excerpts (สำหรับตรวจสอบ)
> ต้นฉบับย่อหน้าสำคัญที่ใช้อ้างอิง

```

## Rules
- **Quote sparingly** — cite key clauses in §11 for traceability, don't paraphrase everything.
- **Don't invent numbers.** If the SOW says "fast response", write that verbatim and add `[TBD: quantify]` in §6 Performance.
- **Preserve client's terminology** — don't auto-translate domain terms unless obviously ambiguous.
- **Gaps are first-class.** Anything missing goes into §10 so `qa-review` can surface it.

## Output conventions (Obsidian)

Follow [[obsidian-markdown]] conventions (see [skills/obsidian-markdown/SKILL.md](../obsidian-markdown/SKILL.md)):
- Add to frontmatter: `tags: [<project-slug>, sow, extracted]`
- Tag gaps inline (`#tbd`) in §10 so they're discoverable via search/Dataview
- Use `> [!quote]` for §11 raw excerpts (renders distinctly in Obsidian)

## Report format
```
Extracted: projects/<project>/_input/extracted.md
Sections: 11 filled, X contain TBDs
Top gaps (for client clarification):
  - ...
  - ...
Next: run draft-doc to start filling SRS/BRD
```
