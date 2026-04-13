---
type: research-index
topic: Living knowledge base
parent: "[[outline]]"
item-count: 6
generated: 2026-04-13
last-updated: 2026-04-13
last-accessed: 2026-04-13
review-after: 2026-07-12
confidence: 0.8
visibility: public
tags:
  - research
  - research/index
  - living-knowledge-base
  - confidence/high
---

# Living knowledge base — research index

> [!info] สรุป
> "Living knowledge base" = personal knowledge system ที่ **compound ตามเวลา** (ยิ่งใส่ ยิ่งมีค่า เพราะ link/insight ใหม่โผล่เอง) ไม่ใช่แค่ที่เก็บไฟล์. มี 3 คลื่นหลักของแนวคิด + คลื่นที่ 4 กำลังมา (AI-augmented)

## TL;DR ใน 6 บรรทัด
1. **Zettelkasten (1960s, Luhmann)** — ต้นตอ: atomic notes + links + unique IDs → network งอกเอง
2. **Evergreen notes (2019+, Matuschak)** — refinement สำหรับ hypertext: concept-oriented, densely linked, own words
3. **PARA / BASB (2017+, Forte)** — workflow-centric: Projects/Areas/Resources/Archive + CODE (capture-organize-distill-express)
4. **AI-augmented vaults (2025+)** — Claude/LLM เป็น primary writer; ingest rewrite หลายหน้า; scheduled maintenance agents
5. **Anti-patterns กินคนเยอะกว่า methodology** — collector's fallacy, note graveyard, tool hopping, over-organization
6. **LLM Wiki v2 (2026, Karpathy + community)** — spec 7 features ยก wiki เรียบๆ ให้มี confidence, memory tiers, typed graph, hybrid search, auto-hooks, forgetting, contradiction resolution. POC เรา adopt 4/7 + partial 1 + defer 2

## Items

| # | Item | Category | Summary | Confidence | Notes |
|---|---|---|---|---|---|
| 1 | [[items/zettelkasten\|Zettelkasten]] | foundational-method | Luhmann's atomic-linked system — รากฐานของทุกอย่าง | 0.9 | — |
| 2 | [[items/evergreen-notes\|Evergreen notes]] | modern-synthesis | Matuschak: write to compound, concept-oriented | 0.9 | — |
| 3 | [[items/second-brain-para\|PARA / BASB]] | popular-framework | Forte: action-first, 4 folders + CODE workflow | 0.8 | ขัดกับ evergreen ในจุด organization |
| 4 | [[items/obsidian-claude-patterns\|Obsidian + Claude patterns]] | modern-tooling | vault ที่ Claude เป็น primary reader/writer | 0.7 | updated 2026-04-13 with POC notes |
| 5 | [[items/anti-patterns\|Anti-patterns]] | what-not-to-do | collector's fallacy, graveyard, tool hopping | 0.8 | — |
| 6 | [[items/llm-wiki-v2\|LLM Wiki v2]] | modern-tooling | Karpathy's LLM Wiki + 7 features (confidence, tiers, graph, search, hooks, forgetting, contradictions) | 0.55 | #new 2026-04-13 via update-knowledge; single-source, needs corroboration |

## Cross-cutting findings

**Pattern ที่ทุก school เห็นตรงกัน:**
- **Atomic** — 1 ไอเดีย 1 note; ตัดแล้วยังเข้าใจได้
- **Own words** — เขียนใหม่ ไม่ copy-paste (ไม่งั้น recall ไม่ออก)
- **Links > folders** — associative > hierarchical (ยกเว้น PARA ที่ hybrid)
- **Write for future self** — บริบทพอให้ 5 ปีข้างหน้าเข้าใจ

**จุดที่ debate กัน:**
- **Organization**: concept-first (evergreen) vs. project-first (PARA) — hybrid ใช้ได้ (PARA ที่ capture, evergreen ที่ resources/)
- **Automation level**: manual (classic) vs. AI-augmented (2025+) — trade-off trust vs. leverage
- **Publish or not**: Matuschak publish public garden, Forte แนะนำ private-first

## Relevance ต่อ POC ของเรา (updated 2026-04-13)

POC นี้เป็น **doc-workflow partner** เป็นหลัก + second-brain layer เสริม. สถานะ pattern ที่เอามาใช้:

**✅ Implemented:**
- `knowledge/` folder = evergreen zone สำหรับ topic ข้ามโปรเจกต์
- Wikilinks + frontmatter tags (ผ่าน [[obsidian-markdown]]) = link density
- `_CLAUDE.md` vault-root operating manual + read-order rules (token discipline)
- `knowledge/README.md` = catalog cheap entry point
- `> [!opinion]` callout convention แยก AI judgment จาก fact
- `_raw/` folder per topic = immutable source trail
- Markitdown multi-format ingestion (DOCX/PPTX/XLSX/HTML/EPub → .md)
- `update-knowledge` skill (append/merge/refresh modes; preserves provenance)
- Visibility scheme (`private|internal|public`)

