---
type: research-index
topic: Living knowledge base
parent: "[[outline]]"
item-count: 5
generated: 2026-04-13
visibility: public
tags:
  - research
  - research/index
  - living-knowledge-base
---

# Living knowledge base — research index

> [!info] สรุป
> "Living knowledge base" = personal knowledge system ที่ **compound ตามเวลา** (ยิ่งใส่ ยิ่งมีค่า เพราะ link/insight ใหม่โผล่เอง) ไม่ใช่แค่ที่เก็บไฟล์. มี 3 คลื่นหลักของแนวคิด + คลื่นที่ 4 กำลังมา (AI-augmented)

## TL;DR ใน 5 บรรทัด
1. **Zettelkasten (1960s, Luhmann)** — ต้นตอ: atomic notes + links + unique IDs → network งอกเอง
2. **Evergreen notes (2019+, Matuschak)** — refinement สำหรับ hypertext: concept-oriented, densely linked, own words
3. **PARA / BASB (2017+, Forte)** — workflow-centric: Projects/Areas/Resources/Archive + CODE (capture-organize-distill-express)
4. **AI-augmented vaults (2025+)** — Claude/LLM เป็น primary writer; ingest rewrite หลายหน้า; scheduled maintenance agents
5. **Anti-patterns กินคนเยอะกว่า methodology** — collector's fallacy, note graveyard, tool hopping, over-organization

## Items

| # | Item | Category | Summary | Notes |
|---|---|---|---|---|
| 1 | [[items/zettelkasten\|Zettelkasten]] | foundational-method | Luhmann's atomic-linked system — รากฐานของทุกอย่าง | — |
| 2 | [[items/evergreen-notes\|Evergreen notes]] | modern-synthesis | Matuschak: write to compound, concept-oriented | — |
| 3 | [[items/second-brain-para\|PARA / BASB]] | popular-framework | Forte: action-first, 4 folders + CODE workflow | ขัดกับ evergreen ในจุด organization |
| 4 | [[items/obsidian-claude-patterns\|Obsidian + Claude patterns]] | modern-tooling | vault ที่ Claude เป็น primary reader/writer | #new |
| 5 | [[items/anti-patterns\|Anti-patterns]] | what-not-to-do | collector's fallacy, graveyard, tool hopping | — |

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

## Relevance ต่อ POC ของเรา

POC นี้เป็น **doc-workflow partner** เป็นหลัก — จึงไม่ต้อง implement living-knowledge-base เต็มรูป. แต่ pattern ที่เอาไปใช้ได้เลย:
- ✅ `knowledge/` folder (เพิ่มใน vault แล้ว) = evergreen zone สำหรับ topic ข้ามโปรเจกต์
- ✅ Wikilinks + frontmatter tags (ผ่าน [[obsidian-markdown]]) = link density
- ⚠️ ยังไม่มี `_CLAUDE.md` / `CRITICAL_FACTS.md` vault-root — ทำได้ภายหลังถ้าอยากขยับฝั่ง second-brain (ดู [[items/obsidian-claude-patterns]])
- ❌ Scheduled agents — ยังไม่ทำ (ผู้ใช้ขอค่อยๆไปก่อน)

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
> 3. **AI-augmented layer คุ้มลงทุน** แต่ต้องมี guardrails สามข้อ: git history, weekly diff review, always-loaded critical facts. ปัจจุบัน POC มีข้อ 1 เท่านั้น — ถ้าจะขยับไปแนวนี้เต็มตัว ต้องเติมข้อ 2-3 ก่อน (ไม่ใช่หลัง)
> 4. **Thai context**: Zettelkasten / Evergreen materials ส่วนใหญ่เป็นอังกฤษ — การเขียน note ด้วยภาษาไทย + title อังกฤษ (ผสม) เป็น compromise ใช้การได้; workflow ของ POC ใช้ pattern นี้อยู่แล้ว

## Sources (aggregate จากทุก items)
- [Zettelkasten — Wikipedia](https://en.wikipedia.org/wiki/Zettelkasten)
- [Evergreen notes — Andy Matuschak](https://notes.andymatuschak.org/Evergreen_notes)
- [Zettelkasten.de](https://zettelkasten.de/)
- [How to Build a Second Brain in Obsidian — Matt Giaro](https://mattgiaro.com/second-brain-obsidian/)
- [eugeniughelbur/obsidian-second-brain](https://github.com/eugeniughelbur/obsidian-second-brain)
- [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)
