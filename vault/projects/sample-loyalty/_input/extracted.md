---
source: sow-acme-loyalty.md
project: sample-loyalty
extracted-at: 2026-04-13
language: th
---

# SOW Extract — sample-loyalty

## 1. เมตาดาต้า (Meta)
- **ลูกค้า:** ACME Retail Co., Ltd.
- **ชื่อโครงการ:** ACME Retail Loyalty Program Platform
- **ประเภทสัญญา:** Fixed-price
- **มูลค่าสัญญา:** 2,400,000 THB (ไม่รวม VAT)
- **ระยะเวลา:** 2026-05-01 → 2026-10-31 (6 เดือน)
- **Vendor:** 2Smooth Co., Ltd.

## 2. วัตถุประสงค์และขอบเขต (Objectives & Scope)

### Objectives
- รวม customer identity ข้าม POS, e-commerce, mobile app
- Points engine ที่ทีม marketing ปรับ rule ได้เองโดยไม่ต้องแก้โค้ด
- 360° customer view สำหรับพนักงานหน้าร้านและ call center
- Pilot 5 สาขา → rollout ครบ 42 สาขา

### In scope
- Customer registration / KYC lite (phone + email OTP)
- Points engine (earn/redeem rules, tier: Silver/Gold/Platinum)
- Member mobile app (iOS + Android) — บัตรสมาชิก, ประวัติธุรกรรม, coupon wallet, store locator
- Admin web console สำหรับ marketing (แคมเปญ, earn rules, promotions)
- Integration: POS (Oracle Retail XStore, REST webhook), e-commerce (Magento 2, REST API)
- Leadership dashboard (members count, points issued/redeemed, GMV from members)
- Data migration จาก legacy stamp-card (~180,000 records)

### Out of scope
- การเปลี่ยน POS
- Loyalty partnerships กับแบรนด์ภายนอก (phase 2)
- Gamification
- AI personalization (phase 2)

### สมมติฐาน (Assumptions)
- ACME ให้ API access ของ POS และ Magento ภายใน 2 สัปดาห์หลังเซ็นสัญญา
- ACME ส่ง database dump ของ legacy stamp-card
- Brand / design system มาจากทีม marketing ACME
- เนื้อหา/คำแปลภาษาไทยมาจาก ACME

## 3. ผู้มีส่วนได้เสีย (Stakeholders)

| บทบาท | ชื่อ / องค์กร | ความรับผิดชอบ |
|---|---|---|
| Sponsor | Ms. Suwanna Tanakit, CMO, ACME | ตัดสินใจระดับ executive |
| Product Owner | Mr. Kittipong Chai, Head of Loyalty, ACME | กำหนด requirement และ priority |
| IT Counterpart | Mr. David Lee, CTO Office, ACME | Integration, security, infrastructure |
| PM (vendor) | [TBD: ยังไม่ระบุ] | บริหารโครงการฝั่ง vendor |
| Tech Lead (vendor) | [TBD: ยังไม่ระบุ] | สถาปัตยกรรมระบบ |

## 4. Deliverables

| # | ชื่อ deliverable | รูปแบบ | เจ้าของ | กำหนดส่ง |
|---|---|---|---|---|
| 1 | Project management plan & schedule | เอกสาร | PM | หลังเซ็น |
| 2 | SRS | เอกสาร | SA/Tech Lead | M1 (2026-06-15) |
| 3 | SDS + architecture diagram | เอกสาร | Tech Lead | M1 |
| 4 | Source code + CI/CD (GitLab) | Repo | Dev | ต่อเนื่อง |
| 5 | Member mobile app (iOS + Android) | App published | Mobile team | M4/M5 |
| 6 | Admin web console บน AWS ap-southeast-1 | Web app | Web team | M2 |
| 7 | Data migration scripts + report | Scripts + doc | Data eng | ก่อน M5 |
| 8 | UAT test plan + sign-off | เอกสาร | QA | M4-M5 |
| 9 | Operations runbook + training (ไทย) | เอกสาร | Tech Lead + Trainer | M5 |
| 10 | Warranty 3 เดือน post-go-live | บริการ | Support | หลัง M5 |

## 5. Functional Requirements (high-level)

- **FR-001:** Customer registration ด้วย phone + email OTP (KYC lite)
- **FR-002:** Points accrual — rules ตั้งค่าได้จาก admin console (by SKU, category, channel, member tier)
- **FR-003:** Points redemption — configurable redemption catalog, rules
- **FR-004:** Member tier logic — Silver / Gold / Platinum (เกณฑ์ upgrade/downgrade) [TBD: เกณฑ์ยังไม่ระบุชัด]
- **FR-005:** Mobile app: card view, transaction history, coupon wallet, store locator
- **FR-006:** Admin console: campaign/promotion/rule configuration UI
- **FR-007:** POS integration — รับ transaction events จาก Oracle XStore ผ่าน REST webhook → issue points
- **FR-008:** E-commerce integration — Magento 2 REST API (link account, sync transactions)
- **FR-009:** 360° customer view สำหรับ staff / call center
- **FR-010:** Leadership dashboard — members count, points issued/redeemed, GMV from members
- **FR-011:** Data migration — import 180k records จาก legacy stamp-card
- **FR-012:** Campaign/coupon distribution (push/email/in-app) [TBD: ช่องทาง push provider ยังไม่ระบุ]

