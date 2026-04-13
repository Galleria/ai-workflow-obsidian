# Statement of Work — ACME Retail Loyalty Program Platform

**Client:** ACME Retail Co., Ltd.
**Vendor:** 2Smooth Co., Ltd.
**Date:** 2026-04-01
**Contract type:** Fixed-price
**Contract value:** 2,400,000 THB (excl. VAT)
**Duration:** 2026-05-01 → 2026-10-31 (6 months)

---

## 1. Background

ACME Retail operates 42 physical stores and an e-commerce site. Current loyalty program is a stamp-card system at POS only — no digital channel, no unified customer view across online/offline. Leadership wants a unified loyalty platform before Q4 peak season.

## 2. Objectives

- Unified customer identity across POS, e-commerce, and a new mobile app
- Points accrual and redemption rules configurable by marketing team (no code changes)
- 360° customer view for store staff and call center
- Launch in a pilot of 5 stores, then rollout to all 42

## 3. Scope

### In scope
- Customer registration / KYC lite (phone + email OTP)
- Points engine (earn rules, redemption rules, tier logic: Silver/Gold/Platinum)
- Member mobile app (iOS + Android) — card view, transaction history, coupon wallet, store locator
- Admin web console for marketing team to configure campaigns, earn rules, promotions
- Integration with existing POS (Oracle Retail XStore, REST webhook) and e-commerce (Magento 2, REST API)
- Basic dashboard for leadership (members count, points issued/redeemed, GMV from members)
- Data migration from legacy stamp-card system (~180,000 member records)

### Out of scope
- Replacement of POS itself
- Loyalty partnerships with external brands (phase 2)
- Gamification features
- AI-driven personalization (phase 2)

## 4. Deliverables

1. Project management plan and schedule
2. System Requirements Specification (SRS)
3. System Design Specification (SDS) including architecture diagram
4. Source code + CI/CD pipeline (GitLab)
5. Member mobile app published to App Store + Google Play under ACME's developer account
6. Admin web console deployed on ACME's AWS (ap-southeast-1)
7. Data migration scripts + migration report
8. UAT test plan and UAT sign-off
9. Operations runbook and training materials (Thai)
10. 3-month post-go-live warranty

## 5. Key stakeholders

- **Sponsor:** Ms. Suwanna Tanakit, CMO, ACME Retail
- **Product owner:** Mr. Kittipong Chai, Head of Loyalty, ACME Retail
- **IT counterpart:** Mr. David Lee, CTO Office, ACME Retail
- **PM (vendor):** TBD
- **Tech lead (vendor):** TBD

## 6. Non-functional expectations

- System must support **500,000 members** and **at least 50 points transactions per second** during peak
- Mobile app should be **responsive and fast**
- Data must be protected per **PDPA**
- Admin console available during business hours (Mon-Sat 08:00-22:00 ICT); member-facing services 24/7
- SLA for post-launch warranty: P1 response 2 hours, fix 8 hours

## 7. Timeline (milestones)

| Milestone | Target |
|---|---|
| M1: SRS + SDS signed off | 2026-06-15 |
| M2: Admin console MVP + POS integration | 2026-08-01 |
| M3: Mobile app beta (TestFlight/Internal) | 2026-09-01 |
| M4: Pilot launch (5 stores) | 2026-09-30 |
| M5: Full rollout + go-live | 2026-10-31 |

## 8. Assumptions

- ACME provides POS and Magento API access within 2 weeks of contract signing
- ACME provides legacy stamp-card database dump for migration
- Design system / brand guideline is provided by ACME's marketing team
- All content/translation provided in Thai by ACME; vendor does not translate marketing copy

## 9. Acceptance criteria

- All functional requirements in SRS pass UAT with ≤ 3 minor defects open
- Load test demonstrates 500k member capacity at target TPS
- Security scan shows no critical/high vulnerabilities
- Data migration: 100% record count match, spot-check 200 records

## 10. Payment schedule

- 20% on contract signing
- 20% on M1 sign-off
- 20% on M2 sign-off
- 20% on M4 pilot launch
- 20% on M5 go-live + 3-month warranty start

## 11. Change management

Changes > 5 man-days require written CR approved by Steering Committee.
