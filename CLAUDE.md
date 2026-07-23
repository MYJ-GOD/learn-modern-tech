# Learn Modern Tech

This project is a universal learning framework. When the user wants to learn a technology, follow the workflow below.

## Trigger Patterns

Parse the user's intent from natural language:

| User says | Sub-command |
|-----------|-------------|
| "learn X", "学习 X", "教我 X" | **init** — initialize new course for technology X |
| "continue", "继续", "继续学习", "下一课" | **continue** — generate today's lesson |
| "continue X", "继续 X" | **continue** — switch to course X and continue |
| "review", "复习", "总结" | **review** — generate cheat sheets |
| "review X" | **review** — cheat sheets for specific course |
| "recall", "复习测试", "quiz" | **recall** — spaced repetition session |
| "recall X" | **recall** — recall session for specific course |
| "graph X", "知识图谱 X" | **graph** — build/show knowledge graph |
| "graph X Y", "概念 Y" | **graph** — focused card for concept Y |
| "find Y", "查找 Y" | **find** — query active course graph |
| "status", "进度", "dashboard" | **status** — progress dashboard |
| "help", "帮助" | **help** — command reference |

If the user says just "learn" with no tech name, show the **status** dashboard.

**Ambiguity guard**: Before init, check if `$DATA_ROOT/courses/<tech>/state.json` exists. If yes, route to `continue` instead of re-initializing.

## Data Root

All persistent data lives under:

```
DATA_ROOT = $HOME/.claude/learn        (Windows: %USERPROFILE%\.claude\learn)
```

Resolve at the start of every action:
```bash
LEARN_ROOT="${HOME:-$USERPROFILE}/.claude/learn"; mkdir -p "$LEARN_ROOT/courses" "$LEARN_ROOT/memory"
```

| Logical path | Real path |
|--------------|-----------|
| `courses/<tech>/` | `$DATA_ROOT/courses/<tech>/` |
| `courses/.active` | `$DATA_ROOT/courses/.active` |
| `memory/` | `$DATA_ROOT/memory/` |

NEVER write course/state/memory files inside the project directory.

---

## Sub-command: init (Initialize New Course)

### Step 1: Research

Research the technology comprehensively:

1. Use `mcp__context7__resolve_library_id` to get the library ID, then `mcp__context7__query_docs` for core concepts, getting started, latest changes
2. If Context7 has no results, use WebSearch + WebFetch as fallback
3. Return a compressed structured overview:
   - Technology name, current version, positioning
   - Core concept list (ordered by importance)
   - Recent breaking changes / new features
   - Ecosystem (common companion tools)
   - Suggested learning order (topological sort)

### Step 2: Collect Goals

Read all files in `$DATA_ROOT/memory/` to extract the user's known tech stack.

Then use **AskUserQuestion** for structured selection:

**Q1: What do you want to do?**
```
AskUserQuestion:
  question: "What do you want to do with <tech>?"
  header: "Goal"
  options:
    - label: "Quick overview"
      description: "Speed mode, build a minimal demo within 3 days"
    - label: "Build a complete project"
      description: "Standard mode, 7 days of systematic learning + deployable project"
    - label: "Deep mastery"
      description: "Deep mode, 14 days covering advanced features + source code"
    - label: "Interview prep"
      description: "Focus on cheat sheets + common interview questions"
  multiSelect: false
```

**Q2: How much time?**
```
AskUserQuestion:
  question: "How much time do you plan to spend learning?"
  header: "Pace"
  options:
    - label: "3 days (Speed)"
      description: "1-2 hours per day, quick start"
    - label: "7 days (Standard)"
      description: "1-2 hours per day, systematic mastery"
    - label: "14+ days (Deep)"
      description: "1-2 hours per day, comprehensive deep dive"
    - label: "Source code study"
      description: "No time limit, module-by-module source code analysis"
  multiSelect: false
```

**Q3: Background?** Generate options dynamically from the target technology's prerequisites. Always include "Complete beginner". Skip if Memory already has the user's background.

### Step 3: Generate Skeleton

Generate in `$DATA_ROOT/courses/<tech>/`:

- **`overview.md`**: Research report (from Step 1)
- **`roadmap.md`**: Mermaid flowchart + MVP demo design + daily outline
- **`state.json`**:
```json
{
  "active_course": "<tech>",
  "current_day": 1,
  "total_days": <based on pace>,
  "mode": "<speed|standard|deep|source>",
  "last_demo_path": null,
  "completed_concepts": [],
  "known_concepts": []
}
```
- **`courses/.active`**: Write current active course name
- Update `memory/`: "User started learning <tech>"

Do NOT generate lesson content. Lessons are generated on-demand.

---

## Sub-command: continue (Generate Daily Lesson)

### Step 0: State Read

1. Read `$DATA_ROOT/courses/.active` for current course (or use specified tech)
2. Read `$DATA_ROOT/courses/<tech>/state.json`
3. If no course exists → show welcome message guiding to "learn <tech>"
4. If `current_day > total_days` → course complete, suggest review
5. Determine `current_day` and `mode`

### Step 0.5: Skip Check

Before teaching, cross-check Day N's concepts against mastered concepts:
- `mastered = completed_concepts ∪ known_concepts` (this course)
- Plus concepts mastered in other courses (from memory)

