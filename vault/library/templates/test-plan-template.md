---
type: test-plan
standard: IEEE 829 (pragmatic) + ISTQB
language: th
tags:
  - test-plan
  - draft
aliases:
  - Test Plan
---

# แผนการทดสอบ (Test Plan)

**โครงการ:** [ชื่อโครงการ]
**เวอร์ชัน:** 0.1
**วันที่:** [YYYY-MM-DD]
**ผู้จัดทำ:** [QA Lead]

---

## 1. บทนำ
- **Scope การทดสอบ:** [ครอบคลุมอะไร / ไม่ครอบคลุมอะไร]
- **เอกสารอ้างอิง:** [[srs]], [[sds]], SOW Acceptance criteria ([[extracted#9. เงื่อนไขการรับมอบและชำระเงิน]])

## 2. กลยุทธ์การทดสอบ (Test Strategy)

### 2.1 ระดับการทดสอบ
| ระดับ | เจ้าของ | เครื่องมือ | สภาพแวดล้อม |
|---|---|---|---|
| Unit | Dev | [TBD: Jest / JUnit / ...] | local/CI |
| Integration | Dev + QA | [TBD] | CI + dev env |
| System / E2E | QA | [TBD: Playwright / Cypress / Detox] | staging |
| Performance | QA + Dev | [TBD: k6 / JMeter] | perf env (prod-like) |
| Security | Vendor / 3rd party | [TBD: OWASP ZAP + manual pentest] | staging |
| UAT | Client | manual / scripted | UAT env |

### 2.2 ประเภทการทดสอบ
- [x] Functional
- [x] Integration (external: POS, Magento, ...)
- [x] Performance / load
- [x] Security / PDPA compliance
- [ ] Accessibility [TBD: ถ้าอยู่ใน scope]
- [x] Regression
- [x] UAT

## 3. Test Environment

| Env | Purpose | Data | Access |
|---|---|---|---|
| dev | integration | seeded | vendor |
| staging | pre-UAT, perf | prod-like masked | vendor + client QA |
| UAT | client acceptance | UAT dataset | client + vendor on-call |
| prod | live | real | restricted |

## 4. Test Coverage Matrix

> ทุก FR ใน SRS ต้องมี test case ≥ 1 รายการ; NFR มี test approach เฉพาะ

| SRS Ref | Test case(s) | Level | Priority |
|---|---|---|---|
| FR-001 | TC-AUTH-001..005 | Integration + E2E | M |
| FR-002 | TC-POINTS-001..010 | Integration + E2E | M |
| NFR Performance | LT-001 | Load | M |
| NFR Security | ST-001 | Security scan | M |

## 5. Entry / Exit Criteria

### Entry (เริ่มทดสอบได้เมื่อ)
- SRS v<ver> signed off
- Build deployed to target env
- Test data available
- Known defects from previous cycle triaged

### Exit (ปิด cycle ได้เมื่อ)
- All Must-priority test cases executed
- Critical defects: 0 open
- High defects: ≤ [N] open with workaround
- Medium/Low: agreed with PO
- Load test: target TPS sustained [duration]
- Security scan: 0 critical/high

## 6. Test Data Management
- PII masking rules
- Data refresh cadence
- Data cleanup

## 7. Defect Management
- Tool: [TBD: Jira / GitLab Issues]
- Severity: Critical / High / Medium / Low
- Priority: Must-fix-before-release / Should-fix / Could-fix
- SLA: response + fix per severity

## 8. UAT Plan

- **Participants:** [client reps]
- **Duration:** [N weeks]
- **Scenarios:** business flow-based (not FR-by-FR)
- **Sign-off criteria:** [SOW Acceptance §]

## 9. Performance Test Plan

| Scenario | Load profile | Target | Pass criteria |
|---|---|---|---|
| Points accrual at peak | 50 tps sustained 30 min | P95 < 1s | < 0.1% error |
| Member list browse | 1000 concurrent users | P95 < 800ms | < 0.5% error |

## 10. Schedule

| Phase | Start | End | Milestone |
|---|---|---|---|
| Test design | | | test cases reviewed |
| Unit + integration | | | |
| System test | | | |
| UAT | | | UAT sign-off |
| Performance | | | perf report |
| Security | | | scan report |

## 11. Risks & Mitigations

| # | Risk | Impact | Mitigation |
|---|---|---|---|
| | test env unstable | delay | backup env on dev-like infra |

## 12. Revision History
| Version | Date | Author | Changes |
|---|---|---|---|
