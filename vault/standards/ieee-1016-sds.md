---
standard: IEEE Std 1016-2009 (Software Design Descriptions)
applies-to: sds
tags:
  - standard
  - sds
  - architecture
aliases:
  - IEEE 1016
  - SDS standard
---

# IEEE 1016 — SDS reference note

Used by `draft-doc` when `project-config.standard-level = strict` and type = `sds`. Also influenced by **arc42** (practical architecture template) for the narrative structure of the SDS template.

IEEE 1016 is a **viewpoint-based** standard — instead of prescribing fixed sections, it tells you which *design viewpoints* a good SDS must cover. The SDS template's sections map to these viewpoints pragmatically.

## The 10 design viewpoints (IEEE 1016 §5)

Each answers a different question; together they give a complete picture.

| # | Viewpoint | Concern | Template section |
|---|---|---|---|
| V1 | Context | What's inside/outside the system; external actors | §2.1 System context (C4 Level 1) |
| V2 | Composition | What are the modules/subsystems? | §2.2 Containers / §3 Components |
| V3 | Logical | What do the classes/entities and their relationships look like? | §4 Data design; domain model |
| V4 | Dependency | What module depends on what? | §3 per-component "Interfaces" |
| V5 | Information | What data is stored/exchanged and how? | §4 Data design; §5 API; §7 Integration |
| V6 | Patterns use | Which architectural patterns are used? | §3 "Key decisions" per component |
| V7 | Interface | External APIs: operations, contracts, errors | §5 API Design |
| V8 | Structure | Physical deployment structure | §2.4 Deployment diagram; §10 Deployment & Ops |
| V9 | Interaction | Runtime flows (sequence, collaboration) | Sequence diagrams in §3 + §5 |
| V10 | State dynamics | State machines, lifecycle | add to relevant component sections; stateDiagram-v2 |

## Enforcement rules for `draft-doc sds` in strict mode

- **Context diagram required** (V1) — system boundary explicit
- **Components ≥ 3 named with responsibilities** (V2) — catch-all "Core System" doesn't count
- **Data model with ERD** (V3, V5) — at minimum a conceptual ERD; full DD if persistence is central
- **API catalog** (V7) — list endpoints / gRPC methods / events; error model stated once
- **Deployment diagram** (V8) — target environment + topology
- **At least 1 sequence diagram** (V9) per critical flow — typically auth, main transaction, error handling
- **ADR (Architecture Decision Record) index** — non-trivial design decisions recorded (V6)
- **Each component section answers V2+V4+V6**: responsibilities, interfaces, dependencies, key decisions
- **NFR → design mapping table** (§9 in the template) — every NFR from SRS has a design response

## Pragmatic mode drops

OK to omit for lean projects:
- Full data dictionary (leave just the ERD)
- State dynamics (V10) — only add if states are genuinely complex
- Full ADR index (inline decisions are fine)
- Deployment diagram if deploy is trivial single-container

Never drop: context, components with responsibilities, interfaces, ERD, at least one sequence diagram, NFR→design mapping.

## arc42 alignment (practical companion)

Our SDS template borrows arc42's narrative order (which IEEE 1016 is agnostic about):

1. Introduction & goals
2. Constraints
3. Context & scope
4. Solution strategy
5. Building blocks (components)
6. Runtime view (sequences)
7. Deployment view
8. Cross-cutting concepts
9. Design decisions (ADRs)
10. Quality requirements (NFR → design)
11. Risks & technical debt
12. Glossary

If the client is familiar with arc42, use its §-numbering; otherwise the template's IEEE 1016-mapped sections are fine.

## References
- IEEE Std 1016-2009, *IEEE Standard for Information Technology — Systems Design — Software Design Descriptions*
- arc42 — https://arc42.org (practical architecture template)
- Simon Brown, "C4 model" — https://c4model.com (notation for context/container/component diagrams)
- Related: [[ieee-830-srs]] upstream; [[bpmn-2]] if process/workflow is architecturally central