If all known → name what's skipped, advance day, offer "Skip to Day N+1?"
If partially known → compress known to one-line recall checks
If nothing known → proceed normally

### Step 1: Generate Lesson

Read `skills/teaching-method/SKILL.md` for the full teaching methodology before generating.

Fetch latest docs from Context7 for today's concepts.

Generate `day-N.md` following the **Explain → Visualize → Build → Verify** pipeline:
- **Speed mode** (3 days): Mental model → Architecture diagram → Structure table → Step-by-step build → Connection diagram
- **Standard mode** (7 days): Everything in Speed + deeper "why" + exercises with acceptance criteria
- **Deep mode** (14 days): Everything in Standard + source code analysis + design patterns
- **Source mode**: Call chain analysis + architecture diagram + design patterns

Generate demo code (progressive, building on previous day).

**Iron rule**: Try to run the code first. If can't, provide verification checklist.

### Step 2: Interactive Teaching

After lesson, enter dialogue mode:
- "I don't get it" → switch analogy/angle
- "Too easy" / "skip X" → mark into `known_concepts`, move on
- "How does X relate to Y?" → use known knowledge as analogy

### Step 3: Update State

After user confirms lesson complete:
1. Update knowledge graph (read `skills/knowledge-graph/SKILL.md` for schema)
2. Update `state.json`: `current_day++`, append completed/known concepts
3. Update `memory/`: record mastery
4. If `current_day > total_days`: suggest review/recall/graph

---

## Sub-command: review (Generate Cheat Sheets)

1. Read all `day-*.md` and `overview.md` under the course
2. If `graph/nodes.json` exists, read it for interview_qa, definitions, pitfalls
3. Generate:
   - `exam/concepts.md`: Concept cheat sheets (Q&A format)
   - `exam/patterns.md`: Practical cheat sheets (code templates)
   - `exam/architecture.md`: Architecture cheat sheets
   - `exam/comparison.md`: Comparison cheat sheets (if applicable)
4. Export `exam/anki.csv` — Anki-importable flashcards:
```
#separator:Comma
#html:true
#columns:Front,Back,Tags
#tags column:3
"Question?","Answer with <code>inline code</code> and <br>line breaks.","tech day1 tag"
```
5. Update memory: "User completed <tech> course, mastery level: xxx"

---

## Sub-command: recall (Spaced Repetition — SM-2)

1. Read `graph/review.json` and `graph/nodes.json`
2. Select due concepts (`due <= today`), hardest/most-overdue first
3. Quiz each: ask from `interview_qa` or "Explain X" or fill-in-the-blank from `code_refs`
4. Grade 0-5, apply SM-2 update, write back `review.json`

SM-2 algorithm:
- Grade ≥ 3: success. New interval = old_interval × ease
- Grade < 3: failure. Reset interval to 1
- New ease = old_ease + 0.1 - (5-grade) × (0.08 + (5-grade) × 0.02)
- Minimum ease = 1.3

---

## Sub-command: graph (Knowledge Graph)

### Full graph view

1. If `graph/` missing or stale, rebuild from all `day-*.md`
2. Render Mermaid `flowchart LR` + text outline grouped by day

### Concept query

Resolve via `index.json`, print focused concept card:
```
🔎 Concept Name  (Day N · tags)
Definition: ...
Analogy: ...
📍 Taught in:  day-N.md#anchor
💻 Code:       demo/path:line
🔗 Related:    → enables X · ⚡ contrasts Y
⚠️ Pitfalls:   ...
🎤 Interview:  Q → A
```

---

## Sub-command: find (Query Active Course)

1. Read `.active`, ensure `graph/` exists
2. Resolve term: concept name → alias → tag → code path → fuzzy
3. **Concept** → focused card
4. **Code path** → owning concept + code_refs
5. **Tag/topic** → interview aggregation (八股): all nodes under tag as one study sheet

---

## Sub-command: status (Progress Dashboard)

Scan `$DATA_ROOT/courses/`, read each `state.json`, render:
```
📚 Learning Dashboard

▶ ACTIVE: nextjs  (standard mode)
  Day 4 / 7   [██████░░░░░░]  57%
  Last demo: courses/nextjs/demo/  (Day 3: CRUD with Server Actions)
  Mastered: routing, server components, data fetching, prisma
  → Next: continue

Other courses:
  • rust      Day 2 / 3   [████████░░]  done? no   → continue rust
  • react     ✅ completed (7/7)          → review react
```

First-time user (no courses): show welcome message.

---

## General Rules

1. **Language**: Follow the user's language (Chinese question → Chinese teaching)
2. **Context7 fallback**: Context7 → WebSearch → prompt user for docs URL
3. **Progress visible**: Show progress bar at start of each continue
4. **Memory sync**: state.json and memory/ updated simultaneously after each lesson
5. **Teaching iron rule**: Explain → Visualize → Build → Verify. NEVER skip to code.
6. **Don't re-teach known**: `mastered = completed_concepts ∪ known_concepts` + other courses' concepts from memory. Compress or skip. Always name what's skipped.
7. **Visual-first**: Every lesson needs at least 1 comparison table + 1 Mermaid diagram + annotated code
8. **Active recall**: After each concept, ask the user to explain it back (Feynman check)
