---
type: brd
kind: template
standard: BABOK v3 (pragmatic)
domain: requirements
language: th
tags:
  - library
  - template
  - requirements
  - brd
  - draft
aliases:
  - Business Requirements Document
---

# เอกสารข้อกำหนดทางธุรกิจ (Business Requirements Document)

**โครงการ:** [ชื่อโครงการ]
**ลูกค้า:** [ชื่อลูกค้า]
**เวอร์ชัน:** 0.1
**วันที่:** [YYYY-MM-DD]

---

## 1. บทสรุปผู้บริหาร (Executive Summary)
[สรุป 1 ย่อหน้า: ปัญหาธุรกิจ, solution ที่เสนอ, ผลลัพธ์ที่คาดหวัง, งบประมาณและระยะเวลา]

## 2. บริบทและภูมิหลัง (Business Context)

### 2.1 สถานการณ์ปัจจุบัน (As-is)
[ระบบ/กระบวนการปัจจุบัน, pain points, ข้อจำกัด]

> Diagram: As-is workflow
> ```mermaid
> flowchart LR
>   A[Current step 1] --> B[Current step 2]
> ```

### 2.2 สถานการณ์เป้าหมาย (To-be)
[ระบบ/กระบวนการที่ต้องการ, benefits, differentiators]

> Diagram: To-be workflow
> ```mermaid
> flowchart LR
>   A[New step 1] --> B[New step 2]
> ```

## 3. วัตถุประสงค์ทางธุรกิจ (Business Objectives)

| # | Objective | KPI / Success metric | Baseline | Target |
|---|---|---|---|---|
| BO-1 | [TBD] | [TBD] | [TBD] | [TBD] |

## 4. ขอบเขตทางธุรกิจ (Business Scope)

### In scope
- ...

### Out of scope
- ...

## 5. ผู้มีส่วนได้เสีย (Stakeholder Analysis)

| บทบาท | ชื่อ | Influence (H/M/L) | Interest (H/M/L) | Engagement strategy |
|---|---|---|---|---|
| [TBD] | [TBD] | | | |

## 6. Business Requirements (high-level)

> BR-xxx ให้เน้น "WHAT the business needs" ไม่ใช่ "HOW" (รายละเอียดไปอยู่ใน SRS)

| # | Requirement | Priority (M/S/C) | Owner | Rationale |
|---|---|---|---|---|
| BR-001 | [TBD] | M | | |

## 7. Business Rules

| # | Rule | Applies to | Source |
|---|---|---|---|
| RULE-1 | [TBD] | | |

## 8. สมมติฐานและข้อจำกัด (Assumptions & Constraints)

### Assumptions
- [TBD]

### Constraints
- [TBD]

## 9. ผลประโยชน์ที่คาดหวัง (Benefits)

### Tangible
- [เช่น ลด customer churn 10%, เพิ่ม GMV 15%]

### Intangible
- [เช่น brand perception, staff satisfaction]

## 10. ความเสี่ยง (Risks)

| # | Risk | Impact | Likelihood | Mitigation |
|---|---|---|---|---|

## 11. Governance & Decision authority

[ใครตัดสินใจเรื่องอะไร — Steering Committee scope, Product Owner scope, Escalation path]

## 12. Revision History

| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | | | Initial draft |
