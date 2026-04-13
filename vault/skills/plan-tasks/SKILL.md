---
name: plan-tasks
description: Decompose an SRS / SDS into a development task backlog at per-module, per-function granularity — with estimates, dependencies, and a sequenced timeline (Gantt). Complements the high-level WBS. Trigger when the user asks to "break down tasks", "decompose into dev tasks", "development plan", "module tasks", "sprint plan", "แตก task", "ทำ timeline dev", "plan sprint for <project>".
---

# plan-tasks

Bridges the gap between **WBS (PM-level work packages)** and **Jira/GitLab backlog (developer-level tickets)**.

Input: SRS (+ optional SDS). Output: a markdown task backlog that the tech lead can drop into Jira / GitLab Issues / Linear, plus a dependency graph and Gantt.

## ⚠ Precondition — run only after client sign-off on the SRS

`plan-tasks` consumes the SRS as **frozen input**. If the SRS changes, the dev plan has to be regenerated. Running too early wastes effort.

**Before running, verify one of:**
1. SRS is signed off (client confirmed via email / meeting — look for `signed-off: true` or a sign-off note in `project-config.md`), OR
2. The user explicitly says "provisional" / "for internal sizing" / "pre-signoff estimate" — in that case, **label the output file** `dev-tasks-provisional-<date>.md` and **warn in the report** that it must be re-run after sign-off.

If neither is true, **ask the user** before proceeding:
> "SRS sign-off status unclear. Is this dev plan for (a) post-signoff implementation, or (b) a provisional internal estimate? The output will be labeled accordingly."

The typical order for a new project is: `extract-sow` → `draft-doc` → `make-diagram` → `qa-review` → `export-doc` → **[client sign-off]** → `plan-tasks`.

## Inputs
- **Project name** — `projects/<name>/`
- **Source doc** — defaults to `20-srs/srs.md`; also reads `30-sds/sds.md` if present
- **Team capacity** (optional) — e.g. "2 backend devs, 2 mobile devs, 1 QA, 6-month window"
- **Iteration length** (optional) — default 2 weeks

## Process

1. **Load** SRS. Pull:
   - Modules from §2.2 Product Functions (F1, F2, ...)
   - FRs from §3.2 (FR-001, FR-002, ...)
   - NFRs from §3.3 — map to cross-cutting tasks (perf, security, observability)
   - Design constraints from §3.4 — map to setup tasks
2. **Load** SDS if present (for component boundaries, stack decisions, ADRs → influence task count)
3. **Load** `project-config.md` → language, capacity if specified there
4. **Decompose** each FR into the standard task pattern (see below)
5. **Estimate** each task using t-shirt sizing (XS/S/M/L/XL) + rough hours
6. **Identify dependencies** — both inter-FR (FR-B needs FR-A) and intra-FR (impl before test)
7. **Sequence** — critical-path-aware ordering + parallel lanes per role
8. **Generate outputs:**
   - Task backlog table (primary deliverable)
   - Dependency DAG (Mermaid)
   - Gantt sequenced against team capacity (Mermaid)
   - Sprint/iteration plan if iteration length given
   - Risk callouts for under-specified FRs
9. **Save** to `projects/<name>/60-wbs/`:
   - Post-signoff: `dev-tasks.md`
   - Provisional: `dev-tasks-provisional-<YYYYMMDD>.md` + header banner "⚠ PROVISIONAL — based on unsigned SRS v<ver>, re-run after sign-off"
10. **Report** summary (note provisional status if applicable)

## Standard task decomposition pattern (per FR)

For each functional requirement, expand into these task types — omit lines that don't apply:

| Task type | Code | Who | When |
|---|---|---|---|
| Design (API contract, UI, DB schema delta) | DSG | Tech Lead / SA | before impl |
| Backend impl | BE | Backend dev | after DSG |
| Frontend / Mobile impl | FE / MO | Frontend / Mobile dev | after DSG (parallel with BE if contract locked) |
| Integration (external API/webhook) | INT | Backend dev | after BE |
| Unit + integration tests | TST | Dev (same) | with impl |
| E2E test | E2E | QA | after INT |
| Docs (API, user, ops) | DOC | Dev / Tech Writer | with impl |
| Code review | CR | Peer | before merge |
| Deploy / config | OPS | DevOps | before E2E on staging |

**Estimation guide (hours):**
- XS: 1-4h (config tweak, tiny fix)
- S: 1-2 days
- M: 3-5 days
- L: 1-2 weeks
- XL: >2 weeks — **must split further** (flag to tech lead)

## Output structure — `dev-tasks.md`

