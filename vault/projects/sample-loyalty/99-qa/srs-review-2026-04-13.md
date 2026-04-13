---
review-of: projects/sample-loyalty/20-srs/srs.md
reviewed-at: 2026-04-13
mode: both
---

# QA Review — SRS v0.1 (sample-loyalty)

## 1. คำถามสำหรับลูกค้า (Client questions)

### Blockers (ต้องตอบก่อนไปขั้นถัดไป — กระทบ architecture หรือ scope)
- [ ] **Q1 — Tier logic:** เกณฑ์ upgrade/downgrade Silver/Gold/Platinum ใช้ metric ใด (ยอดซื้อสะสม / คะแนนสะสม / visit count)? threshold เท่าไร? หน้าต่างเวลา (rolling 12 months?)? มี grace period ก่อน downgrade หรือไม่? (ref: SRS §3.2 FR-004; SOW §3 In scope)
- [ ] **Q2 — Identity merge:** ACME มี customer ใน Magento อยู่แล้ว + ลูกค้า POS ไม่มี digital ID — strategy การ merge identity ข้าม channel คืออะไร (match โดย phone+email? ให้ลูกค้า confirm ด้วย OTP?)  (ref: §3.2 FR-008; SOW §2 Unified identity)
- [ ] **Q3 — Mobile performance quantitative target:** SOW ใช้คำว่า "responsive and fast" — ขอยืนยัน target ที่เรา propose: cold-start ≤ 3s, screen transition ≤ 500ms, API P95 ≤ 800ms  (ref: §3.3 NFR; SOW §6)
- [ ] **Q4 — Member-facing availability SLA:** SOW บอก 24/7 แต่ไม่ระบุ % — เสนอ 99.5% (ดาวน์ได้ประมาณ 3.6 ชม./เดือน) รับได้ไหม?  (ref: §3.3; SOW §6)
- [ ] **Q5 — Push provider:** ใช้ FCM+APNS โดยตรง หรือผ่าน 3rd-party (OneSignal / Braze)? กระทบ cost, compliance, features  (ref: §3.1.3, §3.2 FR-012)

### Confirmations (ของที่เดาไว้ — ขอยืนยัน)
- [ ] **Q6 — Brand / design system:** สมมติว่า ACME มี brand guideline พร้อมส่งมอบ — ถูกต้องไหม? ถ้ายัง, timeline และใครเป็นเจ้าของ?  (ref: §2.5)
- [ ] **Q7 — App Store / Play Store accounts:** สมมติ ACME มี developer account ทั้ง 2 — ถูกต้องไหม?  (ref: §2.5)
- [ ] **Q8 — Staff portal:** 360° view จะ embed ใน staff portal ที่มีอยู่ หรือเป็น standalone app?  (ref: §3.1.1, §3.2 FR-009)
- [ ] **Q9 — Leadership dashboard refresh:** เสนอ daily batch (cost-effective) — รับได้ไหม หรือจำเป็นต้อง realtime?  (ref: §3.2 FR-010)
- [ ] **Q10 — Rule configuration UI:** เบื้องต้นเสนอ form-based UI (ไม่มี DSL ซับซ้อน) — rule ของ ACME ซับซ้อนระดับไหน?  (ref: §3.2 FR-006)
- [ ] **Q11 — Legacy stamp → points conversion:** สูตรแปลง stamp balance → initial points (1 stamp = ? points)?  (ref: §3.2 FR-011)
- [ ] **Q12 — GitLab repository ownership:** repo อยู่ใน GitLab ของ ACME หรือ vendor? ส่งมอบ access อย่างไร?  (ref: §3.4)

### Nice-to-have (ไม่ blocking แต่ควรรู้)
- [ ] **Q13 — Notification channels scope:** ทั้ง push + email + SMS + in-app หรือเลือกบางส่วน? ใครรับผิดชอบ SMS cost?  (ref: §3.2 FR-012)
- [ ] **Q14 — Data retention policy (PDPA):** inactive account เก็บนานเท่าไร? right-to-be-forgotten flow เป็นอย่างไร?  (ref: §3.3 Compliance)
- [ ] **Q15 — Growth projection:** คาดว่า member count เกิน 500k ใน 1-2 ปีหรือไม่? ต้อง design headroom แค่ไหน?  (ref: §3.3 Scalability)
- [ ] **Q16 — Pilot-to-full criteria:** ตัววัดที่ใช้ตัดสินใจ rollout จาก pilot 5 สาขา → 42 สาขา?  (ref: SOW §7)
- [ ] **Q17 — Registration mandatory fields:** phone+email บังคับ; ชื่อ-นามสกุล-วันเกิด optional? หรือบังคับด้วย?  (ref: §3.2 FR-001)
- [ ] **Q18 — Usability target:** มี UX benchmark ที่ ACME ใช้วัดไหม (SUS score, Net Promoter)? เราเสนอ SUS ≥ 70 ใน UAT

