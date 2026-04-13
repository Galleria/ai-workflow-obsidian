---
type: research-item
topic: Living knowledge base
item: Zettelkasten
category: foundational-method
parent: "[[outline]]"
researched: 2026-04-13
tags:
  - research
  - research/deep
  - living-knowledge-base
  - pkm/method
---

# Zettelkasten

> [!summary]
> ระบบ note-taking ที่ [[items/evergreen-notes|Niklas Luhmann]] ใช้ผลิตงานวิชาการ 70+ เล่ม — โน้ตสั้นต่อแผ่น, ทุกแผ่นมี ID, เชื่อมกันด้วย references. รากฐานของทุก "living knowledge base" สมัยใหม่

## Core idea
โน้ตเล็ก atomic (1 ไอเดียต่อแผ่น) + ระบบลิงก์ระหว่างแผ่น → network ที่งอกเองเมื่อใส่เข้าไปเรื่อยๆ. "Thinking partner" ไม่ใช่แค่ที่เก็บ

## Key principles
- **Atomicity** — 1 แผ่น = 1 ไอเดีย; ตัดแล้วยังเข้าใจได้
- **Unique ID** — ทุกแผ่นมีเลขกำกับ (เช่น `21/3a`) เพื่อลิงก์ไปถึงได้
- **Links over hierarchy** — ไม่ใส่ในหมวดหมู่ตายตัว; ใช้ cross-reference แทน folder structure
- **Own words** — เขียนใหม่ด้วยภาษาตัวเอง ไม่ copy-paste
- **Write for your future self** — บริบทพอให้ 5 ปีข้างหน้าเข้าใจ

## Historical origin
Niklas Luhmann (1927-1998) นักสังคมวิทยาเยอรมัน. Zettelkasten ของเขา ~90,000 แผ่น ในตู้ไม้. หลังเสียชีวิต มีการ digitize เผยแพร่ที่ University of Bielefeld

## Tools / examples
- [Zettelkasten.de](https://zettelkasten.de/) — community + software
- Obsidian, Roam Research, Logseq — ทุกตัว implement แนวคิดนี้
- ต้นฉบับกระดาษยัง scan ได้จาก Bielefeld

## Strengths & trade-offs
- ✅ **Compounds**: ค่าของ vault เติบโต super-linear เมื่อลิงก์ขยาย
- ✅ **Emergent structure**: ไอเดียใหม่โผล่จาก cluster ที่เรา "ไม่ได้วางแผน"
- ⚠️ **Upfront cost**: ทุก note ต้อง refactor ให้ atomic + link — ช้ากว่า dump
- ⚠️ **Discipline required**: ระบบล้มถ้า title/links มักง่าย → กลายเป็น note graveyard (ดู [[items/anti-patterns]])

## Sources
[^1]: [Zettelkasten — Wikipedia](https://en.wikipedia.org/wiki/Zettelkasten) — accessed 2026-04-13
[^2]: [A Beginner's Guide to the Zettelkasten Method — Zenkit](https://zenkit.com/en/blog/a-beginners-guide-to-the-zettelkasten-method/)

> [!opinion] AI take #opinion
> Zettelkasten บริสุทธิ์ (ID-based, manual linking) overkill สำหรับคนส่วนใหญ่ในปี 2026 — tool สมัยใหม่ (Obsidian/Logseq) แทน ID ด้วย wikilink ได้เลย. คุณค่าจริงไม่ได้อยู่ที่ ID system แต่คือ **วินัย atomicity + own-words** — สองอันนี้จะใช้ method ไหนก็ compound ได้. เริ่มด้วย [[items/evergreen-notes]] (lighter) แล้วย้อนกลับมาดู Zettelkasten ถ้าต้องการ rigor มากขึ้นดีกว่า
