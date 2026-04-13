---
type: research-item
topic: Living knowledge base
item: Anti-patterns & failure modes
category: what-not-to-do
parent: "[[outline]]"
researched: 2026-04-13
last-accessed: 2026-04-13
review-after: 2026-07-12
confidence: 0.8
tags:
  - research
  - research/deep
  - living-knowledge-base
  - pkm/anti-pattern
  - confidence/high
---

# Anti-patterns & failure modes

> [!summary]
> วิธีที่ living knowledge base "ตาย" มากกว่าที่ใคร debate กันเรื่อง methodology — collector's fallacy, tool-hopping, over-organization. รู้ไว้เพื่อหลีกเลี่ยง

## Common failure modes

### 1. Collector's fallacy
Capture เยอะ, distill น้อย → read-later queue ที่ไม่เคยกลับมาดู. วัดจาก ratio: notes edited ≥ 2 ครั้ง / notes ทั้งหมด. ถ้า <20% แปลว่า collect อย่างเดียว

### 2. Note graveyard
Note atomic ถูกต้อง, แต่ไม่มี link → searchable แต่ไม่ compound. วัดจาก avg backlinks/note. Andy Matuschak แนะนำ ≥2-3 [^1]

### 3. Tool hopping
ย้าย Notion → Obsidian → Logseq → Roam ทุกๆ 3 เดือน. ทุกครั้งสูญ context + momentum. Solution: stick ≥1 year per tool, เลือก markdown-based เพื่อ migration ง่าย

### 4. Over-organization / bikeshedding folder structure
ใช้เวลา refactor folder + YAML schema > time เขียนไอเดีย. Atomic + tag-based ไม่ต้อง folder ลึก — [[items/evergreen-notes]] ให้เหตุผลไว้

### 5. Copy-paste zettelkasten
จดโดย copy-paste quote แทนเขียนด้วยภาษาตัวเอง → ไม่เข้าหัว, ใช้ไม่ได้ตอน recall

### 6. Template bloat
สร้าง 20+ templates (daily note, project, meeting, book, movie, person, decision, ADR…) ใช้จริง 2-3. Start with 1-2, เพิ่มเมื่อต้องการซ้ำ ≥3 ครั้ง

### 7. AI-only vaults (new, 2025+)
Agent เขียน vault ทั้งหมด, user ไม่ intervene → แทนที่จะเป็น "second brain" กลายเป็น "AI's brain that user can't trust". Mitigation: git + review gates; user ยัง "write mode" อย่างน้อย weekly

## Red-flag signals (self-audit)
- [ ] เปิด vault ครั้งสุดท้ายเพื่อเพิ่ม > เพื่อเปิดดูข้อมูลเดิม?
- [ ] มี note ที่ไม่มี backlink > 50%?
- [ ] มี note ที่ title เป็น "Meeting 2025-04-13" มากกว่า "Client X prioritizes X over Y because Z"?
- [ ] มี template ที่ใช้ครั้งเดียวแล้วไม่ได้ใช้อีก?

## Sources
[^1]: [Evergreen notes — Andy Matuschak](https://notes.andymatuschak.org/Evergreen_notes)
[^2]: [The Collector's Fallacy — Zettelkasten.de](https://zettelkasten.de/posts/collectors-fallacy/)

> [!opinion] AI take #opinion
> Collector's fallacy (#1) คือตัวฆ่า vault อันดับหนึ่ง — มากกว่า tool choice, มากกว่า method debate. สาเหตุคือ capture ให้ dopamine (เหมือน shopping), distill ต้องใช้ effort. คำแนะนำจริงๆ: **วัดค่า vault ด้วย notes-revisited ไม่ใช่ notes-created**. ถ้าเดือนนี้ capture 50 notes แต่กลับมาแก้/ลิงก์ 0 อัน แสดงว่า vault ไม่ compound จริง. สำหรับ AI-augmented vault (#7) เพิ่มความเสี่ยงใหม่ — delegate distill ให้ AI ทั้งหมด → user สูญการเข้าใจลึก. Rule of thumb: AI เขียน, user **revisit + approve** อย่างน้อย 20% ของ notes
