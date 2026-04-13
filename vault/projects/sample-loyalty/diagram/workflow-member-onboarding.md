---
type: workflow
source: SRS §3.2 FR-001
tool: mermaid
---

# Workflow — Member onboarding (new registration)

```mermaid
flowchart TD
  Start([เริ่ม: เปิด app / กด สมัครสมาชิก])
  Start --> F1[กรอก เบอร์ + email + PDPA consent]
  F1 --> CheckDup{เบอร์มีอยู่แล้ว?}
  CheckDup -->|ไม่มี| SendOTP[ส่ง OTP ทั้ง SMS + email]
  CheckDup -->|มี — legacy| MergePath[เสนอ merge account]
  CheckDup -->|มี — active| Login[นำไปหน้า Login]

  SendOTP --> VerifyOTP{กรอก OTP ถูกต้อง?}
  VerifyOTP -->|ไม่| Retry[แจ้ง error, retry 3 ครั้ง]
  Retry --> VerifyOTP
  VerifyOTP -->|ใช่ ครบทั้ง 2 ช่องทาง| Profile[กรอก ชื่อ-นามสกุล, วันเกิด opt]
  Profile --> Create[สร้าง member record, tier=Silver]
  Create --> Welcome[ส่ง welcome notification + bonus คะแนน ถ้ามี campaign]
  Welcome --> Done([เสร็จ: พร้อมใช้งาน])

  MergePath --> MigrateVerify[verify ด้วย OTP เดิม]
  MigrateVerify --> MigrateLegacy[import stamp balance → points ตามสูตร conversion]
  MigrateLegacy --> Done
```

## Notes
- PDPA consent จับเป็น record แยกต่อ type (ดู ERD `CONSENT`)
- สูตร conversion stamp → points [TBD: FR-011] ยังไม่ระบุ — placeholder จนกว่าจะยืนยัน
- Retry cap 3 ครั้งต่อ session; เกินให้รอ 15 นาที (anti-abuse)
