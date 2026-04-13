---
type: sequence
source: SRS §3.2 FR-002, FR-007
tool: mermaid
---

# Sequence — Points accrual from POS transaction

```mermaid
sequenceDiagram
  actor Cashier as พนักงานเก็บเงิน
  participant POS as Oracle XStore POS
  participant API as Loyalty API
  participant Rule as Rule Engine
  participant Ledger as Points Ledger
  participant Notif as Notification Service
  actor Member as สมาชิก (app)

  Cashier->>POS: scan member QR + สินค้า
  POS->>POS: complete transaction
  POS->>API: POST /webhook/transaction (tx_id, member_id, items, amount)
  activate API
  API->>API: verify HMAC signature
  API->>API: idempotency check by tx_id
  alt first time
    API->>Rule: evaluate earn rules (items, channel=POS, tier)
    Rule-->>API: points = N, rule_id
    API->>Ledger: INSERT accrual (member_id, +N, tx_id, rule_id)
    API->>Notif: push("ได้รับ N คะแนน")
    Notif-->>Member: notification
    API-->>POS: 200 OK
  else duplicate
    API-->>POS: 200 OK (no-op)
  end
  deactivate API

  Note over API,Ledger: retry-safe: ถ้า 5xx, XStore retry โดยใช้ tx_id เดิม
```

## Edge cases (ต้องครอบคลุมใน implementation)
- Member-less transaction (ไม่มี member_id ใน payload) — ไม่ทำอะไร, ตอบ 200
- Rule inactive หรือไม่ match — บันทึก attempt แต่ไม่ออกคะแนน, log reason
- Member suspended — skip accrual, log
- Partial refund (transaction void / refund event) — reverse ledger entry ตาม tx_id
