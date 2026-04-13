---
type: erd
source: SRS §3.2 FR-001..FR-012
tool: mermaid
---

# ERD — Loyalty Platform (conceptual)

```mermaid
erDiagram
  MEMBER ||--o{ IDENTITY : has
  MEMBER ||--|| TIER : assigned
  MEMBER ||--o{ POINTS_LEDGER : accrues
  MEMBER ||--o{ REDEMPTION : makes
  MEMBER ||--o{ CONSENT : grants

  IDENTITY {
    string id PK
    string member_id FK
    string provider "phone | email | magento | legacy"
    string external_id
    datetime verified_at
  }

  MEMBER {
    string id PK
    string phone
    string email
    string first_name
    string last_name
    date birthdate
    string tier_id FK
    string status "active | suspended | deleted"
    datetime created_at
  }

  TIER {
    string id PK
    string name "Silver | Gold | Platinum"
    int rank
    json upgrade_criteria
    json downgrade_criteria
  }

  POINTS_LEDGER {
    bigint id PK
    string member_id FK
    string type "accrual | redemption | adjustment | expiry"
    int points
    string source_tx_id "POS tx id / Magento order id"
    string rule_id FK
    datetime occurred_at
  }

  EARN_RULE ||--o{ POINTS_LEDGER : applied
  EARN_RULE {
    string id PK
    string name
    json filter "channel, sku, category, tier, date_range"
    string formula "e.g. 1pt per 10 THB"
    bool active
    datetime valid_from
    datetime valid_to
  }

  REDEMPTION {
    string id PK
    string member_id FK
    string catalog_item_id FK
    int points_used
    string voucher_code
    string status "issued | used | expired"
    datetime issued_at
  }

  REDEMPTION_CATALOG {
    string id PK
    string name
    int points_required
    json tier_restriction
    int stock
  }

  CAMPAIGN ||--o{ NOTIFICATION : sends
  CAMPAIGN {
    string id PK
    string name
    json segment
    json channels "push | email | sms | in_app"
    datetime starts_at
    datetime ends_at
  }

  NOTIFICATION {
    string id PK
    string campaign_id FK
    string member_id FK
    string channel
    string status
    datetime sent_at
  }

  CONSENT {
    string id PK
    string member_id FK
    string type "marketing | analytics | profiling"
    bool granted
    datetime granted_at
    datetime revoked_at
  }

  REDEMPTION ||--|| REDEMPTION_CATALOG : redeems
```

## Notes
- `POINTS_LEDGER` is the source of truth (no aggregated `points_balance` column — derive)
- `IDENTITY` supports multi-provider linking (POS / Magento / legacy / phone / email)
- `CONSENT` is PDPA-first — separate record per consent type, with grant/revoke timestamps
- TBDs flagged in SRS §3.2 FR-004 (tier criteria) reflected as `json` blobs here until rules are confirmed
