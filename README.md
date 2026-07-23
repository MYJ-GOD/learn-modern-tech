# Learn Modern Tech

> A Claude Code Plugin that chains Research → Goal Collection → Planning → Teaching → Progressive Projects → Review into a complete learning workflow.

## Install

### Option A — via marketplace (recommended)

Inside Claude Code, add this repo as a marketplace, then install the plugin:

```
/plugin marketplace add MYJ-GOD/learn-modern-tech
/plugin install learn-modern-tech@learn-modern-tech
```

(`learn-modern-tech@learn-modern-tech` = plugin name @ marketplace name — both
happen to match here since this repo is a single-plugin marketplace.)

### Option B — local development

Clone the repo and point Claude Code at the directory:

```bash
git clone https://github.com/MYJ-GOD/learn-modern-tech.git
claude --plugin-dir ./learn-modern-tech
```

### Verify

```
/learn help
```

If you see the command list, it's loaded. First use auto-starts the Context7 MCP
server (downloads `@upstash/context7-mcp@3.2.4` via `npx` — needs Node 18+).

### Prerequisites

- Claude Code CLI installed
- Node.js 18+ (for Context7 MCP Server)

The Context7 MCP server is pinned to a specific version in `.mcp.json`
(`@upstash/context7-mcp@3.2.4`) rather than `@latest`, so the MCP tool names the
plugin calls (`mcp__context7__resolve_library_id`, `mcp__context7__query_docs`)
can't drift out from under it on an upstream update. To upgrade: bump the version
in `.mcp.json`, confirm the tool names still match those referenced in
`commands/learn.md` and `agents/*.md`, then commit.

## Usage

```bash
# Initialize a new course
/learn nextjs

# Continue today's lesson
/learn continue

# Continue a specific course
/learn continue rust

# Generate review cards (cheat sheets)
/learn review

# See your progress dashboard (or just run /learn)
/learn status

# Command reference
/learn help
```

## Core Features

| Feature | Description |
|---------|-------------|
| **Always Up-to-date** | Fetches latest docs via Context7 + WebSearch in real time |
| **Learning Memory** | Remembers what you already know, skips mastered concepts |
| **On-demand Generation** | Daily lessons generated at `/learn continue`, not upfront |
| **Progressive Demo** | Build a complete runnable project by the end of the course |
| **Code Trust** | Code is verified runnable; if not, a verification checklist is provided |
| **Visual-first Teaching** | Diagrams, tables, and flowcharts before any code |
| **Cheat Sheets** | Auto-generate structured review cards + Anki-importable `anki.csv` |
| **Knowledge Graph** | Per-course concept map; query by concept, code location, or topic (八股) |

## Commands

| Command | Description |
|---------|-------------|
| `/learn <tech>` | Initialize a new course (research → ask goals → generate skeleton) |
| `/learn continue` | Continue the active course (generate today's lesson) |
| `/learn continue <tech>` | Switch to a specific course and continue |
| `/learn review` | Generate cheat sheets for the current course |
| `/learn review <tech>` | Generate cheat sheets for a specific course |
| `/learn graph <tech>` | Build + explore the course knowledge map (concepts, code, relationships) |
| `/learn graph <tech> <concept>` | Focused card for one concept (definition, code refs, related, interview Q&A) |
| `/learn find <term>` | Query the active course: concept lookup · code jump · topic aggregation (八股) |
| `/learn recall [tech]` | Adaptive spaced-repetition session (SM-2) — reviews concepts when they're due |
| `/learn status` | Progress dashboard — courses, current day, what's next (also: bare `/learn`) |
| `/learn help` | Command reference |

## Architecture

```
learn-modern-tech/
├── .claude-plugin/plugin.json    # Plugin metadata
├── commands/learn.md             # /learn entry command
├── agents/
│   ├── researcher.md             # Research agent (context isolation)
│   └── teacher.md                # Teaching agent (visual-first methodology)
├── skills/
│   ├── teaching-method/          # Evidence-based teaching methodology
│   └── knowledge-graph/          # Per-course graph schema + build/query rules
├── .mcp.json                     # Context7 MCP Server
└── .gitignore                    # Runtime data lives in ~/.claude/learn/, not here
```

Runtime data (created on first use, under `~/.claude/learn/`):

```
~/.claude/learn/
├── memory/                       # Learning Memory (cross-course)
└── courses/<tech>/
    ├── overview.md · roadmap.md · state.json
    ├── day-*.md · demo/          # Lessons + progressive project
    ├── exam/                     # Cheat sheets (/learn review)
    └── graph/                    # nodes.json · edges.json · index.json (/learn graph)
```

## Detailed Design

- Command flow & sub-commands: [`commands/learn.md`](commands/learn.md)
- Research methodology: [`agents/researcher.md`](agents/researcher.md)
- Teaching methodology (evidence base): [`skills/teaching-method/SKILL.md`](skills/teaching-method/SKILL.md) and its `references/`
- Knowledge graph schema & queries: [`skills/knowledge-graph/SKILL.md`](skills/knowledge-graph/SKILL.md)

## Data & Privacy

All course content, progress state, and Learning Memory are stored locally under
`$HOME/.claude/learn/` (Windows: `%USERPROFILE%\.claude\learn\`) — never inside the
read-only plugin directory, and never sent anywhere except the doc/search queries
you trigger.