```markdown
---
type: dev-tasks
project: <name>
source: 20-srs/srs.md (+ 30-sds/sds.md if present)
generated: YYYY-MM-DD
iteration: 2 weeks
capacity: <team summary>
---

# Development task backlog — <project>

## Summary
- FRs decomposed: N
- Total tasks: M
- Estimated effort: X person-weeks
- Critical path: Y weeks
- Recommended iteration count: Z

## Module / FR → task table

### Module F1 — Customer registration (FR-001)

| # | Task | Type | Role | Size | Est. (h) | Depends on | Notes |
|---|---|---|---|---|---:|---|---|
| T1.1 | Design OTP API contract + DB schema (member, identity, consent tables) | DSG | TL | S | 12 | — | Ties to FR-001, patterns/auth-otp-flow |
| T1.2 | Implement OTP request endpoint | BE | BE1 | S | 12 | T1.1 | Redis for code storage |
| T1.3 | Implement OTP verify endpoint + rate limit | BE | BE1 | S | 10 | T1.2 | constant-time compare |
| T1.4 | SMS gateway integration | INT | BE1 | S | 8 | T1.2 | depends on provider choice (see QA Q5) |
| T1.5 | Email transactional send integration | INT | BE2 | XS | 4 | T1.2 | |
| T1.6 | Mobile UI: registration screen + OTP input | MO | MO1 | S | 12 | T1.1 | two-field OTP |
| T1.7 | Unit tests (backend) | TST | BE1 | XS | 4 | T1.2, T1.3 | |
| T1.8 | E2E: new user happy path + invalid OTP path | E2E | QA | XS | 6 | T1.3, T1.6, T1.4, T1.5 | |
| T1.9 | Docs: API reference + registration user flow | DOC | BE1 | XS | 3 | T1.3 | |

### Module F2 — Points accrual (FR-002, FR-007)
...

### Cross-cutting (NFR-driven)

| # | Task | Type | Role | Size | Est. (h) | Notes |
|---|---|---|---|---|---:|---|
| X1 | AWS + GitLab CI/CD baseline setup | OPS | DevOps | M | 40 | Foundation; blocks all deploys |
| X2 | Observability stack (metrics, logs, tracing, alerts) | OPS | DevOps | M | 40 | Ties to NFR Availability |
| X3 | Load test harness (500k members, 50 tps scenarios) | OPS | QA+DevOps | M | 32 | Ties to NFR Performance |
| X4 | Security baseline (WAF, secrets manager, OWASP scan gate) | OPS | DevOps | M | 32 | |
| X5 | PDPA compliance: consent flow, retention, right-to-be-forgotten | BE+TL | TL+BE2 | M | 32 | |

## Dependency graph (DAG)

\`\`\`mermaid
flowchart LR
  X1[X1 CI/CD] --> T1.1
  T1.1 --> T1.2
  T1.1 --> T1.6
  T1.2 --> T1.3
  T1.2 --> T1.4
  T1.2 --> T1.5
  T1.3 --> T1.8
  T1.4 --> T1.8
  T1.5 --> T1.8
  T1.6 --> T1.8
  X2[X2 Observability] -.-> T1.3
\`\`\`

## Sequenced timeline (Gantt)

\`\`\`mermaid
gantt
  title Dev timeline (T-shirt estimate, assumes given capacity)
  dateFormat YYYY-MM-DD
  axisFormat %b-%d

  section Foundation (OPS)
  CI/CD baseline         :x1, 2026-05-15, 5d
  Observability stack    :x2, after x1, 5d

  section F1 Registration
  Design OTP contract    :t11, after x1, 2d
  BE: OTP request        :t12, after t11, 2d
  BE: OTP verify         :t13, after t12, 2d
  INT: SMS gateway       :t14, after t12, 1d
  INT: Email send        :t15, after t12, 1d
  MO: registration UI    :t16, after t11, 2d
  E2E                    :t18, after t16 t13 t14 t15, 1d
\`\`\`

## Sprint / iteration plan (if iteration length given)

### Sprint 1 (2026-05-15 → 2026-05-28) — Foundation
- X1 CI/CD baseline [DevOps]
- X2 Observability stack kickoff [DevOps]
- T1.1 Design OTP contract [TL]
- ...

### Sprint 2 ...

## Open risks from decomposition

| # | FR | Risk | Recommended action |
|---|---|---|---|
| R1 | FR-004 Tier logic | Criteria unknown — can't estimate; assumed S, could balloon to M | Resolve via client Q1 before sprint 2 planning |
| R2 | FR-012 Notification channels | Push provider not chosen — INT tasks are placeholders | Resolve via client Q5 |

## Assumptions (for estimates)
- Team capacity: <as provided>
- Stack confirmed in SDS (otherwise add "stack selection" task)
- No external vendor handoffs beyond SOW-specified integrations

## Next
- Review with tech lead — re-size where experience contradicts generic estimates
- Import into Jira/GitLab (one ticket per row; labels = module, role, size)
- Re-run after SRS updates (regenerate `dev-tasks.md`; diff to see delta)
```

## Rules

- **Every task traceable to at least one FR or NFR** — no orphan work. If the SRS doesn't justify it, don't invent it.
- **Flag XL tasks as MUST-SPLIT** — decompose further before running again.
- **Include cross-cutting NFR tasks explicitly** — they're the ones everyone forgets.
- **Don't over-specify** — this is a planning artifact, not a design spec. If a task needs design work, it gets a DSG task (T1.1 pattern).
- **Language:** follow project-config; default Thai for notes, but task codes and type labels in English for tooling compatibility.
- **Don't calendar-schedule tasks unless capacity + start date are given** — otherwise leave sequence only (no dates).
- **Critical path:** identify explicitly in summary so PM knows the slippage-sensitive chain.

## When to re-run

- SRS/SDS revised materially → regenerate, diff against previous `dev-tasks.md`
- Capacity changes (team scaled up/down) → regenerate with new Gantt only
- Sprint cadence changes → regenerate sprint allocation only

## Report format

```
Generated: projects/<name>/60-wbs/dev-tasks.md
FRs: 12 | NFRs mapped to 5 cross-cutting tasks
Tasks: 87 total (68 feature, 14 ops, 5 cross-cutting)
Estimated effort: 42 person-weeks
Critical path: 18 weeks (FR-001 → FR-002 → FR-007 → integration tests → UAT)
Flags: 0 XL tasks (all split already); 2 risks from under-specified FRs
Next: tech lead review, then import to Jira
```
