# Learn Modern Tech

> A Claude Code Plugin that chains Research → Goal Collection → Planning → Teaching → Progressive Projects → Review into a complete learning workflow.

## Install

```bash
# Local development
claude --plugin-dir ./learn-modern-tech

# From Marketplace (after publishing)
/plugin install learn-modern-tech@claude-code-marketplace
```

### Prerequisites

- Claude Code CLI installed
- Node.js 18+ (for Context7 MCP Server)

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

# Build knowledge graph for a completed course (P2+)
/learn graph nextjs
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
| **Cheat Sheets** | Auto-generate structured review cards |

## Commands

| Command | Description |
|---------|-------------|
| `/learn <tech>` | Initialize a new course (research → ask goals → generate skeleton) |
| `/learn continue` | Continue the active course (generate today's lesson) |
| `/learn continue <tech>` | Switch to a specific course and continue |
| `/learn review` | Generate cheat sheets for the current course |
| `/learn review <tech>` | Generate cheat sheets for a specific course |
| `/learn graph <tech>` | Build knowledge graph for a completed course (P2+) |

## Architecture

```
learn-modern-tech/
├── .claude-plugin/plugin.json    # Plugin metadata
├── commands/learn.md             # /learn entry command
├── agents/
│   ├── researcher.md             # Research agent (context isolation)
│   └── teacher.md                # Teaching agent (visual-first methodology)
├── skills/teaching-method/       # Teaching methodology
├── .mcp.json                     # Context7 MCP Server
├── memory/                       # Learning Memory
├── courses/                      # Course outputs
└── data/graphs/                  # Knowledge graphs (P2+)
```

## Detailed Design

- Command flow & sub-commands: [`commands/learn.md`](commands/learn.md)
- Research methodology: [`agents/researcher.md`](agents/researcher.md)
- Teaching methodology (evidence base): [`skills/teaching-method/SKILL.md`](skills/teaching-method/SKILL.md) and its `references/`

## Data & Privacy

All course content, progress state, and Learning Memory are stored locally under
`$HOME/.claude/learn/` (Windows: `%USERPROFILE%\.claude\learn\`) — never inside the
read-only plugin directory, and never sent anywhere except the doc/search queries
you trigger.
