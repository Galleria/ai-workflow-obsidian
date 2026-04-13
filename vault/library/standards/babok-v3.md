---
kind: standard
standard: BABOK v3 (IIBA Business Analysis Body of Knowledge, 3rd ed.)
domain: requirements
applies-to: brd
tags:
  - library
  - standard
  - requirements
  - brd
  - business-analysis
aliases:
  - BABOK
  - Business Analysis Body of Knowledge
---

# BABOK v3 — BRD reference note

Used by `draft-doc` when `project-config.standard-level = strict` and type = `brd`. Summarizes what BABOK v3 expects in a solid BRD so the filled template isn't missing load-bearing content.

BABOK itself is a **knowledge area framework**, not a document template — it defines *what* a BA should analyze. The BRD is the deliverable that synthesizes several BABOK knowledge areas for stakeholder sign-off.

## The 6 knowledge areas BABOK covers

1. **Business Analysis Planning & Monitoring** — how the BA work will be done (approach, stakeholders to engage, performance metrics)
2. **Elicitation & Collaboration** — interviews, workshops, observation; validating and communicating results
3. **Requirements Life Cycle Management** — traceability, prioritization, change, approval
4. **Strategy Analysis** — current state, future state, risks, change strategy
5. **Requirements Analysis & Design Definition** — model, specify, verify, validate, evaluate
6. **Solution Evaluation** — measure performance vs. business objectives post-implementation

A BRD pulls primarily from **Strategy Analysis (§4)** and **Requirements Analysis (§5)**.

## Required BRD content (BABOK-aligned)

| BRD section | BABOK knowledge area | Must include |
|---|---|---|
| Executive summary | Strategy Analysis | Business need, solution overview, expected outcome |
| Current state (As-is) | Strategy Analysis | Context, capabilities, pain points, constraints |
| Future state (To-be) | Strategy Analysis | Target capabilities, benefits, differentiators |
| Business objectives + KPIs | Strategy Analysis | SMART objectives, baseline + target metrics |
| Scope (in/out) | Requirements Analysis | Clear boundary; what's phase 2 vs excluded |
| Stakeholders | Planning & Monitoring | Role, influence, interest, engagement approach |
| Business requirements (BR-xxx) | Requirements Analysis | WHAT, not HOW; priority (Must/Should/Could); owner; rationale |
| Business rules | Requirements Analysis | Authoritative rules (regulatory, policy, algorithmic) — separate from BRs |
| Assumptions & constraints | Strategy Analysis | What must be true; what's fixed |
| Benefits (tangible + intangible) | Solution Evaluation | Measurable + qualitative |
| Risks | Strategy Analysis | Impact, likelihood, mitigation |
| Governance | Planning & Monitoring | Decision rights, escalation, change control |

## Quality attributes of a good business requirement (BABOK §5.4)

Every BR should be:
- **Atomic** — one requirement per statement
- **Complete** — enough to make a decision
- **Consistent** — doesn't contradict other BRs
- **Concise** — no unnecessary words
- **Feasible** — achievable within constraints
- **Unambiguous** — one interpretation
- **Testable / measurable** — verifiable
- **Prioritized** — Must / Should / Could / Won't (MoSCoW)
- **Understandable** — by non-BA readers
- **Independent** — standalone; dependencies noted explicitly
- **Traceable** — has ID, source, forward-linked to SRS/test

## Enforcement rules for `draft-doc brd` in strict mode

- Every BR has unique ID `BR-NNN`, priority, owner, rationale
- Business objectives MUST have KPI + baseline + target (no "improve X" without numbers)
- As-is and To-be both required — single-diagram "As-is | To-be" not enough; each needs a paragraph
- Stakeholder register must score **Influence × Interest** for each (standard 2x2 mental model)
- Distinguish **business rules** (authoritative constraints) from **requirements** (stakeholder asks)
- Benefits split into tangible (numbers) + intangible (descriptive)

## Pragmatic mode drops

If the BRD is short-turnaround or for internal use only, acceptable to drop:
- Full stakeholder Influence × Interest analysis (keep names + role)
- Detailed governance section (if project has a simple steering committee)
- Benefits section (can be moved inline with objectives)

Never drop: objectives with KPIs, scope boundaries, BRs with IDs+priority, risks.

## References
- IIBA, *A Guide to the Business Analysis Body of Knowledge (BABOK Guide) v3*, 2015
- Related: [[ieee-830-srs]] for the downstream SRS that inherits from BR-xxx
