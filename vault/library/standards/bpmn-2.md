---
kind: standard
standard: BPMN 2.0 (ISO/IEC 19510:2013)
domain: process
applies-to: workflow, process-diagram
tags:
  - library
  - standard
  - process
  - diagram
  - workflow
aliases:
  - BPMN
  - Business Process Model and Notation
---

# BPMN 2.0 — Workflow diagram reference note

Used by `make-diagram` when `project-config.Diagram tool.workflow = drawio` (formal BPMN) or when the client specifically asks for BPMN. Mermaid flowcharts are the default for internal/informal workflows; BPMN is for contractual or regulated deliverables.

## When to use BPMN vs. a simple flowchart

| Scenario | Use |
|---|---|
| Internal dev team walkthrough of a user flow | Mermaid flowchart |
| Happy-path + 1-2 edge cases, no swimlanes needed | Mermaid flowchart |
| Client wants process docs in their BPM system | **BPMN via draw.io** |
| Regulated environment (banking, healthcare, gov) expects BPMN | **BPMN** |
| Process spans multiple organizations / systems (swimlanes matter) | **BPMN** (collaboration diagram) |
| Complex exception handling (compensation, escalation, boundary events) | **BPMN** (events give you the vocabulary) |

## BPMN element categories (minimum vocabulary a diagram must use correctly)

### Flow Objects
- **Events** (circles): Start / Intermediate / End. Triggers: None / Message / Timer / Error / Signal / Compensation / Terminate
- **Activities** (rounded rectangles): Task / Sub-process / Transaction / Call Activity. Task markers: User / Service / Manual / Send / Receive / Script / Business Rule
- **Gateways** (diamonds): Exclusive (X) / Parallel (+) / Inclusive (O) / Event-based / Complex

### Connecting Objects
- **Sequence Flow** (solid arrow) — order within a pool
- **Message Flow** (dashed arrow) — between pools (participants)
- **Association** (dotted) — attach data / artifacts

### Swimlanes
- **Pool** — a participant (organization, system). Cross-pool communication = message flows only
- **Lane** — a role / department within a pool. Cross-lane = sequence flow allowed

### Artifacts
- **Data Object** (document shape) — data flowing through
- **Group** (dashed rectangle) — visual grouping (no semantic meaning)
- **Annotation** (square bracket) — comments

## Rules / common mistakes

- **Start event in every pool** (unless pool is a black-box participant)
- **End event ends a path** — don't leave dangling flows
- **Gateway semantics matter:**
  - XOR (exclusive) = exactly one path; conditions on outgoing flows must be mutually exclusive
  - AND (parallel) = all paths must complete before merge
  - OR (inclusive) = at least one path; requires explicit merge
- **Don't use gateways as decisions without a question** — gateway label should be a question or decision variable
- **Message flows only between pools** — within one pool use sequence flow
- **Don't draw tasks inside gateways or events** — use sub-processes if detail is needed
- **Compensation** has specific notation — don't use regular backflows for undo/rollback semantics

## Enforcement rules for `make-diagram` when using BPMN

- Output file: `<slug>.drawio` (XML) — tell user they need the Diagrams/draw.io Obsidian plugin to render
- Include at least: 1 start event, 1 end event, correct gateway type for each branch, lanes if multiple roles involved
- Label every activity as a **verb + noun** ("Submit claim", "Validate data")
- Label every gateway condition on the outgoing flow, not the gateway itself
- Use **Service Task** marker for activities executed by system; **User Task** for human-driven; **Send/Receive** for external message exchange
- For long-running processes: use Intermediate Timer Event, not a looped Service Task

## Mermaid fallback (when BPMN is overkill)

If `workflow = mermaid`, use these conventions to approximate:
- `flowchart TD` with labeled nodes for activities
- `{Question?}` diamonds for XOR gateways
- `[[Sub-process]]` for call activities
- `(( ))` circles for start/end events
- Subgraphs for swimlanes
- Clearly note "BPMN-inspired; not formal BPMN" in the diagram header

## References
- OMG, *Business Process Model and Notation (BPMN) v2.0.2*, 2013 — https://www.omg.org/spec/BPMN/
- ISO/IEC 19510:2013 — identical to OMG BPMN 2.0.1
- draw.io BPMN shape library built-in
