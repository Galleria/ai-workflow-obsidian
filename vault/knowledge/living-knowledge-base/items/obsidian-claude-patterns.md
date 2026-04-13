---
type: research-item
topic: Living knowledge base
item: Obsidian + Claude patterns (AI-as-writer vaults)
category: modern-tooling
parent: "[[outline]]"
researched: 2026-04-13
tags:
  - research
  - research/deep
  - living-knowledge-base
  - tooling
---

# Obsidian + Claude patterns

> [!summary]
> Generation ใหม่ของ "living knowledge base" ที่ Claude (หรือ LLM) เป็น primary reader/writer — vault กลายเป็น database ที่ agent operate บน. แตกต่างจาก human-only vault เพราะ ingestion สามารถ rewrite หลายหน้าพร้อมกัน และมี scheduled background ops

## Core idea
Vault หยุด static. Claude เห็น vault เป็น workspace:
- ingest source ใหม่ → **rewrite 5-15 หน้าที่เกี่ยวข้อง** (ไม่ใช่แค่ append)
- resolve contradictions ข้ามหน้าอัตโนมัติ
- scheduled agents (morning/nightly/weekly) ทำ maintenance: daily notes, orphan linking, review prompts

## Key principles
- **`_CLAUDE.md` operating manual** — vault-root file บอก Claude ว่า vault นี้ organize ยังไง, commands ทำอะไร
- **Always-loaded `CRITICAL_FACTS.md`** — ~120 tokens pinned เสมอ (user identity, current projects, hard constraints) — trick ที่ Karpathy promote [^3]
- **`index.md` catalog** — หน้าที่ Claude อ่านก่อนเสมอ เพื่อรู้ว่าใน vault มีอะไรบ้าง
- **Bi-temporal facts** — track ทั้ง "เมื่อไหร่ข้อมูลนี้จริง" และ "เมื่อไหร่ vault รู้" → audit trail
- **Immutable `raw/`** — เก็บ source ต้นฉบับแยกจาก synthesized notes