## 6. Non-Functional Requirements

| หมวด | ข้อกำหนด |
|---|---|
| Performance | รองรับ 500,000 members, ≥ 50 points-tx/sec ช่วง peak |
| Performance (mobile) | "responsive and fast" [TBD: ยังไม่ quantify — ควรขอ target เช่น cold-start < 3s, screen transition < 500ms] |
| Availability | Admin console: Mon-Sat 08:00-22:00 ICT; Member-facing: 24/7 [TBD: % uptime SLA สำหรับ member-facing ยังไม่ระบุ — แนะนำ 99.5%] |
| Security | ปฏิบัติตาม PDPA; ผลสแกนต้องไม่มี critical/high vulnerabilities |
| Compliance | PDPA |
| Scalability | [TBD: growth projection ไม่ระบุ — ควรถามว่าจะขยายเกิน 500k หรือไม่ใน 1-2 ปี] |
| Support SLA (warranty) | P1 response 2 ชม., fix 8 ชม. |
| Usability | [TBD: ไม่มี explicit usability target] |

## 7. Timeline & Milestones

| Milestone | วันที่ | เงื่อนไขการส่งมอบ |
|---|---|---|
| M1 | 2026-06-15 | SRS + SDS signed off |
| M2 | 2026-08-01 | Admin console MVP + POS integration |
| M3 | 2026-09-01 | Mobile app beta (TestFlight/Internal) |
| M4 | 2026-09-30 | Pilot launch (5 stores) |
| M5 | 2026-10-31 | Full rollout + go-live |

## 8. ข้อจำกัด (Constraints)

- **เทคโนโลยี:** ต้อง deploy บน AWS ap-southeast-1 (ของ ACME), integrate กับ Oracle Retail XStore (REST webhook) และ Magento 2 (REST API)
- **กฎหมาย/Compliance:** PDPA
- **งบประมาณ:** 2.4M THB fixed-price
- **ทรัพยากร:** ACME ให้ API access ภายใน 2 สัปดาห์ (assumption — ถ้าช้าเป็น risk)
- **Change control:** การเปลี่ยนแปลง > 5 man-days ต้องมี CR ผ่าน Steering Committee

## 9. เงื่อนไขการรับมอบและชำระเงิน (Acceptance & Payment)

### Acceptance
- ทุก FR ใน SRS ผ่าน UAT, เหลือ minor defect ≤ 3 รายการ
- Load test ผ่านที่ 500k members + target TPS
- Security scan ไม่มี critical/high
- Data migration: ตรง 100% count, spot-check 200 records

### Warranty
- 3 เดือน post-go-live (P1 response 2h, fix 8h)

### งวดชำระ (5 งวด)
- 20% เซ็นสัญญา
- 20% M1 sign-off
- 20% M2 sign-off
- 20% M4 pilot
- 20% M5 go-live

## 10. Open questions / Gaps

- [ ] **Tier logic:** เกณฑ์ upgrade/downgrade Silver/Gold/Platinum ใช้อะไรเป็นตัววัด (ยอดซื้อ/คะแนนสะสม/ระยะเวลา) และช่วง threshold เท่าไร?
- [ ] **Push provider:** ใช้ FCM/APNS โดยตรง หรือผ่าน provider เช่น OneSignal/Braze? กระทบ cost + scope
- [ ] **Mobile performance target:** "responsive and fast" ต้อง quantify — เสนอ cold-start < 3s, screen transition < 500ms, API P95 < 800ms
- [ ] **Member-facing uptime SLA:** 24/7 แต่ไม่ระบุ % — เสนอ 99.5%
- [ ] **Design system:** มี brand guideline แล้วหรือไม่? ถ้ายัง ใครทำ และใช้เวลาเท่าไร?
- [ ] **Existing customer identity:** Magento มี customer อยู่แล้ว, POS ยังไม่มี digital ID — strategy การ merge identity ข้าม channel?
- [ ] **Campaign notification channels:** SMS / email / push / in-app — ทั้งหมดหรือบางส่วน? ใครเป็นเจ้าของ cost SMS?
- [ ] **Data retention / PDPA consent:** flow การขอ consent + right to be forgotten จัดการอย่างไร?
- [ ] **Call center tooling:** 360° view ใช้ใน tool อะไร (web app แยก / integrate กับ Salesforce / อื่น ๆ)?
- [ ] **Leadership dashboard:** realtime หรือ daily batch? ใช้ BI tool ที่มีอยู่หรือสร้างใหม่?
- [ ] **App Store / Play Store account:** ACME มี developer account อยู่แล้วหรือไม่? ถ้ายัง ใคร submit?
- [ ] **Pilot-to-full criteria:** ตัววัดที่ทำให้ตัดสินใจ rollout จาก pilot 5 สาขา → 42 สาขา คืออะไร?

## 11. Raw excerpts (สำหรับตรวจสอบ)

> "System must support 500,000 members and at least 50 points transactions per second during peak" — §6
>
> "Mobile app should be responsive and fast" — §6 (ต้อง quantify)
>
> "All functional requirements in SRS pass UAT with ≤ 3 minor defects open" — §9
>
> "Changes > 5 man-days require written CR approved by Steering Committee" — §11
