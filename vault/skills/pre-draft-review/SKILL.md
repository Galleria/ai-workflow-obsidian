---
name: pre-draft-review
description: Analyze a SOW extract BEFORE drafting docs — identify information gaps that must be resolved to draft reliably, recommend industry/compliance additions the SOW may have missed, and produce a pre-draft question list to discuss with the client before investing in SRS/BRD/SDS. Trigger when the user asks to "pre-draft review", "review extract", "what's missing before drafting", "ตรวจก่อน draft", "มีอะไรต้องถามก่อน draft", "gap analysis on SOW".
tags:
  - skill
  - review
  - pre-draft
---

# pre-draft-review

Sits between `extract-sow` and `draft-doc`. The SOW extract often has enough to know *what the client wants* but not enough to draft a good SRS/BRD. This skill surfaces the gaps **before** drafting — so the PM can negotiate, research, or schedule a clarification meeting first instead of writing on assumptions and re-working later.

## How it's different from `qa-review`

| | `pre-draft-review` | `qa-review` |
|---|---|---|
| Input | `extracted.md` (from SOW) | drafted doc (SRS/BRD/SDS) |
| Timing | before drafting | after drafting |
| Question types | "info missing to draft" | "info to confirm in draft" |
| Can invoke research | yes (may call `research-outline` for industry context) | no (reviews only what's drafted) |
| Output | pre-draft question list | client-facing QA questions + risk register |
| Primary audience | PM + BA before client meeting | client reviewer |

Both exist because they serve genuinely different concerns at different workflow steps.

## Inputs

- **Project name** — `projects/<name>/`
- **Source extract** — defaults to `projects/<name>/_input/extracted.md` (path-agnostic — falls back to any `extracted.md` under the project root)
- **Research mode** (optional) — `none` (default, fast) | `shallow` (single research-outline pass on 2-3 topics) | `deep` (invoke research-deep for industry benchmarks)

## Process

1. **Load extracted.md** from the project
2. **Load `project-config.md`** for language + standard level
3. **Classify every gap, ambiguity, or over-brief section** in the extract into one of these buckets:
   - **Blocker** — cannot draft without this (e.g., no FR list → no SRS §3.2; no tier rules → no tier FR)
   - **Assumption risk** — can draft by assuming, but wrong assumption = large rework
   - **Industry/compliance gap** — SOW didn't mention something the client's domain/regulation usually requires (PDPA consent flow, SOC2 logging, HIPAA, etc.)
   - **Scope boundary** — phrasing unclear if something is in or out
   - **Technical feasibility** — something in SOW may be infeasible or require major change (e.g., "realtime 1M concurrent" on a fixed-price budget)
   - **Stakeholder / governance** — missing owner, approver, change-control path
4. **Optional research pass** (if `research-mode != none`):
   - Pick 2-3 topics to research based on domain (identified from extracted §1 + §5)
   - Examples: "loyalty platform common NFRs", "PDPA consent flow requirements", "Oracle XStore integration gotchas"
   - Invoke `research-outline` per topic → outline lands in `projects/<name>/research/<topic>/`
   - Use findings to feed the "Suggested additions" section of the output
5. **Generate the output file** (see structure below)
6. **Save** to `projects/<name>/qa/pre-draft-review-<YYYY-MM-DD>.md`
7. **Report** summary

## Output structure

```markdown
---
type: pre-draft-review
review-of: "[[extracted]]"
reviewed-at: YYYY-MM-DD
research-mode: none | shallow | deep
tags:
  - <project-slug>
  - pre-draft-review
---

# Pre-draft review — <project>

> Purpose: resolve gaps **before** drafting SRS/BRD/SDS. Answers to Blocker questions drive the timeline to M1 sign-off.

## 1. Executive summary
- Readiness to draft: **X/10**  (reasoning in one sentence)
- Blocker questions: **N** (see §3)
- Recommended prep: [research / workshop / email confirmation / ready to draft]

## 2. Gap heatmap

| Bucket | Count | Severity |
|---|---|---|
| Blocker | | H |
| Assumption risk | | M |
| Industry / compliance gap | | M |
| Scope boundary | | M |
| Technical feasibility | | M |
| Stakeholder / governance | | L |

## 3. Blocker questions (ถามลูกค้าก่อน draft)
> ตอบครบ = draft ได้เลย; ไม่ครบ = ต้อง assume + re-work

- [ ] **Q1:** ... (ref: extracted §X)
  - **Why blocker:** without this, cannot draft SRS §Y
  - **Suggested answer if we must assume:** ...
- [ ] **Q2:** ...

## 4. Assumption risks (เราสามารถ draft ได้ แต่ลูกค้าควร confirm)
- [ ] **A1:** เราจะ assume X = Y (ตาม industry default / common client practice). Confirm?
  - **Risk if wrong:** medium — requires re-work in section Z
- [ ] **A2:** ...

## 5. Suggested additions (สิ่งที่ SOW ไม่ได้พูด แต่ domain ใกล้เคียงมักมี)

> หากเปิด research mode = shallow/deep, รายการเหล่านี้มี citation จาก `projects/<name>/research/...`

### 5.1 Compliance / legal
- **PDPA consent flow** — SOW mention PDPA แต่ไม่ระบุ flow การขอ consent + right-to-be-forgotten (`#suggested`)
- ...

### 5.2 NFR ที่มักขาด
- **Observability:** logging standard, tracing, alerting — ไม่อยู่ใน SOW แต่ dev + ops จะต้องมี
- **Backup/DR:** RPO/RTO ไม่ได้ระบุ
- ...

### 5.3 Architecture / security
- **Secrets management:** SOW บอก "secure" แต่ไม่ระบุ mechanism
- ...

## 6. Scope boundary clarifications
- [ ] **B1:** "X" อยู่ใน scope หรือไม่ — phrasing ใน SOW §Y ไม่ชัด (อ้างใน [[extracted#...]])
- [ ] ...

## 7. Technical feasibility flags (internal — review with tech lead before raising with client)
- [ ] **T1:** "50 tps with 500k members on fixed-price 2.4M THB" — feasibility check: likely OK with <stack>; risk if requires <other stack>
- [ ] ...

## 8. Stakeholder / governance gaps
- [ ] **S1:** ไม่มี Ops/DevOps representative จากฝั่งลูกค้า — ใครจะเซ็น runbook?
- [ ] ...

## 9. Recommended next step

Pick one based on gap count:
- **0-2 Blockers, ≤ 3 Assumption risks** → ready to draft; send Assumption risks by email, start SRS in parallel
- **3-5 Blockers** → schedule 1-hour requirement workshop; draft after MoM
- **>5 Blockers** → SOW materially under-specified — discuss with PM whether CR is needed before continuing

### Suggested agenda for next meeting (if needed)
1. Review Blocker questions §3 (allot 30 min)
2. Confirm Assumption risks §4 (allot 15 min)
3. Discuss Suggested additions §5 — which apply? (allot 15 min)

## 10. Research references
> Only populated if research-mode != none

- [[research/<topic-1>/outline]] — <1-liner>
- [[research/<topic-2>/outline]] — <1-liner>

---

## Next steps (for PM)

1. Forward §3 (Blockers) + §6 (Scope boundaries) to client — blocker answers needed before SRS drafting
2. Forward §4 (Assumption risks) — can accompany draft; lower urgency
3. Internal review §7 (Technical feasibility) with tech lead before exposing to client
4. After answers received → run `update-doc` on `extracted.md` (MoM → updated extract) → run `draft-doc`
```

## Research integration

If `research-mode = shallow` or `deep`:
- Identify 2-3 topics from the extract: domain ("loyalty platform"), integrations ("Oracle XStore"), compliance ("PDPA"), pattern match ("OTP auth" → can reference existing `patterns/auth-otp-flow.md`)
- For each, invoke `research-outline` skill
- Check `patterns/` library FIRST — if a pattern already exists for the topic, cite it and skip the fresh research
- Findings inform §5 (Suggested additions) with citations

If `research-mode = none`:
- Skip external research
- Rely on `patterns/` library + standard industry defaults baked into the skill

## Rules

- **Don't draft the SRS here.** This skill identifies gaps; it doesn't fill them. Filling gaps happens in `draft-doc` after answers arrive.
- **Distinguish internal vs client-facing.** §7 (Technical feasibility) is internal; §3-§6 are PM-to-client question sources. Be explicit in the output.
- **Language:** follow project-config (default Thai). Client-facing sections in Thai; internal in Thai too, but technical terms can stay English.
- **Every question cites the extract reference** — so PM can point client to "§5 FR-004 says X; we need Y" without hunting.
- **Prioritize Blockers ruthlessly.** A question is only a Blocker if drafting stalls without it. Everything else is Assumption risk (draftable + confirmable).
- **Check `patterns/` library** before writing §5 additions — if `patterns/auth-otp-flow.md` already exists, cite it instead of restating.
- **Respect pattern-compliance inheritance** — if SOW mentions PDPA/HIPAA/etc., loading that standard's compliance checklist into §5 is mandatory.

## Common scenarios

| SOW situation | Expected gaps |
|---|---|
| Greenfield platform SOW, short on NFRs | Many §5 Suggested additions (perf targets, SLA, observability) |
| Integration-heavy SOW with terse API mentions | Many §3 Blockers (auth method, idempotency, retry policy per integration) |
| "Migrate from X to Y" SOW | §3 Blockers on data profile (quality, volume, schema mismatch) |
| Thin SOW attached to verbal agreement | High blocker count — recommend requirement workshop |
| Well-specified SOW with full NFR table | Few gaps — mostly §4 Assumption confirmations + §5 compliance additions |

## Report format

```
Pre-draft review: projects/<name>/qa/pre-draft-review-<date>.md
Source: projects/<name>/_input/extracted.md
Readiness to draft: 6/10
Blockers: 4 | Assumption risks: 7 | Industry additions: 5
Recommended next: schedule 1-hour requirement workshop
Research mode: shallow (2 topics)  ← if invoked
Next: forward §3 + §6 to client; internal review §7 with tech lead
```

## Output conventions (Obsidian)

Follow [[obsidian-markdown]] conventions:
- Blockers use `> [!blocker]` or `> [!question]+` callouts (depending on vault theme)
- Assumption risks use `> [!warning]` callouts
- Suggested additions use `> [!tip]` callouts
- All items carry inline tag `#pre-draft-review` + severity (`#blocker`, `#assumption`, `#suggestion`) for Dataview
- §5 items that come from research use wikilinks to the research note
- §3 items resolved later → tag `#resolved-v<x>` and link to the MoM or email