**⚠️ Deferred:**
- `CRITICAL_FACTS.md` always-loaded pin — ยังไม่มี ground-truth facts ระดับนี้
- Scheduled agents (morning/nightly) — manual ops ก่อน

**❌ Rejected:**
- Rename `knowledge/` → `wiki/` — rename churn ไม่คุ้ม
- Auto-ingest ที่ rewrite หลายหน้าอัตโนมัติ — trust boundary ใหญ่เกินไป, ใช้ explicit `update-knowledge` แทน

รายละเอียดเต็ม + lessons learned ดู [[items/obsidian-claude-patterns#Update 2026-04-13]]

## Changelog

- **2026-04-13** (append, via update-knowledge) — new item [[items/llm-wiki-v2]] added from community post capture ([[_raw/llm-wiki-v2-2026-04-13]]). Introduces 7-feature spec (Karpathy extension). Cross-cutting findings section below extended to reflect; retrofitted 5 original items with `confidence` scores + `last-accessed` + `review-after` fields. Added typed relations convention (inline `#causes`/`#refines`/`#contradicts`/etc.) — retrofit applied to evergreen-notes + obsidian-claude-patterns
- **2026-04-13** (append, via update-knowledge) — เพิ่ม POC implementation notes ใน [[items/obsidian-claude-patterns]]: confirmed winners / deferred / rejected patterns + lessons learned. Relevance section ปรับเป็น concrete status. AI consolidated take ปรับข้อ 3 ให้สะท้อนว่า guardrails ข้อ 2-3 (weekly diff review, critical facts) ยัง defer
- **2026-04-13** (initial) — 5 items, outline + index + consolidated opinion. Source: research-outline + research-deep demo

## Open threads
- ดูว่า `knowledge/` vs `projects/*/10-research/` จะ cross-pollinate ยังไง — เช่น pattern ที่เจอจาก project A ย้ายเป็น evergreen note ใน `knowledge/` ได้ไหม
- Bi-temporal facts ใน PKM: under-studied — อาจคุ้มค่า revisit ใน phase ถัดไป

## Follow-up research ideas (ถ้าอยาก expand)
- [ ] Digital Gardens movement (Maggie Appleton) — public-facing variant
- [ ] Smart Notes (Sönke Ahrens, 2017) — Zettelkasten สำหรับนักวิชาการ, bridge ระหว่าง Luhmann กับ modern
- [ ] Progressive summarization technique (Forte) รายละเอียด

## AI's overall take #opinion

> [!opinion] Consolidated judgment (not fact — flagged for reader filtering)
> 1. **Methodology ไม่ใช่ bottleneck** — คนล้มเหลวเพราะ anti-patterns (ดู [[items/anti-patterns]]) มากกว่าเลือก method ผิด. เริ่มเลยด้วย evergreen-style แล้ว iterate — อย่ารออ่าน book stack ก่อน
> 2. **Hybrid ชนะ pure** — PARA ที่ capture/project layer + evergreen ที่ resources/knowledge layer. POC นี้โครงสร้างได้แล้ว (projects/ = P, knowledge/ = R)
> 3. **AI-augmented layer คุ้มลงทุน** แต่ต้องมี guardrails สามข้อ: git history, weekly diff review, always-loaded critical facts. ปัจจุบัน POC มี (1) + (2)-lite ผ่าน `update-knowledge` ที่ preserve provenance + supersede callouts; ข้อ (3) `CRITICAL_FACTS.md` ยัง defer เพราะ POC ยังไม่มี ground-truth ระดับที่คุ้ม pin. จะ re-evaluate เมื่อทำโปรเจกต์จริง 1-2 ตัวและเห็น repeated facts
> 4. **Thai context**: Zettelkasten / Evergreen materials ส่วนใหญ่เป็นอังกฤษ — การเขียน note ด้วยภาษาไทย + title อังกฤษ (ผสม) เป็น compromise ใช้การได้; workflow ของ POC ใช้ pattern นี้อยู่แล้ว

## Sources (aggregate จากทุก items)
- [Zettelkasten — Wikipedia](https://en.wikipedia.org/wiki/Zettelkasten)
- [Evergreen notes — Andy Matuschak](https://notes.andymatuschak.org/Evergreen_notes)
- [Zettelkasten.de](https://zettelkasten.de/)
- [How to Build a Second Brain in Obsidian — Matt Giaro](https://mattgiaro.com/second-brain-obsidian/)
- [eugeniughelbur/obsidian-second-brain](https://github.com/eugeniughelbur/obsidian-second-brain)
- [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)