## Tools / examples
- [eugeniughelbur/obsidian-second-brain](https://github.com/eugeniughelbur/obsidian-second-brain) — Claude Code skill, reference implementation [^1]
- [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) — official Obsidian author's skills (markdown, bases, canvas) [^2]
- [Weizhena/Deep-Research-skills](https://github.com/Weizhena/Deep-Research-skills) — structured research workflow
- แนวนี้กำลัง mainstream เร็วในปี 2025-2026 เพราะ Claude Code's Skill format + Agent SDK

## Strengths & trade-offs
- ✅ **Compounds automatically** — morning/nightly agents ทำให้ vault healthy โดยไม่ต้อง manual review
- ✅ **Multi-modal ingestion** — URL/PDF/audio/image เข้าได้ทันที
- ⚠️ **Trust boundary** — Claude อาจ rewrite ผิด; ต้องมี git + review gate
- ⚠️ **Cost** — scheduled agents = API usage ต่อเนื่อง; ไม่ฟรี
- ⚠️ **Less tangible ownership** — user อาจรู้สึกว่า vault "ไม่ใช่ของตัวเอง" ถ้า AI เขียนเยอะเกิน

## Relation to foundations
- แนวคิด atomic + linked notes จาก [[items/zettelkasten]] ยังใช้
- Compound-over-time จาก [[items/evergreen-notes]] ยังจริง (และเร่งได้)
- Workflow ของ [[items/second-brain-para]] ถูก automate (capture → organize → distill ทำโดย agent)

> [!info] Update 2026-04-13 — POC implementation notes (real-world experience)

เราทำ POC นี้เองตามแนวทาง AI-augmented vault ด้านบน. Pattern ที่ **ยืนยันว่าคุ้มทำ** กับ pattern ที่ **ตัดทิ้งโดยเจตนา**:

**✅ Confirmed winners (นำมาใช้ใน POC):**
- **`_CLAUDE.md` at vault root** — operating manual + read-order rules. ช่วย token discipline ชัดเจน — Claude ไม่ scan ทั้ง directory เพื่อหา context อีกต่อไป
- **Topic-level `index.md`** (per `knowledge/<topic>/`) — TL;DR + cross-cutting synthesis = cheap entry point. Items โหลด on-demand
- **`> [!opinion]` callout convention** — แยก AI judgment จาก fact ชัดเจน, tag `#opinion` เพื่อ filter ได้. คุ้มมากเพราะ reader trust เพิ่ม
- **Immutable `_raw/` folder** — provenance trail; markitdown conversion เก็บต้นฉบับเพื่อ replay ได้
- **Markitdown integration** (Microsoft, MIT) — multi-format → markdown ใน 1 คำสั่ง. ติดตั้ง 25 MB, รองรับ DOCX/PPTX/XLSX/HTML/EPub/YouTube
- **Visibility scheme** (`private|internal|public` ในแต่ละ note) — ป้องกัน client-confidential leak เข้า public repo

**⚠️ Deferred / not yet implemented:**
- **Always-loaded `CRITICAL_FACTS.md`** (~120 tokens ของ ground-truth) — ประโยชน์ชัดแต่ยังไม่มี concrete critical facts ระดับ POC นี้
- **Scheduled agents** (morning/nightly/weekly) — cost ยังไม่ justify; ทำ manual ก่อน
- **Bi-temporal facts tracking** — overkill สำหรับ POC; ใช้ `Update YYYY-MM-DD` stamps + `superseded` callouts ใน `update-knowledge` แทน (bi-temporal-lite)
- **Thinking tools** (`/challenge`, `/emerge`, `/connect`) — interesting แต่ค่อย add เมื่อ vault โตพอที่จะ benefit

**❌ Actively rejected:**
- **Rename `knowledge/` → `wiki/`** — ไม่เพิ่มค่า, แค่ rename churn
- **AI rewrites หลายหน้าพร้อมกันอัตโนมัติ** (obsidian-ingest style) — trust boundary ใหญ่เกินไปตอนนี้; เลือกทำ `update-knowledge` skill ที่ explicit + scoped แทน (1 source → 1 update, ต้อง user approve mode: append/merge/refresh)

**Lesson learned ที่ write-up ไม่ค่อยพูด:**
- การเขียน `_CLAUDE.md` ให้ดีใช้เวลาน้อยกว่าที่คิด (~20 นาที) แต่ ROI สูงมากเพราะลด re-explanation ทุก session
- Opinion convention เพิ่ม cognitive load ตอนเขียน (ต้อง articulate judgment ชัด) แต่ pay off ตอน revisit เพราะ future-self อ่านเข้าใจว่า "อันนี้ฉันเดา" vs. "อันนี้มี source"
- Markitdown **PDF ภาษาไทยยังไม่ได้ทดสอบ** — ต้องระวัง; fallback ใช้ Claude's Read tool ซึ่งจัดการภาษาไทยได้อยู่แล้ว

## Sources
[^1]: [eugeniughelbur/obsidian-second-brain README](https://github.com/eugeniughelbur/obsidian-second-brain) — accessed 2026-04-13
[^2]: [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)
[^3]: ทำตาม Andrej Karpathy — Everyday with Captain (YouTube: egBoq66lCRc) — pinned-context trick

> [!opinion] AI take #opinion
> แนวนี้เป็น **real step-change** ไม่ใช่แค่ incremental — AI agent ที่ rewrite vault ได้ทำให้ "compound" จากทฤษฎีกลายเป็นจริงได้เร็วขึ้นมาก (user ไม่ต้องจำว่า note ไหนควร update). แต่ bet ที่ต้องวางคือ **trust**: ถ้า agent เขียนผิดแล้วไม่รู้ตัว vault จะเน่าเงียบๆ. Mitigation ที่จำเป็น 3 อย่าง — (1) git commit ทุก agent run, (2) diff review อย่างน้อยสัปดาห์ละครั้ง, (3) always-loaded `CRITICAL_FACTS.md` เพื่อ ground agent. POC ของเราทำข้อ 1 ได้แล้ว (vault เป็น git repo), ยังไม่มีข้อ 2-3. ถ้า move ไปแนวนี้เต็มตัว อย่าข้ามสามข้อนี้
