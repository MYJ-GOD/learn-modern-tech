---
description: Learn any modern software technology. Usage: /learn <tech> (init) | continue | review | graph
argument-hint: <tech> | continue [tech] | review [tech] | graph <tech>
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
| `/learn graph <tech>` | graph | Build knowledge graph for completed course |

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
```
AskUserQuestion:
  question: "Your technical background? (Detected from memory: xxx)"
  header: "Background"
  options:
    - label: "React experience"
      description: "Know components, JSX, hooks"
    - label: "Other frontend framework"
      description: "Vue/Angular etc., haven't used React"
    - label: "Backend experience, frontend newbie"
      description: "Familiar with server-side dev, new to frontend"
    - label: "Complete beginner"
      description: "Limited programming experience"
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
  "completed_concepts": []
}
```

**`courses/.active`**: Write current active course name

**Sync update memory/**: Write "User started learning <tech>"

★ **Do NOT generate lesson content.** Day 7 content should adapt based on user's Day 1-6 reactions. Writing it upfront means giving up being a "live teacher."

---

## Sub-command: continue (Generate Daily Lesson On-Demand)

### Step 0: Mandatory State Read

1. Read `courses/.active` to determine current course (use parameter if tech specified)
2. Read `courses/<tech>/state.json`
3. If doesn't exist, prompt: "Please initialize first with `/learn <tech>`"
4. Determine `current_day` and `mode`

### Step 1: Launch teacher agent

Pass the following info to the **teacher** agent:
- state.json content (current progress)
- Learning Memory content (user background)
- overview.md and roadmap.md content (course plan)
- Today is Day N

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
- "Too easy" → Skip or go deeper
- "How does X relate to Y?" → Use known knowledge as analogy

### Step 3: Update State After Completion

After user confirms lesson complete:
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
2. **Language**: Follow the user's language (Chinese question → Chinese teaching, English → English)
3. **Context7 fallback**: Context7 has results → use Context7; otherwise → WebSearch; neither → prompt user to provide docs URL
4. **Progress visible**: Show current progress bar at the start of each continue
5. **Memory bidirectional sync**: state.json and memory/ (both under DATA_ROOT) updated simultaneously after each lesson
6. **Teaching iron rule**: Explain → Visualize → Build → Verify. NEVER skip to code.
