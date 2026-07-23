---
description: Learn any modern software technology. Usage: /learn <tech> (init) | continue | review | recall | graph | find | status | help
argument-hint: <tech> | continue [tech] | review [tech] | recall [tech] | graph <tech> [concept] | find <term> | status | help
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Agent, WebSearch, WebFetch, AskUserQuestion, mcp__context7__resolve_library_id, mcp__context7__query_docs
---

# Learn Modern Tech — Learning Command

## Data Root (READ THIS FIRST)

The plugin's own install directory is **read-only** when installed from a marketplace. NEVER write course, memory, or state files inside the plugin directory.

All persistent data lives under the **Data Root**:

```
DATA_ROOT = $HOME/.claude/learn        (Windows: %USERPROFILE%\.claude\learn)
```

Resolve it at the start of every sub-command:
- Bash: `LEARN_ROOT="${HOME:-$USERPROFILE}/.claude/learn"; mkdir -p "$LEARN_ROOT/courses" "$LEARN_ROOT/memory"`

Every relative path in this document resolves against `DATA_ROOT`:

| Logical path | Real path |
|--------------|-----------|
| `courses/<tech>/` | `$DATA_ROOT/courses/<tech>/` |
| `courses/.active` | `$DATA_ROOT/courses/.active` |
| `memory/` | `$DATA_ROOT/memory/` |

The plugin directory itself is read only for reading bundled `agents/`, `skills/`, and templates.

## Argument Parsing

User input: `$ARGUMENTS`

Parse sub-commands as follows:

| Input | Sub-command | Action |
|-------|-------------|--------|
| `/learn` (empty) | status | Show progress dashboard (default when no args) |
| `/learn status` | status | Show progress dashboard |
| `/learn help` | help | Show command reference |
| `/learn <tech>` | init | Initialize a new course |
| `/learn continue` | continue | Continue the active course |
| `/learn continue <tech>` | continue | Switch to specified course and continue |
| `/learn review` | review | Generate cheat sheets |
| `/learn review <tech>` | review | Generate cheat sheets for specified course |
| `/learn graph <tech>` | graph | Build/rebuild + show the course knowledge graph |
| `/learn graph <tech> <concept>` | graph | Show a focused card for one concept |
| `/learn find <term>` | find | Query the active course's graph (concept / code / tag / 八股) |
| `/learn recall` | recall | Adaptive spaced-repetition session (SM-2) for due concepts |
| `/learn recall <tech>` | recall | Recall session for a specific course |

**Reserved keywords**: `status`, `help`, `continue`, `review`, `graph`, `find`, `recall` are sub-commands. If a user genuinely wants to learn a technology literally named one of these, they can't collide in practice — treat a bare reserved word as its sub-command. Any other single token is treated as `<tech>` for `init`.

**Ambiguity guard**: Before `init` on a `<tech>`, check `$DATA_ROOT/courses/<tech>/state.json`. If it already exists, do NOT re-initialize — inform the user the course exists and route to `continue` (or ask if they want to restart).

---

## Sub-command: init (Initialize New Course)

### Step 1: Research (launch researcher agent)

Launch the **researcher** agent with the task:
1. Query Context7 for the latest documentation (`mcp__context7__resolve_library_id` → `mcp__context7__query_docs`)
2. Search WebSearch for latest news, changelog, community discussions
3. Fetch official docs homepage and core concept pages via WebFetch

The researcher must return a **compressed structured overview** containing:
- Technology name, current version, positioning
- Core concept list (ordered by importance)
- Recent breaking changes / new features
- Ecosystem (common companion tools)
- Suggested learning order (topological sort)

### Step 2: Collect Goals (use AskUserQuestion for structured interaction)

Read Learning Memory (all files in `memory/` directory) to extract the user's known tech stack.

Then use **AskUserQuestion** tool for structured selection (do NOT use open-ended questions):

**Q1: What do you want to do?** (goal-driven, reverse-engineer required knowledge)
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

**Q2: How much time do you have?**
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

**Q3: Your background?** (auto-fill from Memory first; only ask if insufficient)

★ Generate the options **dynamically from the target technology's prerequisites** (from the researcher's report) — do NOT hardcode React-specific options. The options should map to the actual prior knowledge that changes how `<tech>` is taught.

