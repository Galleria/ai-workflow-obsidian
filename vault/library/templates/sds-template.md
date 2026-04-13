---
type: sds
standard: IEEE 1016 (pragmatic) + arc42 influence
language: th
tags:
  - sds
  - draft
aliases:
  - System Design Specification
  - Software Design Specification
---

# เอกสารออกแบบระบบ (System/Software Design Specification)

**โครงการ:** [ชื่อโครงการ]
**เวอร์ชัน:** 0.1
**วันที่:** [YYYY-MM-DD]
**ผู้ออกแบบ:** [Tech Lead / Architect]

---

## 1. บทนำ

### 1.1 วัตถุประสงค์
[เอกสารนี้ออกแบบ solution ให้ครอบคลุม requirement ใน SRS v<ver>; กลุ่มผู้อ่าน: dev, QA, ops, ทีมลูกค้าฝั่ง IT]

### 1.2 ขอบเขตการออกแบบ
[modules/services ที่ออกแบบในเอกสารนี้]

### 1.3 เอกสารอ้างอิง
- SRS: [[srs]]
- SOW extract: [[extracted]]
- มาตรฐาน: IEEE 1016, arc42

---

## 2. Architecture Overview

### 2.1 System context (C4 Level 1)
> ```mermaid
> flowchart LR
>   User --> System
>   System --> ExternalA
> ```

### 2.2 Containers (C4 Level 2)
> ```mermaid
> flowchart TB
>   subgraph System
>     Web[Admin web]
>     Mobile[Mobile app]
>     API[Core API]
>     Worker[Background workers]
>     DB[(Database)]
>     Cache[(Cache)]
>     Queue[(Queue)]
>   end
> ```

### 2.3 Technology stack
| Layer | Choice | Rationale |
|---|---|---|
| Frontend (web) | [TBD] | |
| Frontend (mobile) | [TBD] | |
| Backend | [TBD] | |
| Database | [TBD] | |
| Cache | [TBD] | |
| Queue | [TBD] | |
| Infra | AWS ap-southeast-1 | [SOW constraint] |
| CI/CD | GitLab CI | |

### 2.4 Deployment diagram
> ```mermaid
> flowchart LR
>   subgraph "AWS ap-southeast-1"
>     ALB[ALB] --> ECS[ECS Fargate]
>     ECS --> RDS[(RDS)]
>     ECS --> Redis[(ElastiCache)]
>   end
> ```

---

## 3. Component Design (per SRS requirement)

### 3.1 [Component / Service name]
- **Responsibilities:** [bullet list]
- **Interfaces:** [API endpoints / events consumed / events emitted]
- **Key decisions:** [design rationale, alternatives considered]
- **Maps to SRS:** FR-xxx, FR-yyy

> Sequence / class diagram ถ้าจำเป็น

### 3.2 [Component 2] ...

---

## 4. Data Design

### 4.1 ERD
> ```mermaid
> erDiagram
>   A ||--o{ B : relation
> ```

### 4.2 Data dictionary
| Entity | Field | Type | Constraints | Notes |
|---|---|---|---|---|

### 4.3 Data lifecycle
- Retention policy
- Archival
- Backup / restore RPO / RTO

### 4.4 PII / PDPA handling
- [Fields classified as PII]
- [Encryption: at rest / in transit / in use]
- [Consent linkage]

---

## 5. API Design

### 5.1 Style
[REST / gRPC / GraphQL — pick one per context + why]

### 5.2 Endpoint catalog
| Method | Path | Auth | SRS ref |
|---|---|---|---|
| POST | /auth/otp/send | Public | FR-001 |

### 5.3 Error model
[Standard envelope: code, message, details, trace_id]

### 5.4 Versioning strategy
[URL-based / header-based, deprecation policy]

---

## 6. Security Design

- AuthN: [TBD]
- AuthZ: [TBD — role matrix]
- Secrets management: [TBD]
- Network: [TBD]
- Logging/audit: [TBD]
- OWASP top 10 mitigations: [TBD]

---

## 7. Integration Design

| External | Direction | Protocol | Reliability strategy |
|---|---|---|---|

---

## 8. Observability

- Metrics (Prometheus / CloudWatch): [TBD]
- Logs (centralized): [TBD]
- Tracing: [TBD]
- Alerting: [TBD]
- Dashboards: [TBD]

---

## 9. Non-functional design decisions

| NFR (from SRS §3.3) | Design approach |
|---|---|
| 500k members / 50 tps | horizontal scale ECS + read replica + cache |
| 99.5% uptime | multi-AZ, graceful degradation |

---

## 10. Deployment & Ops

- Environment strategy (dev/staging/prod)
- Release strategy (blue-green / canary / rolling)
- Rollback
- Data migration strategy
- Runbook references

---

## 11. Open design decisions (ADR index)

| # | Decision | Status | Date |
|---|---|---|---|
| ADR-001 | [e.g. Choose Postgres over MongoDB] | accepted | |

---

## 12. Revision History
| Version | Date | Author | Changes |
|---|---|---|---|
| 0.1 | | | Initial draft |
