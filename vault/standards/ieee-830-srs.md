---
standard: IEEE 830-1998
applies-to: srs
---

# IEEE 830 — SRS reference note

Standard used by `draft-doc` when `project-config.standard level = strict` and type = `srs`.
Summarizes what each section **must** cover so the filled template isn't missing load-bearing content.

## Eight characteristics of a good SRS

1. **Correct** — every requirement is something the software actually must do
2. **Unambiguous** — each requirement has exactly one interpretation
3. **Complete** — all requirements, responses to all inputs, all references defined
4. **Consistent** — no conflicting requirements
5. **Ranked for importance/stability** — Must / Should / Could
6. **Verifiable** — testable with a finite, cost-effective process
7. **Modifiable** — structured, cross-referenced
8. **Traceable** — each requirement has a unique ID, traceable to source (SOW/BRD) and forward to tests

## Required sections (Section 5 of IEEE 830)

### 1. Introduction
- 1.1 Purpose — **who** reads this and **why**
- 1.2 Scope — product name, what it does / doesn't, benefits
- 1.3 Definitions, acronyms, abbreviations
- 1.4 References — numbered list with full citation
- 1.5 Overview — how this SRS is organized

### 2. Overall Description
- 2.1 Product perspective — system context; include diagram
- 2.2 Product functions — summary, not detailed (detail goes in §3)
- 2.3 User characteristics — education, experience, expertise
- 2.4 Constraints — regulatory, hardware, interfaces to other apps, audit, security, safety/reliability thresholds
- 2.5 Assumptions and dependencies

### 3. Specific Requirements (the bulk)
- 3.1 External interfaces: user / hardware / software / communication
- 3.2 Functional requirements — each requirement: unique ID, description, input, processing, output, pre/post-conditions, priority
- 3.3 Performance requirements (quantitative — numbers, not adjectives)
- 3.4 Design constraints — standards compliance, hardware limits
- 3.5 Software system attributes — reliability, availability, security, maintainability, portability
- 3.6 Other requirements

### 4. Supporting information
- Table of contents, index, appendices (use cases, diagrams, mockups)

## Enforcement rules for `draft-doc` in strict mode

- Every functional requirement **must** have a unique ID (`FR-NNN`) and priority
- NFRs must be **quantitative** — reject "fast", require "< 2s at P95"
- Every external interface must name **protocol + data format + error handling**
- Use cases belong in an appendix, not inline in §3
- Any section without content gets `[TBD: <specific question>]` — never delete the section

## Pragmatic mode (not strict)

Drop these if SOW doesn't support them:
- 3.1.2 Hardware Interfaces (usually N/A for web apps)
- 3.4 Design Constraints (if client has no tech mandate)
- 3.5 full breakdown (collapse into a single NFR table)

Keep §1.1-1.2, §2.1-2.2, §3.2, §3.3 — those are non-negotiable even in pragmatic mode.

## References
- IEEE Std 830-1998, *IEEE Recommended Practice for Software Requirements Specifications*
- ISO/IEC/IEEE 29148:2018 (successor standard — use if project demands it)
