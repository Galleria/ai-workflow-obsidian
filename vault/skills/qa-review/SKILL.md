---
name: qa-review
description: Review a drafted document (SRS/BRD/SDS/etc.) or SOW extract, surface gaps and risks, generate questions to ask the client, and propose the next plan. Trigger when the user asks to "review", "ตรวจ", "ask questions about", "what's missing in", "next plan for", or "QA" a project document.
---

# qa-review

The closing step of the workflow. Takes a drafted doc (or SOW extract) and produces:
1. A question list for the client
2. A risk register
3. A proposed next-plan (what to do after client answers)

## Inputs
- **Project name**
- **Target doc** — path or type (`srs`, `brd`, `sds`, `extracted`, ...)
- **Mode** (optional) — `client` (external questions) | `internal` (team review) | `both` (default)

## Process

1. Read `project-config.md` for language + standard level
2. Read the target doc
3. If target = a drafted deliverable, also read `00-sow/extracted.md` for source traceability
4. Walk the doc and classify each issue:
   - **TBD** — explicit placeholder → becomes client question
   - **Inferred** — filled by assistant, needs confirmation → client question with current guess
   - **Ambiguous** — wording allows >1 interpretation → clarification question
   - **Conflicting** — contradicts another section or SOW → internal red flag
   - **Missing standard section** — required by standard but absent → internal flag (if strict mode)
   - **Risk** — unquantified NFR, aggressive timeline, unclear acceptance → risk register entry
5. Save output to `projects/<project>/99-qa/<doc>-review-<YYYY-MM-DD>.md`
6. Report summary

## Output structure

```markdown
---
review-of: projects/<project>/20-srs/srs.md
reviewed-at: YYYY-MM-DD
mode: both
---

# QA Review — <doc title>

## 1. คำถามสำหรับลูกค้า (Client questions)
> ส่งให้ลูกค้ายืนยันก่อน finalize เอกสาร

### Blockers (ต้องตอบก่อนไปขั้นถัดไป)
- [ ] Q1: ...  (ref: §X.Y, tag: TBD)
- [ ] Q2: ...

### Confirmations (ของที่เดาไว้ — ขอยืนยัน)
- [ ] Q3: เราเดาว่า <X> — ถูกต้องหรือไม่?  (ref: §X.Y, tag: Inferred)

### Nice-to-have (ไม่ blocking แต่ควรรู้)
- [ ] Q4: ...

## 2. Risk register
| # | ความเสี่ยง | ผลกระทบ (H/M/L) | โอกาสเกิด (H/M/L) | Mitigation เบื้องต้น |
|---|---|---|---|---|
| R1 | ... | H | M | ... |

## 3. Internal findings (ไม่ส่งลูกค้า)
- Conflicts: ...
- Missing standard sections (strict mode): ...
- Consistency checks: ...

## 4. Next plan
**ก่อนได้คำตอบลูกค้า:**
- [ ] ...

**หลังได้คำตอบลูกค้า:**
- [ ] แก้ TBD ที่ยืนยันแล้วใน SRS
- [ ] รันทำ make-diagram สำหรับ flows ที่ยังไม่มี
- [ ] รันทำ draft-doc สำหรับ <next deliverable>

## 5. Traceability
> requirement → SOW excerpt
- FR-001 ← §11 excerpt "..."
- FR-002 ← [TBD: not in SOW]
```

## Rules
- **Always separate client-facing vs internal findings** — internal issues never leak to client-facing questions unless sanitized
- **Group client questions by urgency** (blockers / confirmations / nice-to-have) — PM needs to know what unblocks the next step
- **Write questions in the user's language setting** (default Thai) — the PM forwards them directly to the client
- **Every question cites its source** — section reference so reviewer can jump back
- **Risk register is pragmatic, not PMP-textbook** — 5 rows max unless the project is unusually risky
