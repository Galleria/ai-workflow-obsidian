---
name: ask-knowledge
user-invocable: true
allowed-tools: Read, Glob, Grep, WebSearch, AskUserQuestion
description: Answer a question using the knowledge base (second brain). Searches topic catalogs, reads relevant indexes and items, synthesizes answer with citations and aggregate confidence. Trigger when the user asks a factual question that might be answered from stored knowledge, or says "ask the knowledge base", "what do I know about X", "ถามเรื่อง X จาก second brain", "ค้น knowledge", "/ask".
---

# ask-knowledge

Queries the `knowledge/` layer (second brain). Synthesizes an answer from existing notes with citations + aggregated confidence; if the knowledge base doesn't cover it, offers to research.

Counterpart to `research-outline`/`research-deep` (which *create* knowledge) and `update-knowledge` (which *revises* it). This skill only *reads*.

## Inputs
- **Question** — freeform text
- **Depth** (optional) — `quick` (index-only, fast) | `deep` (index + relevant items, default) | `exhaustive` (all items in matching topics)

## Process

1. **Clarify** via `AskUserQuestion` only if the question is ambiguous enough that multiple topics could apply — otherwise proceed
2. **Read catalog** — `knowledge/README.md` (always). Pick up to 3 candidate topics based on keywords + typed-graph hints
3. **Read index.md** for each candidate (parallel, cheap)
4. **Decide depth**:
   - Answer visible in index synthesis → skip item reads; cite index + relevant item links
   - Answer needs specific angle → read just that item (max 3 items in `deep` mode)
   - User asked `exhaustive` → read all items in matching topics
5. **Touch `last-accessed: <today>`** on every item/index actually read — Edit the frontmatter line
6. **Synthesize** per the output structure below
7. **If knowledge base doesn't cover it** — respond with "No matching topic; closest is [[X]] (confidence 0.?)" + offer: "run `/research <topic>` to create it"

## Output structure

```markdown
## Answer
<2-5 sentences, direct. If the question is a yes/no or factual, lead with the answer>

## Reasoning / details
<expand as needed — bullets or short paragraphs. Every non-trivial claim cites an item>

## Citations
- [[<topic>/index|<Topic> TL;DR]] — confidence X.X
- [[<topic>/items/<slug>|<Item>]] — confidence X.X
- ...

## Aggregate confidence: **X.X** (min of cited items' confidence; drops to "low" if any item has #contradiction)

## Gaps (if any)
- Not covered in current knowledge: <what>
- Partially covered but #uncertain: <what>
- Want more depth? Run `/research-deep` or `/update-knowledge`

## AI take (optional) #opinion
> [!opinion]
> Short judgment beyond the cited facts. Flag as opinion clearly.
```

## Rules

- **Citations mandatory** — every non-trivial claim in "Reasoning" links to a specific item or index. No uncited assertions.
- **Confidence propagates** — answer confidence = min of cited items' confidence (conservative). If any cited item is `#contradiction`-tagged, cap at 0.5 and flag.
- **Respect visibility** — if any candidate topic is `private` or `internal`, preserve that sensitivity; don't mix with public output unless user asked.
- **Don't re-research silently** — if gaps exist, surface them and offer `/research-deep`. Don't WebSearch to fill gaps inline (that's a `research` concern, not `ask`).
- **Stale-note warning** — if a cited item's `review-after` is past-due, add: "⚠️ note stale since <date>; may be out of date"
- **Update `last-accessed`** on everything read (supports forgetting-curve — fresh access resets staleness)
- **Token discipline** — read catalog + indexes first; items only when needed. For `quick` mode, never read items

## Escalation paths

| Situation | Skill to call |
|---|---|
| Topic doesn't exist | `research-outline` → `research-deep` |
| Topic exists but source is stale / need fresh info | `update-knowledge` (refresh or merge mode) |
| Contradiction surfaced | `update-knowledge` (merge with contradiction classification) |
| Question is about a project (not general knowledge) | read `projects/<name>/` instead; this skill is for `knowledge/` only |

## Report format

```
Asked: "<question summary>"
Mode: deep
Topics consulted: living-knowledge-base (conf 0.8)
Items read: 3 (obsidian-claude-patterns, llm-wiki-v2, anti-patterns)
Aggregate confidence: 0.65 (pulled down by llm-wiki-v2 at 0.55)
Gaps flagged: 1 (primary-source verification for LLM Wiki v2)
Last-accessed touched: 4 notes
```

## Output conventions

Follow [[obsidian-markdown]] — wikilinks for citations, `> [!opinion]` for AI judgment, `#uncertain` / `#contradiction` tags preserved from source items.
