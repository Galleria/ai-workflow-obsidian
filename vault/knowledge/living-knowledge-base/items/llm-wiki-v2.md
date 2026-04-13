---
type: research-item
topic: Living knowledge base
item: LLM Wiki v2 (Karpathy + community extensions)
category: modern-tooling
parent: "[[outline]]"
researched: 2026-04-13
last-accessed: 2026-04-13
review-after: 2026-06-12
confidence: 0.55
tags:
  - research
  - research/deep
  - living-knowledge-base
  - tooling
  - confidence/low
---

# LLM Wiki v2

> [!summary]
> ต่อยอดจาก **Karpathy's LLM Wiki** (AI สร้าง/จัดการ wiki ของตัวเอง — long-term memory, ลดหลอน). V2 เพิ่ม 7 features เปลี่ยน wiki เรียบๆ → ระบบความจำที่มีชีวิต: confidence score, memory tiers, typed knowledge graph, hybrid search, automated hooks, forgetting curves, contradiction resolution

## Core idea (v1 → v2)

**V1 (Karpathy)**: AI เขียน wiki pages ให้ตัวเองไว้อ่าน/อ้าง — ลดการคำนวณซ้ำ, ลด hallucination ระยะยาว

**V2 (community extension)**: v1 wiki ใช้ไม่ scale เพราะขาด memory hierarchy, ไม่แยกความมั่นใจ, ข้อมูลเก่า/ใหม่ปนกัน. V2 เพิ่ม 7 features ให้ wiki "มีชีวิต" — grow, compress, forget, resolve conflicts อัตโนมัติ

## 7 Features (v2)

| # | Feature | What it does |
|---|---|---|
| 1 | **Confidence Scoring** | ทุกข้อมูลมี score 0.0-1.0; ค้นหา/ใช้ได้ filter by confidence |
| 2 | **Memory Tiers** | 4 ระดับ: Working (เพิ่งเกิด) → Episodic (สรุปแต่ละ session) → Semantic (ความรู้ข้ามสาย) → Procedural (ขั้นตอน). ยิ่งสูง ยิ่งกระชับ+คงทน |
| 3 | **Typed Knowledge Graph** | แทน flat wikilinks ด้วย typed relations: `A causes B, verified by 3 sources, confidence 0.9`. Graph traversal จับ connection ที่ keyword search มองข้าม |
| 4 | **Hybrid Search** | BM25 (keyword) + Vector (semantic) + Graph traversal — ครอบ 3 mode ของการค้น |
| 5 | **Automated Hooks** | New source → auto-ingest; Session end → compress+store; Scheduled → review, merge, demote. แก้ปัญหา "wiki maintenance burden" ที่ฆ่า wiki ทั่วไป |
| 6 | **Forgetting Curves** | ข้อมูลไม่ถูก access นาน → จางลง (ไม่ลบ, แค่ demote ranking). Architecture decisions จางช้า, bugs ชั่วคราวจางเร็ว |
| 7 | **Contradiction Resolution** | AI เข้าใจ + resolve contradictions อัตโนมัติ (scope ไม่ระบุชัดในโพสต้นฉบับ) |

## Historical / authorial origin

**V1**: Andrej Karpathy (อดีต Tesla AI Director, OpenAI researcher) — promote แนวคิดนี้ในชุมชน AI ปี 2024-2025

**V2**: community-driven extension; source ที่ capture คือโพสต์ shared จากผู้ใช้ไทย 2026-04-13 (ดู [[_raw/llm-wiki-v2-2026-04-13]]). Primary source + reference implementation ยังไม่ได้ verify ใน research รอบนี้ — #uncertain

## Tools / examples

