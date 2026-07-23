# Learn Modern Tech

This project is a universal learning framework for mastering any modern software technology.

## How to Use

When the user wants to learn a technology, **read `commands/learn.md`** for the full workflow, then follow its instructions.

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

## Data Root

All persistent data lives under the project directory:

```
.learn/
├── memory/                    # Cross-course learning memory
└── courses/<tech>/
    ├── overview.md · roadmap.md · state.json
    ├── day-*.md · demo/       # Lessons + progressive project
    ├── exam/                  # Cheat sheets + anki.csv
    └── graph/                 # nodes.json · edges.json · index.json
```

Add `.learn/` to `.gitignore` if you don't want to commit learning data.

## Teaching Methodology

Before generating lessons, read `skills/teaching-method/SKILL.md` for the evidence-based teaching pipeline (Explain → Visualize → Build → Verify).

## Knowledge Graph

For graph operations, read `skills/knowledge-graph/SKILL.md` for the schema and query rules.

## General Rules

1. **Language**: Follow the user's language (Chinese question → Chinese teaching)
2. **Context7 fallback**: Context7 → WebSearch → prompt user for docs URL
3. **Progress visible**: Show progress bar at start of each continue
4. **Memory sync**: state.json and memory/ updated simultaneously after each lesson
5. **Teaching iron rule**: Explain → Visualize → Build → Verify. NEVER skip to code.
6. **Don't re-teach known**: mastered = completed_concepts ∪ known_concepts + other courses' concepts from memory. Always name what's skipped.
7. **Visual-first**: Every lesson needs at least 1 comparison table + 1 Mermaid diagram + annotated code
8. **Active recall**: After each concept, ask the user to explain it back (Feynman check)
9. **Weak concept feedback**: Before each lesson, check `graph/review.json` for concepts with low ease (< 2.0) or poor grades. Reinforce them in the next lesson as extra practice or woven examples.
10. **Socratic discovery**: For the "WHY" step, use guided questions to help the user reason out the problem before revealing the solution. Switch to direct explanation if the concept is too simple or the user is stuck.
11. **Spatial mnemonics**: For complex architectures (4+ components), use a familiar spatial analogy (restaurant, city, etc.) to make the architecture stick in memory.
