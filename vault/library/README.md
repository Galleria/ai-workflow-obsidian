---
tags:
  - library
  - index
---

# Library — shared reference material

Read-only content that `skills/` draw from across all projects. If something belongs in exactly one project → it lives in `projects/<name>/`, not here. If something's a live research note that updates over time → it lives in `knowledge/`, not here.

---

## Subfolders

| Folder | What it holds | Add items when |
|---|---|---|
| [standards/](standards/) | Reference notes summarizing industry specs (IEEE, BABOK, PMBOK, BPMN, etc.) for `draft-doc` to load in strict mode | you need a new doc type in strict mode and no existing standard note covers it |
| [templates/](templates/) | Fillable skeletons per deliverable type (SRS, BRD, SDS, WBS, test-plan, MoM, …) | you start producing a new deliverable type regularly |
| [patterns/](patterns/) | Reusable design/architecture patterns (auth flows, integration shapes, UI conventions) | you see the same design chunk get rewritten across projects ≥ 2 times |

---

## Index — what's here today

### standards/ (5)
| File | Domain | For doc type |
|---|---|---|
| [babok-v3](standards/babok-v3.md) | requirements | BRD |
| [ieee-830-srs](standards/ieee-830-srs.md) | requirements | SRS |
| [ieee-1016-sds](standards/ieee-1016-sds.md) | design | SDS |
| [bpmn-2](standards/bpmn-2.md) | process | workflow / BPMN diagrams |
| [pmbok](standards/pmbok.md) | project-management | WBS / project plan |

### templates/ (5)
| File | Domain | For doc type |
|---|---|---|
| [srs-template](templates/srs-template.md) | requirements | SRS (IEEE 830-based) |
| [brd-template](templates/brd-template.md) | requirements | BRD (BABOK-based) |
| [sds-template](templates/sds-template.md) | design | SDS (IEEE 1016 + arc42) |
| [test-plan-template](templates/test-plan-template.md) | testing | Test plan / UAT |
| [wbs-template](templates/wbs-template.md) | project-management | WBS / Gantt |

### patterns/ (3)
| File | Domain | Applies to |
|---|---|---|
| [auth-otp-flow](patterns/auth-otp-flow.md) | security | registration, login, sensitive-action re-auth |
| [webhook-integration](patterns/webhook-integration.md) | integration | inbound event APIs, payment callbacks, POS webhooks |
| [crud-admin-entity](patterns/crud-admin-entity.md) | ui | admin console entity management (rules, campaigns, users) |

---

## Frontmatter conventions for library files

Every file under `library/` has:
```yaml
---
kind: standard | template | pattern       # library role
domain: requirements | design | process | project-management | testing | security | integration | ui | data | ...
applies-to: <doc type or scenario>
tags:
  - library
  - <kind>         # standard | template | pattern
  - <domain>       # requirements | design | ...
---
```

Notes:
- `kind:` distinguishes the library role (template vs standard vs pattern) without colliding with doc-type `type:` used inside template files (e.g. `type: srs`).
- `domain:` is the single source of truth for topic-based filtering in the dashboard.

Query examples:
```dataview
LIST FROM "library" WHERE domain = "security"
LIST FROM "library" WHERE kind = "template"
```

---

## Growth plan

Current state: all three subfolders are flat (≤ 5 items each). No subdivision needed yet.

**Trigger to subdivide by domain** (create `patterns/security/`, `patterns/integration/`, etc.): when any one subfolder reaches **≥ 8 items spanning ≥ 3 distinct domains**, re-plan and migrate.

`patterns/` will hit this first (many distinct domains — security, integration, UI, data, messaging, finance, …). `standards/` and `templates/` grow much more slowly (bounded by doc types + domain industries we work in).

---

## Not in library

- **Project-specific stuff** → `projects/<name>/document|diagram|_input|…`
- **Live / evergreen research notes** → `knowledge/<topic>/items/`
- **Skill instructions** → `skills/<name>/SKILL.md`
- **Brand / image assets** → `_assets/`
