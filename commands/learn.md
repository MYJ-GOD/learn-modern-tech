---
description: Learn any modern software technology. Usage: /learn <tech> (init) | continue | review | graph
argument-hint: <tech> | continue [tech] | review [tech]
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
| `/learn <tech>` | init | Initialize a new course |
| `/learn continue` | continue | Continue the active course |
| `/learn continue <tech>` | continue | Switch to specified course and continue |
| `/learn review` | review | Generate cheat sheets |
| `/learn review <tech>` | review | Generate cheat sheets for specified course |

---

## Sub-command: init (Initialize New Course)

### Step 1: Research (launch researcher agent)

Launch the **researcher** agent with the task:
1. Query Context7 for the latest documentation (`mcp__context7__resolve_library_id` -> `mcp__context7__query_docs`)
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

- **Q1 Goal**: Quick overview (speed/3d) | Build a complete project (standard/7d) | Deep mastery (deep/14d) | Interview prep
- **Q2 Pace**: 3 days | 7 days | 14+ days | Source code study (no limit)
- **Q3 Background**: auto-fill from Memory first; only ask if insufficient. Generate the background options **dynamically based on the target technology's prerequisites** (do NOT hardcode React-specific options).

If Learning Memory already has the user's background recorded, skip Q3 and only ask Q1 and Q2.

### Step 3: Generate Skeleton (only overview + roadmap, NO lesson content)

Generate in `courses/<tech>/` directory (under DATA_ROOT):

- **`overview.md`**: Framework status report (from researcher's compressed output)
- **`roadmap.md`**: Mermaid flowchart (concept dependency + learning order) + MVP demo design (progressive, one feature per day) + daily outline
- **`state.json`**: `{ "active_course", "current_day": 1, "total_days", "mode", "last_demo_path": null, "completed_concepts": [] }`
- **`courses/.active`**: Write current active course name
- **Sync update memory/**: Write "User started learning <tech>"

**Do NOT generate lesson content.** Later-day content should adapt based on earlier days' reactions. Writing it upfront means giving up being a "live teacher."

---

## Sub-command: continue (Generate Daily Lesson On-Demand)

### Step 0: Mandatory State Read

1. Read `courses/.active` to determine current course (use parameter if tech specified)
2. Read `courses/<tech>/state.json`
3. If doesn't exist, prompt: "Please initialize first with `/learn <tech>`"
4. Determine `current_day` and `mode`

### Step 1: Launch teacher agent

Pass to the **teacher** agent: state.json content, Learning Memory content, overview.md + roadmap.md, "Today is Day N".

Teacher agent executes:
1. Fetch latest docs and code examples from Context7 for today's concepts
2. Generate `day-N.md` following the **Explain -> Visualize -> Build -> Verify** pipeline (mode-calibrated)
3. Generate demo code (progressive, building on previous day)
4. Iron rule: try to run the code first; if can't, provide verification checklist

### Step 2: Interactive Teaching

After teacher outputs the lesson, enter dialogue mode: wait for feedback, re-explain on "I don't get it", skip/deepen on "too easy", use analogies for relationship questions.

### Step 3: Update State After Completion

1. Update `state.json`: `current_day++`, append `completed_concepts`, update `last_demo_path`
2. Update `memory/`: Write "Mastered <concept list>"
3. If `current_day > total_days`, prompt: "Course complete! Run `/learn review` to generate cheat sheets."

---

## Sub-command: review (Generate Cheat Sheets)

1. Read all `day-*.md` and `overview.md` under `courses/<tech>/`
2. Launch **teacher** agent to extract structured knowledge:
   - `exam/concepts.md`: Concept cheat sheets (Q&A format)
   - `exam/patterns.md`: Practical cheat sheets (code templates)
   - `exam/architecture.md`: Architecture cheat sheets (component relationship diagrams)
   - `exam/comparison.md`: Comparison cheat sheets (A vs B tables, if applicable)
3. Grade by difficulty: Beginner / Intermediate / Advanced
4. Update memory: Write "User completed <tech> course, mastery level: xxx"

---

## Sub-command: graph (Roadmap — Not Implemented Yet)

Post-learning knowledge graph construction across completed courses. Planned for a
future release. If invoked today, tell the user it is not yet available and suggest
`/learn review` for consolidation instead.

---

## General Rules

1. **Data Root**: Always resolve `DATA_ROOT = $HOME/.claude/learn` at the start of every sub-command (Windows: `$USERPROFILE\.claude\learn`), and `mkdir -p` the target directories before writing. NEVER write to the plugin install directory.
2. **Language**: Follow the user's language (Chinese question -> Chinese teaching, English -> English)
3. **Context7 fallback**: Context7 has results -> use Context7; otherwise -> WebSearch; neither -> prompt user to provide docs URL
4. **Progress visible**: Show current progress bar at the start of each continue
5. **Memory bidirectional sync**: state.json and memory/ (both under DATA_ROOT) updated simultaneously after each lesson
6. **Teaching iron rule**: Explain -> Visualize -> Build -> Verify. NEVER skip to code.
