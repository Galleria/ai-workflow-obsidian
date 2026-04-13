---
standard: PMBOK Guide 7th ed. (PMI, 2021) — with 6th-ed. process artifacts still referenced
applies-to: wbs, project-plan
tags:
  - standard
  - wbs
  - project-management
aliases:
  - PMBOK
  - PMI Project Management Body of Knowledge
---

# PMBOK — WBS + Project plan reference note

Used by `draft-doc` when `project-config.standard-level = strict` and type = `wbs`. Grounds the WBS output in PMBOK terminology and structure.

PMBOK 7 shifts from process-based to **principle/performance-domain based**, but the classic artifacts (WBS, schedule, risk register, etc.) from PMBOK 6 are still the industry baseline and what clients expect to see. This note uses the practical 6th-edition artifact shapes while respecting 7th-edition principles.

## Core PMBOK artifacts a project plan should include

| Artifact | Purpose | Section in WBS template |
|---|---|---|
| **WBS** (Work Breakdown Structure) | 100% of scope decomposed into deliverables | §2 WBS Tree |
| **WBS Dictionary** | definition + acceptance criteria per package | not in current template — add inline if strict |
| **Activity list + sequencing** | activities within each package, dependencies | implicit in §4 Gantt |
| **Schedule / Gantt** | time-phased plan | §4 Gantt |
| **Resource plan** | people, skills, timing | §5 |
| **Risk register** | risk ID, prob, impact, response | §6 |
| **Cost / budget** | per phase or per package | not yet in template — add if scope demands |
| **Quality plan** | how quality is measured & reviewed | link to test plan |
| **Communication plan** | who needs what info and how often | optional |
| **Stakeholder register** | names, expectations (can reuse from BRD) | link to BRD |

## WBS rules (PMBOK 6, §5.4)

- **100% rule** — the WBS captures 100% of the scope defined by the project scope statement; work not in WBS = out of scope
- **Decomposition to work-package level** — each package is (a) schedulable, (b) estimable, (c) assignable to one owner, (d) has clear completion criteria. Stop decomposing when you hit this.
- **Mutually exclusive** — no overlap between packages; otherwise double-counting
- **Deliverable-oriented** — packages describe *what will be delivered*, not *what activity will be done* (activities belong in the activity list, one level below the WBS)
- **Numbering** — hierarchical dotted (1, 1.1, 1.1.1) with stable codes for traceability

## Enforcement rules for `draft-doc wbs` in strict mode

- Every WBS package has: ID, name, description, owner, acceptance criteria (at least 1 line), estimated effort, dependencies
- Add a **WBS Dictionary** table after the tree (one row per leaf package)
- Every SOW deliverable must map to at least one WBS package (§3 mapping table); unmapped deliverables = incomplete WBS
- Critical path identified and named in the schedule section
- Risk register: minimum 5 rows for any project >3 months, scored on Impact × Likelihood
- Assumptions + constraints sections required (PMBOK 6 §5.1.2)

## Pragmatic mode drops

OK to omit if the project is short / internal / low-ceremony:
- WBS Dictionary (leave the tree + inline notes)
- Full communication plan
- Formal cost section (if the SOW already fixes budget)
- Quality plan as separate section (reference the test plan instead)

Never drop: WBS tree ≥ 3 levels, deliverable-to-package mapping, critical-path identification, risk register, resource plan.

## PMBOK 7 performance-domain alignment (bonus context)

PMBOK 7's 8 performance domains are useful as review lenses, not as document sections:
1. Stakeholders
2. Team
3. Development approach & life cycle
4. Planning
5. Project work
6. Delivery
7. Measurement
8. Uncertainty

When reviewing a WBS, ask: does the plan credibly address each of the 8 domains, even if not as named sections?

## References
- PMI, *A Guide to the Project Management Body of Knowledge (PMBOK Guide) 7th ed.*, 2021
- PMI, *PMBOK Guide 6th ed.*, 2017 (for the classic artifact shapes)
- Related: [[babok-v3]] (stakeholder register reuse); test plan ([[ieee-829-test-plan]] if added) for quality linkage