เท่าที่โพสต้นฉบับระบุ: เป็นแนวทาง (architecture pattern) ไม่ใช่ tool เฉพาะ. Implementation ตัวอย่างในโลกจริงที่ใกล้เคียง:
- [[items/obsidian-claude-patterns]] — [eugeniughelbur/obsidian-second-brain](https://github.com/eugeniughelbur/obsidian-second-brain) มี 4 จาก 7 features (hooks, contradiction handling, multi-tier via `raw/` + `wiki/`, implicit forgetting via daily notes)
- เต็มรูปแบบ 7/7 ยังไม่เจอ public implementation ในรอบนี้ — #uncertain

## Strengths & trade-offs

- ✅ **Complete architecture** — เป็น coherent spec ที่แก้ pain points ของ wiki classical (collector's fallacy, stale info, contradiction paralysis) แบบ systematic
- ✅ **Memory tiers ฉลาด** — มนุษย์มี working/episodic/semantic/procedural อยู่แล้ว; mapping ตรงนี้สะท้อน cognitive architecture จริง
- ⚠️ **Infra requirement สูง** — hybrid search ต้อง vector DB + embedding service + graph engine (Neo4j/ArangoDB/custom). ไม่ใช่ markdown + Obsidian ล้วนๆ ได้อีกแล้ว
- ⚠️ **Automated hooks = trust stake** — ถ้า auto-merge ผิด + forgetting ผิด = vault เน่าเงียบ. ต้อง audit trail แข็ง
- ⚠️ **"Contradiction resolution อัตโนมัติ"** — scope กว้าง, โพสไม่ชัดว่าเคสไหน AI resolve vs escalate to human. Red flag: over-automation เสี่ยงซ่อนข้อมูลสำคัญ
- ⚠️ **Single-source confidence (ในรอบ research นี้)** — โพสต์เดียว, ไม่มี primary paper/repo link, ไม่ verify กับ Karpathy's original

## Mapping ต่อ POC ของเรา (2026-04-13)

สถานะการ adopt แต่ละ feature:

| V2 feature | POC status | Implementation |
|---|---|---|
| 1. Confidence scoring | ✅ adopted | `confidence: 0.0-1.0` frontmatter + `confidence/low|medium|high` tags |
| 2. Memory tiers | ⚠️ partial | `skills/` ≈ procedural, `knowledge/` ≈ semantic. ขาด working/episodic |
| 3. Typed knowledge graph | ✅ adopted (lean) | inline relation tags: `[[A]] #causes [[B]]`. ไม่มี graph engine — ใช้ Dataview query แทน |
| 4. Hybrid search | ❌ deferred | overkill; Obsidian native search + Dataview พอ |
| 5. Automated hooks | ❌ deferred | manual ops ก่อน (defer ตามเดิม) |
| 6. Forgetting curves | ✅ adopted (lean) | `last-accessed` + `review-after` fields; dashboard surfaces stale items; ไม่ auto-demote |
| 7. Contradiction resolution | ✅ adopted (lean) | `update-knowledge` skill มี protocol: preserve both → tag `#contradiction` → classify → drop confidence → report to user. ไม่ auto-resolve |

คะแนน: adopted 4/7 full + 1 partial; deferred 2/7 (hybrid search, automated hooks) — คุ้ม/infra cost ยังไม่ justify สำหรับ POC scale

## Typed relations
- [[items/llm-wiki-v2]] #refines [[items/obsidian-claude-patterns]] — spec ชัดกว่า, ครอบคลุมกว่า (7 features vs. general pattern)
- [[items/llm-wiki-v2]] #builds-on [[items/zettelkasten]] — inherit atomic + link concept
- [[items/llm-wiki-v2]] #contradicts [[items/anti-patterns]] ที่ข้อ "AI-only vaults" — v2 ตั้งใจให้ AI เป็น primary writer เต็มตัว; POC เราเลือกกลางๆ (AI เขียน, user review weekly)

## Sources
[^1]: [[_raw/llm-wiki-v2-2026-04-13]] — shared community post (Thai), captured 2026-04-13. Single source. #uncertain primary-reference
[^2]: Andrej Karpathy public talks/tweets ~2024-2025 (LLM Wiki concept origin) — not fetched in this research round; TODO follow-up

> [!opinion] AI take #opinion
> V2 spec ถูกต้องในทิศทาง — address pain points ที่ v1 wiki เจอจริง. แต่ 3/7 features (hybrid search, automated hooks, full typed graph) ต้องการ infra stack ใหญ่ที่ contradiction กับปรัชญา "local-first markdown vault" ของ Obsidian/Claude Code. การ adopt แบบ **lean subset** (1+3+6+7) ได้ 80% ของ value ที่ 20% ของ cost — นี่คือสิ่งที่ POC เราทำ. อย่า over-engineer เต็มสเป็ก จนกว่าจะมี vault ขนาด 1000+ notes ที่ native search/Dataview ใช้ไม่ไหวจริงๆ. ส่วน #7 contradiction resolution "อัตโนมัติ" เต็มตัวผม skeptical — trust boundary ใหญ่เกินไป; POC ของเราเลือก "surface + preserve + ask user" ดีกว่า