## 2. Risk register

| # | ความเสี่ยง | Impact | Likelihood | Mitigation เบื้องต้น |
|---|---|---|---|---|
| R1 | API access (POS/Magento) ส่งช้ากว่า 2 สัปดาห์ → slip M1 | H | M | ขอ sandbox access ล่วงหน้าในสัปดาห์แรก; lock assumption ใน SOW Amendment ถ้าเห็นสัญญาณช้า |
| R2 | Tier logic (Q1) ไม่ confirm ทันก่อน M1 → block SDS design ของ tier engine | H | M | นัด workshop กับ marketing ในสัปดาห์ 1-2; prepare 2-3 option patterns |
| R3 | Q4 peak season deadline (2026-10-31) fixed → ลด buffer สำหรับ UAT/load test | H | H | Plan load test + UAT cutover 3 สัปดาห์ก่อน go-live; cut scope phase 2 features เข้ม |
| R4 | PDPA consent flow ยังไม่ชัด (Q14) → risk legal rework ก่อน launch | M | M | Loop in ACME legal ตั้งแต่ SRS sign-off; อ้าง template consent PDPA |
| R5 | Data migration 180k records — data quality ของ legacy stamp-card ไม่ทราบ | M | H | ขอ sample dump ใน 2 สัปดาห์แรก; run profiling + dry-run migration ก่อน |

## 3. Internal findings (ไม่ส่งลูกค้า)

### Conflicts / inconsistencies
- SOW §6 บอก "responsive and fast" แต่ §9 Acceptance ไม่มี perf criteria — **ต้อง negotiate ให้ถ้า load test fail → fix warranty, ไม่ใช่ payment hold**
- SOW §5 stakeholders ไม่ระบุ ตัวแทน Ops/DevOps ฝั่ง ACME — ใครเซ็น runbook / deploy?

### Missing sections (pragmatic mode — acceptable)
- ไม่ได้เขียน §3.6 Other requirements — OK เพราะ pragmatic
- §3.5 Software System Attributes ใส่รวบรัด — พอสำหรับ pragmatic

### Consistency checks — pass
- FR-001..FR-012 traceable ไปยัง `extracted.md §5` — ✓
- NFR quantities trace ไปยัง SOW §6 — ✓
- Terminology สอดคล้อง (ไม่ใช้ทั้ง "สมาชิก" และ "ลูกค้า" สลับกัน) — ✓

## 4. Next plan

### ก่อนได้คำตอบลูกค้า (ภายในสัปดาห์นี้)
- [ ] นัด requirement workshop 1 รอบ (ครอบคลุม Q1, Q2, Q6, Q11, Q17)
- [ ] ขอ sandbox API (POS/Magento) และ legacy DB sample dump
- [ ] Tech lead ร่าง design option 2-3 แบบสำหรับ tier engine (pre-empt Q1)

### หลังได้คำตอบลูกค้า
- [ ] Update SRS → v0.2 (แก้ TBDs ที่ confirmed)
- [ ] รันสร้าง use-case diagram (PlantUML) สำหรับ flows ที่ scope ยังไม่ครบ
- [ ] รัน `draft-doc` สำหรับ SDS (M1 deliverable)
- [ ] เริ่ม draft test plan skeleton + WBS

## 5. Traceability (spot check)

| SRS item | Source |
|---|---|
| FR-001 (registration) | `extracted.md §5 FR-001`; SOW §3 In scope |
| FR-002 (accrual) | `extracted.md §5 FR-002`; SOW §3 + §6 |
| NFR 500k members / 50 tps | SOW §6 raw excerpt §11 |
| Tier Silver/Gold/Platinum | SOW §3 In scope |
| Migration 180k | SOW §3 In scope |
| Warranty 3 months | SOW §4 Deliverable #10 + §6 |

---

## QA report summary
- Blockers (require client answer): **5**
- Confirmations: **7**
- Nice-to-have: **6**
- Risks logged: **5**
- Internal flags: **2** (contract wording on perf + stakeholder gap)
- **Overall verdict:** Draft is structurally complete; 5 Q1-Q5 must be resolved before M1 SDS sign-off. Schedule risk (R3) is the biggest ongoing concern.