Guidelines for building the options:
- Derive 3-4 background buckets from the tech's real prerequisites. Examples:
  - Learning **Next.js** → "React experience" / "Other frontend framework" / "Backend, frontend-new" / "Complete beginner"
  - Learning **Rust** → "Systems lang (C/C++)" / "GC lang (Go/Java/Python)" / "Only scripting (JS/Python)" / "Complete beginner"
  - Learning **Kubernetes** → "Docker experience" / "Traditional ops/VMs" / "App dev, ops-new" / "Complete beginner"
- Always include a "Complete beginner" option.
- Each label ≤ 5 words; description names the concrete prior knowledge assumed.

```
AskUserQuestion:
  question: "Your technical background? (Detected from memory: xxx)"
  header: "Background"
  options: <dynamically generated per above>
  multiSelect: true
```

★ If Learning Memory already has the user's background recorded, skip Q3 and only ask Q1 and Q2.

### Step 3: Generate Skeleton (only overview + roadmap, NO lesson content)

Generate in `courses/<tech>/` directory:

**`overview.md`**: Framework status report (from researcher's compressed output)

**`roadmap.md`**: Learning roadmap containing:
- Mermaid flowchart (concept dependency + learning order)
- MVP demo design (progressive, one feature per day)
- Daily/module outline based on user's chosen granularity

**`state.json`**: Initialize course state
```json
{
  "active_course": "<tech>",
  "current_day": 1,
  "total_days": <based on granularity>,
  "mode": "<speed|standard|deep|source>",
  "last_demo_path": null,
  "completed_concepts": [],
  "known_concepts": []
}
```

- `completed_concepts`: concepts **taught in this course** and confirmed done.
- `known_concepts`: concepts the user **already knew and skipped** (from prior
  background or marked mid-course). Both count as "mastered" for skip decisions,
  but are tracked separately so `review`/`graph` know what was actually taught here.

At init, seed `known_concepts` from Learning Memory: if memory records the user
already masters a concept that appears in this course's roadmap, pre-fill it here.

**`courses/.active`**: Write current active course name

**Sync update memory/**: Write "User started learning <tech>"

★ **Do NOT generate lesson content.** Day 7 content should adapt based on user's Day 1-6 reactions. Writing it upfront means giving up being a "live teacher."

---

## Sub-command: continue (Generate Daily Lesson On-Demand)

### Step 0: Mandatory State Read

1. Read `courses/.active` to determine current course (use parameter if tech specified)
2. Read `courses/<tech>/state.json`
3. If it doesn't exist:
   - No courses at all → run the `status` welcome path (guide them to `/learn <tech>`)
   - Courses exist but not this one / no active → run `status` so they can pick one
   - A specific `<tech>` was named but not initialized → "No course for `<tech>` yet. Start it with `/learn <tech>`."
4. If `current_day > total_days` → course is complete; suggest `/learn review <tech>` instead of teaching.
5. Determine `current_day` and `mode`.

### Step 0.5: Skip-check (avoid re-teaching known concepts)

Before launching the teacher, compute what Day N *would* cover (from `roadmap.md`)
and cross-check against **already-mastered** concepts:

- `mastered = completed_concepts ∪ known_concepts` (from state.json)
- Also consult Learning Memory for concepts mastered in **other courses**
  (e.g. learned `hooks` in a React course → skip it in a Next.js course).

Then:
1. **All of Day N is already known** → don't re-teach. Tell the user briefly what
   they already know, mark those into `known_concepts`, advance `current_day`, and
   offer: "Skip to Day N+1?" or "Quick recall check instead?" Do NOT generate a full lesson.
2. **Part of Day N is known** → pass the known subset to the teacher so it
   *compresses* those into a one-line recall ("You already know X — quick check:
   …") and spends the lesson budget on the genuinely new concepts.
3. **Nothing known** → proceed normally.

Never silently skip: always name what's being skipped so the user can veto
("actually, re-teach that").

### Step 1: Launch teacher agent

Pass the following info to the **teacher** agent:
- state.json content (current progress) — including `completed_concepts` + `known_concepts`
- Learning Memory content (user background + concepts mastered in other courses)
- overview.md and roadmap.md content (course plan)
- Today is Day N
- **Skip list**: concepts the user already knows → compress to a recall check, don't teach fresh

Teacher agent executes:
1. Fetch latest docs and code examples from Context7 for today's concepts
2. Generate `day-N.md` following the **Explain → Visualize → Build → Verify** pipeline:
   - **Speed mode**: Mental model → Architecture diagram → Structure table → Step-by-step build → Connection diagram
   - **Standard mode**: Everything in Speed + deeper "why" + exercises with acceptance criteria
   - **Deep mode**: Everything in Standard + source code analysis + design patterns
   - **Source mode**: Call chain analysis + architecture diagram + design patterns
3. Generate demo code (progressive, building on previous day)
4. ★ Iron rule: try to run the code first; if can't, provide verification checklist

### Step 2: Interactive Teaching

After teacher outputs the lesson, enter dialogue mode:
- Wait for user feedback
- "I don't get it" → Switch analogy/angle/method to re-explain
- "Too easy" / "I already know this" / "skip X" → confirm which concept(s), then
  **mark them into `known_concepts`** (Step 3 will persist), stop teaching those,
  and move on. If it's a whole-topic skip, jump ahead; if partial, continue with the rest.
- "How does X relate to Y?" → Use known knowledge as analogy

**Skip is a first-class action, not just "go faster."** When the user marks a
concept known, it must be recorded so future days and future courses respect it.

### Step 3: Update State After Completion

After user confirms lesson complete:
1. **Update the knowledge graph** (incremental): follow the **Knowledge Graph** skill
   (`skills/knowledge-graph/SKILL.md`) → "Building / Updating". Extract today's new
   concepts → nodes (with real `code_refs` from the demo you just built), link edges
   to prior concepts, rebuild `index.json`. If extraction isn't clean, skip rather
   than write garbage — a later `/learn graph` rebuild recovers it.
2. Update `state.json`:
   - `current_day++`, update `last_demo_path`
   - Append concepts **taught this lesson** → `completed_concepts`
   - Append concepts the user **skipped/marked known** this lesson → `known_concepts`
   - Never double-list a concept in both arrays.
3. Update `memory/`: record mastery so **other courses** can skip it later. Write
   the concept names (not just "Day N done") — e.g. "Mastered: hooks, JSX, components
   (via <tech>, Day N)". Skipped-known concepts count as mastered in memory too.
4. If `current_day > total_days`, prompt: "Course complete! Run `/learn review` for cheat sheets, `/learn recall` to start spaced-repetition review, or `/learn graph <tech>` to explore the knowledge map."

---

## Sub-command: review (Generate Cheat Sheets)

1. Read all `day-*.md` and `overview.md` under `courses/<tech>/`. If `graph/nodes.json`
   exists, also read it — its `interview_qa`, `definition`, and `pitfalls` are the
   best raw material for cards (already concept-anchored).
2. Launch **teacher** agent to extract structured knowledge:
   - `exam/concepts.md`: Concept cheat sheets (Q&A format)
   - `exam/patterns.md`: Practical cheat sheets (code templates)
   - `exam/architecture.md`: Architecture cheat sheets (component relationship diagrams)
   - `exam/comparison.md`: Comparison cheat sheets (A vs B tables, if applicable)
3. Grade by difficulty: Beginner / Intermediate / Advanced
4. **Export Anki-importable flashcards** → `exam/anki.csv` (see below).
5. Update memory: Write "User completed <tech> course, mastery level: xxx"

### Anki CSV Export (`exam/anki.csv`)

Anki natively imports CSV/TSV — no `.apkg` packaging needed. Generate a UTF-8 CSV
with a header comment block Anki understands, then `front,back,tags` rows:

```
#separator:Comma
#html:true
#columns:Front,Back,Tags
#tags column:3
"What are React Server Components?","Components that render on the server and ship zero JS to the browser. Can't use hooks or event handlers.","nextjs rendering day2"
"useState vs useReducer?","useState: simple local state. useReducer: complex state with multiple actions / transitions.","nextjs state day3"
```

Rules for good cards (from `references/spaced-repetition.md` → Flashcard Design):
- **Question format**, one concept per card (not statements, not paragraphs).
- Prefer cards sourced from graph `interview_qa`; supplement with fill-in-the-blank
  code cards (`const [state, ____] = useState(0)` → `setState`).
- Escape per CSV: wrap every field in double quotes; double any internal `"`.
- Use HTML `<br>` for line breaks and `<code>…</code>` for inline code (header sets `#html:true`).
- **Tags** column: space-separated, always include `<tech>` + the concept's day + tags.
- Skip concepts in `known_concepts` (user already knew them — don't drill them).

Tell the user how to import: **Anki → File → Import → select `anki.csv`**, field
mapping is automatic from the header. Also mention the plain-Markdown cheat sheets
in `exam/` for reading/printing.

---

## Sub-command: status (Progress Dashboard) — also the default for bare `/learn`

Goal: in ONE glance, answer "what am I learning, how far am I, what do I do next."

### Step 1: Scan the Data Root

```bash
LEARN_ROOT="${HOME:-$USERPROFILE}/.claude/learn"
```

1. If `$LEARN_ROOT/courses/` doesn't exist or is empty → this is a first-time user. Show the **welcome path** (see below) instead of a dashboard.
2. Read `$LEARN_ROOT/courses/.active` → the active course name (may be absent).
3. For each `courses/<tech>/` directory, read its `state.json`.

### Step 2: Render the Dashboard

For the **active course**, show a progress bar built from `current_day` / `total_days`:

```
📚 Learning Dashboard

▶ ACTIVE: nextjs  (standard mode)
  Day 4 / 7   [██████░░░░░░]  57%
  Last demo: courses/nextjs/demo/  (Day 3: CRUD with Server Actions)
  Mastered: routing, server components, data fetching, prisma
  → Next: /learn continue

Other courses:
  • rust      Day 2 / 3   [████████░░]  done? no   → /learn continue rust
  • react     ✅ completed (7/7)          → /learn review react

Total: 3 courses · 1 active · 1 completed
```

Rules:
- Build the bar from real numbers: filled = round(current_day-1 / total_days * 12), so "Day 4/7" shows 3 full days done.
- If `current_day > total_days`, mark ✅ completed and suggest `/learn review <tech>`.
- "Mastered" line = `completed_concepts` (taught) + `known_concepts` (skipped, tag them
  as "known" e.g. "routing, RSC, hooks (known)"). Truncate to ~5, "+N more".
- If `.active` names a course whose folder is missing, warn and list what does exist.
- Keep it compact. No walls of text.

### Welcome path (first-time user, no courses yet)

```
👋 No courses yet. Start one with:

  /learn <tech>        e.g. /learn nextjs, /learn rust, /learn kubernetes

I'll research the latest docs, ask about your goal + pace, and build a
day-by-day plan. Then /learn continue teaches one lesson at a time.

Run /learn help for the full command list.
```

---

## Sub-command: help (Command Reference)

Print a concise, copy-pasteable reference. Follow the user's language.

```
🎓 Learn Modern Tech — Commands

  /learn <tech>            Start a new course (research → goals → plan)
                           e.g. /learn nextjs · /learn rust · /learn fastapi
  /learn continue          Teach today's lesson of the active course
  /learn continue <tech>   Switch to another course and continue
  /learn review            Generate cheat sheets (+ Anki anki.csv) for the active course
  /learn review <tech>     Cheat sheets for a specific course
  /learn recall [tech]     Adaptive spaced-repetition session (SM-2) for due concepts
  /learn graph <tech>      Build + show the course knowledge map
  /learn graph <tech> <c>  Focused card for one concept
  /learn find <term>       Query active course: concept · code · topic (八股)
  /learn status            Show progress dashboard (also: bare /learn)
  /learn help              This message

Modes (chosen at init): ⚡ speed 3d · 📚 standard 7d · 🔬 deep 14d · 🔍 source
Data is stored locally under ~/.claude/learn/ — nothing is uploaded.
```

---

## Sub-command: graph (Knowledge Graph — Build & Explore)

Per-course knowledge graph: concepts linked by relationships, anchored to lessons
and demo code. Full details in the **Knowledge Graph** skill (`skills/knowledge-graph/SKILL.md`).

### `/learn graph <tech>` (no concept term)

1. Resolve `<tech>` (or `.active` if omitted). If no such course → route to `status`.
2. If `courses/<tech>/graph/` is missing or older than the newest `day-*.md`,
   **rebuild** it from all `day-*.md` + demo (skill → "Manual Build / Rebuild").
3. Render the **full graph view**: a Mermaid `flowchart LR` PLUS a compact
   day-grouped text outline (terminals don't render Mermaid — always include text).

### `/learn graph <tech> <concept>`

Resolve the concept via `index.json` and print the **focused concept card**
(definition, analogy, lesson anchor, code refs, related edges, pitfalls, interview Q&A).
If unmatched, list the closest 3-5 concept labels.

## Sub-command: find (Query the Active Course Graph)

Shortcut for querying without naming the tech — uses `.active`.

1. Read `.active`; if none → `status`. Ensure its `graph/` exists (build if missing).
2. Resolve `<term>` in order: concept name/alias → tag → code path/symbol → fuzzy.
3. Dispatch by match type (skill → "Querying"):
   - **Concept** → focused concept card (same as `graph <tech> <concept>`)
   - **Code path/symbol** → the concept(s) that own it + their `code_refs` (code-jump)
   - **Tag / topic phrase** → **interview aggregation (八股)**: all nodes under the
     tag as one study sheet with contrasts + merged Q&A
4. No match → say so, suggest `/learn graph <tech>` for the full map.

Examples:
- `/learn find server components` → concept card
- `/learn find middleware.ts` → "route-protection (Day 3), demo/middleware.ts:12"
- `/learn find state management` → aggregated 八股 sheet across useState/useReducer/Context

## Sub-command: recall (Adaptive Spaced Repetition — SM-2)

Active-recall review session scheduled per-concept by SM-2. Full algorithm in
`skills/teaching-method/references/spaced-repetition.md` → "Adaptive Scheduling".

1. Resolve `<tech>` (or `.active`). Ensure `graph/` exists; if not, suggest
   `/learn continue` first (nothing to recall yet).
2. Read `graph/review.json` (empty if absent) and `graph/nodes.json`.
3. **Select due concepts**: `due <= today`, plus any completed-day concept with no
   review entry yet (seed it). Exclude `known_concepts`. Order hardest/most-overdue first.
   - If nothing is due: tell the user, show the next due date, offer an early review anyway.
4. **Quiz** each due concept using **active recall** (not re-teaching):
   - Ask a question from the node's `interview_qa`, or "Explain X in your own words"
     (Feynman), or a fill-in-the-blank from its `code_refs`.
   - Let the user answer from memory, THEN reveal the expected answer.
5. **Grade** the answer 0–5 (self-assessed, or inferred from the reply per the grade
   mapping), apply the SM-2 update, and set the new `due`.
6. Write back `graph/review.json`. Summarize: how many reviewed, how many passed,
   what's scheduled next, and which concepts are weak (low ease) and worth a
   `/learn find <concept>` refresher.

Keep it a session, not a lecture: one concept at a time, answer-first, brief.

---

## General Rules

1. **Data Root**: Always resolve `DATA_ROOT = $HOME/.claude/learn` at the start of every sub-command (Windows: `$USERPROFILE\.claude\learn`), and `mkdir -p` the target directories before writing. NEVER write to the plugin install directory.
2. **Language**: Follow the user's language (Chinese question → Chinese teaching, English → English)
3. **Context7 fallback**: Context7 has results → use Context7; otherwise → WebSearch; neither → prompt user to provide docs URL
4. **Progress visible**: Show current progress bar at the start of each continue
5. **Memory bidirectional sync**: state.json and memory/ (both under DATA_ROOT) updated simultaneously after each lesson
6. **Teaching iron rule**: Explain → Visualize → Build → Verify. NEVER skip to code.
7. **Don't re-teach what's known**: before every lesson, `mastered = completed_concepts ∪ known_concepts` (this course) plus concepts mastered in other courses (from memory). Compress or skip those — but always name what's skipped so the user can veto.
