# Vault Dashboard

Cross-project control tower. Requires the **Dataview** plugin (should already be enabled — see [SETUP.md](SETUP.md)).

Open this note in Obsidian; queries below render live.

---

## Projects — at a glance

```dataview
TABLE WITHOUT ID
  file.link AS "Project",
  client AS "Client",
  language AS "Lang",
  "standard-level" AS "Standard"
FROM "projects"
WHERE file.name = "project-config"
SORT file.folder ASC
```

> *Requires each `project-config.md` to have `client`, `language`, `standard-level` in frontmatter — the `_example` template has these.*

---

## Deliverables produced (all project docs)

```dataview
TABLE WITHOUT ID
  file.folder AS "Project / Folder",
  file.link AS "Doc",
  type AS "Type",
  version AS "Ver",
  file.mtime AS "Last touched"
FROM "projects"
WHERE type AND type != "sow-extract"
SORT file.mtime DESC
LIMIT 30
```

---

## Open TBDs / [ต้องยืนยัน] across all project docs

```dataview
TABLE WITHOUT ID
  file.link AS "Doc",
  length(filter(file.lists.text, (t) => contains(t, "[TBD") OR contains(t, "ต้องยืนยัน"))) AS "TBD count"
FROM "projects"
WHERE contains(file.content, "[TBD") OR contains(file.content, "ต้องยืนยัน")
SORT "TBD count" DESC
```

---

## Open client questions (from QA reviews)

Checkbox-based — unchecked = still open.

```dataview
TASK
FROM "projects"
WHERE !completed AND contains(file.path, "/qa/")
GROUP BY file.link
```

---

## QA reviews by project

```dataview
TABLE WITHOUT ID
  file.folder AS "Project",
  file.link AS "Review",
  "reviewed-at" AS "Date",
  mode AS "Mode"
FROM "projects"
WHERE contains(file.path, "/qa/")
SORT "reviewed-at" DESC
```

---

## Diagrams catalog

```dataview
TABLE WITHOUT ID
  file.folder AS "Project",
  file.link AS "Diagram",
  type AS "Type",
  tool AS "Tool"
FROM "projects"
WHERE type AND (type = "sequence" OR type = "erd" OR type = "workflow" OR type = "use-case" OR type = "component" OR type = "gantt")
SORT file.folder, type
```

---

## Standards + patterns library

```dataview
LIST
FROM "library/standards" OR "library/patterns"
SORT file.name ASC
```

---

## Knowledge base (second brain)

### Topics at a glance

```dataview
TABLE WITHOUT ID
  file.link AS "Topic",
  item-count AS "Items",
  confidence AS "Conf",
  last-updated AS "Updated",
  review-after AS "Review by"
FROM "knowledge"
WHERE type = "research-index"
SORT last-updated DESC
```

### Low-confidence items (<0.7) — worth corroborating

```dataview
TABLE WITHOUT ID
  file.link AS "Item",
  topic AS "Topic",
  confidence AS "Conf",
  last-accessed AS "Last seen"
FROM "knowledge"
WHERE type = "research-item" AND confidence < 0.7
SORT confidence ASC
```

### Stale items (review-after < today) — revisit queue

```dataview
TABLE WITHOUT ID
  file.link AS "Item",
  topic AS "Topic",
  confidence AS "Conf",
  review-after AS "Was due"
FROM "knowledge"
WHERE review-after AND review-after < date(today)
SORT review-after ASC
```

### Items with contradictions

```dataview
LIST
FROM "knowledge"
WHERE contains(tags, "contradiction") OR contains(file.content, "#contradiction")
```

### Typed relations (inline graph edges)

> Scans item notes for lines matching `[[...]] #<type> [[...]]` — surfaces the typed-graph structure without a dedicated graph engine.

```dataview
LIST
FROM "knowledge"
WHERE type = "research-item"
FLATTEN file.lists AS L
WHERE regexmatch("\[\[.+\]\] #(causes|supports|contradicts|part-of|implements|supersedes|builds-on|refines|automates|mitigates) \[\[.+\]\]", L.text)
GROUP BY file.link
```

---

## Recently modified (anywhere in vault)

```dataview
TABLE WITHOUT ID
  file.link AS "File",
  file.folder AS "Folder",
  file.mtime AS "Modified"
FROM ""
WHERE file.name != "dashboard"
SORT file.mtime DESC
LIMIT 15
```

---

## How to extend

- Add `deliverable-due::` inline field in project notes → make a "due this week" query.
- Add `risk::` frontmatter to risk entries → query open risks by impact.
- See Dataview docs: https://blacksmithgu.github.io/obsidian-dataview/
