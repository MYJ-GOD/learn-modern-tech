---
name: Knowledge Graph
description: Build and query a per-course knowledge graph linking concepts, code locations, and relationships. Loaded when /learn continue updates the graph, or /learn graph|find queries it. Supports concept lookup, code jump, relationship maps, and interview-topic aggregation.
version: 0.1.0
---

# Knowledge Graph (Per-Course)

A knowledge graph turns a finished-or-in-progress course into a queryable map:
concepts (nodes) linked by typed relationships (edges), each concept anchored to
where it was taught (lesson) and where it lives in code (demo). This powers four
retrieval scenarios:

1. **Concept location** — "Which day taught Server Components?" → day + lesson anchor
2. **Code jump** — "Where's the Day 3 route-protection code?" → `demo/…:line`
3. **Relationship map** — "What does useEffect depend on?" → edge traversal + Mermaid
4. **Interview aggregation (八股)** — "Everything about state management" → tag group + contrasts

## Storage Layout

All under the **Data Root** (`.learn/` in the project directory), never the plugin dir:

```
courses/<tech>/graph/
├── nodes.json    # concepts + definitions + anchors
├── edges.json    # typed relationships between concepts
├── index.json    # fast lookup: name/alias → id, tag → ids, code → id
└── review.json   # per-concept SM-2 state for /learn recall (ease/interval/due)
```

`review.json` is owned by `/learn recall` (schema + algorithm in
`skills/teaching-method/references/spaced-repetition.md`). The graph build steps
below never touch it.

The graph is built incrementally: each `/learn continue` appends the day's new
nodes/edges/index entries. A manual `/learn graph <tech>` can rebuild from all
`day-*.md` if the graph is missing or stale.

## Schema

### `nodes.json` — array of concept nodes

```json
[
  {
    "id": "server-components",
    "label": "Server Components",
    "aliases": ["RSC", "React Server Components"],
    "day": 2,
    "lesson_anchor": "day-2.md#server-components",
    "definition": "Components that render on the server and ship zero JS to the browser.",
    "analogy": "Like a server-rendered template, but composable like a React component.",
    "bloom": 2,
    "tags": ["rendering", "architecture"],
    "code_refs": [
      { "path": "demo/app/page.tsx", "line": 1, "note": "default RSC, async data fetch" }
    ],
    "pitfalls": ["Can't use useState/useEffect", "Can't attach event handlers"],
    "interview_qa": [
      { "q": "RSC vs Client Component?", "a": "RSC: server-only, no JS, no hooks. Client: 'use client', hooks, events." }
    ]
  }
]
```

Required per node: `id` (kebab-case, stable), `label`, `day`, `definition`, `tags`.
Optional but recommended: `aliases`, `lesson_anchor`, `code_refs`, `interview_qa`, `pitfalls`, `analogy`, `bloom`.

### `edges.json` — array of typed relationships

```json
[
  { "from": "jsx", "to": "components", "type": "prerequisite" },
  { "from": "server-components", "to": "data-fetching", "type": "enables" },
  { "from": "server-components", "to": "client-components", "type": "contrasts" }
]
```

Edge `type` vocabulary (keep to these):
- `prerequisite` — `from` must be understood before `to` (drives learning order + "what depends on X")
- `enables` — `from` makes `to` possible/practical
- `contrasts` — sibling concepts often compared (drives interview contrasts)
- `part-of` — `from` is a sub-concept of `to`
- `used-with` — commonly combined in practice

### `index.json` — precomputed lookup tables

```json
{
  "by_name": { "server components": "server-components", "rsc": "server-components" },
  "by_tag": { "rendering": ["server-components", "client-components"], "state": ["usestate", "usereducer", "context"] },
  "by_code": { "demo/app/page.tsx": ["server-components"], "demo/middleware.ts": ["route-protection"] },
  "by_day": { "1": ["routing"], "2": ["server-components", "client-components"] }
}
```

`by_name` keys are lowercased label + all aliases. Rebuild `index.json` from
`nodes.json` on every write — it is a derived cache, never the source of truth.

## Building / Updating (called from `/learn continue`)

After a lesson is confirmed complete, before updating `state.json`:

1. Ensure `courses/<tech>/graph/` exists (`mkdir -p`).
2. Read existing `nodes.json` / `edges.json` (empty arrays if absent).
3. From today's `day-N.md` + its demo, extract:
   - **Nodes**: each new concept taught today → one node. Set `day: N`,
     `lesson_anchor` to the section, `code_refs` to the demo files/lines that
     implement it (use real paths + line numbers from the demo you generated).
   - **Edges**: link today's concepts to each other and to prior-day concepts
     (prerequisite/enables/contrasts). Reuse existing node `id`s — do NOT
     duplicate a concept that already has a node; merge new `code_refs`/`interview_qa` into it.
   - **interview_qa**: 1-3 Q&A per node where natural (feeds 八股 aggregation).
4. Deduplicate by `id`. Write `nodes.json`, `edges.json`.
5. Rebuild `index.json` from the merged nodes.
6. Keep it honest: only add `code_refs` that point at code you actually produced.
   Never invent file paths or line numbers.

## Manual Build / Rebuild (`/learn graph <tech>` with no query term)

If `graph/` is missing or older than the newest `day-*.md`:
1. Read all `day-*.md` and the demo tree.
2. Rebuild nodes/edges/index from scratch using the extraction rules above.
3. Then render the full-graph view (below).

## Querying

Resolve a query term against `index.json` in this order: exact `by_name` →
alias → `by_tag` → `by_code` (path or symbol substring) → fuzzy label match.
If nothing matches, say so and list the closest 3-5 concept labels.

### Full graph view — `/learn graph <tech>`

Render a Mermaid `flowchart LR` of all nodes, edges styled by type
(prerequisite = solid arrow, contrasts = dashed). Because terminals don't render
Mermaid, ALSO print a compact text outline grouped by day:

```
Day 1: routing
Day 2: server-components → (enables) data-fetching · (contrasts) client-components
...
```

### Concept query — `/learn graph <tech> <concept>` or `/learn find <concept>`

Print a focused card for the matched concept:

```
🔎 Server Components  (Day 2 · rendering, architecture)

Definition: Components that render on the server and ship zero JS.
Analogy:    Server-rendered template, but composable like a React component.

📍 Taught in:  day-2.md#server-components
💻 Code:       demo/app/page.tsx:1  (default RSC, async data fetch)
🔗 Related:    → enables data-fetching · ⚡ contrasts client-components · prereq: components
⚠️ Pitfalls:   Can't use useState/useEffect · Can't attach event handlers
🎤 Interview:  "RSC vs Client Component?" → server-only, no JS, no hooks vs 'use client' + hooks
```

Include only the lines that have data.

### Interview / topic aggregation (八股) — `/learn find <tag>` or a topic phrase

When the term matches a **tag** (or a phrase mapping to one), aggregate ALL nodes
under it into one study sheet:

```
🎤 State Management  (3 concepts across Day 2-4)

useState      Day 2  demo/app/counter.tsx:8   — local component state
useReducer    Day 3  demo/app/cart.tsx:12      — complex state transitions
Context       Day 4  demo/app/theme.tsx:5      — cross-tree shared state

Contrasts (from edges):
  useState vs useReducer — simple vs multi-action state
  Context  vs props      — global vs explicit passing

Interview Q&A (merged):
  Q: When useReducer over useState?  A: ...
  Q: Does Context cause re-renders?  A: ...
```

## Rules

- Data Root only. `mkdir -p graph/` before writing. Never touch the plugin dir.
- `id`s are stable kebab-case; never renumber. Merge, don't duplicate.
- `code_refs` must be real. No invented paths/lines.
- `index.json` is always derived — rebuild it, don't hand-edit.
- Follow the user's language for all rendered output.
- Graph is optional-but-cheap: if `continue` can't extract cleanly, skip the
  update rather than writing garbage; a later `/learn graph` rebuild will fix it.

