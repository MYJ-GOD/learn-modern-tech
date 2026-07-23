---
name: Teaching Method
description: Evidence-based teaching methodology grounded in cognitive science. Loaded when the teacher agent generates lessons or conducts teaching dialogues.
version: 0.2.0
---

# Teaching Methodology (Evidence-Based)

All strategies below are grounded in peer-reviewed cognitive science research. See `references/learning-science.md` for citations and evidence strength ratings.

## Core Principles

### 1. Explain -> Visualize -> Build -> Verify (Iron Pipeline)

Every lesson follows this sequence. NEVER skip to code.

```
1. WHAT    - One sentence + analogy from known tech
2. WHY     - Why does this exist? What problem does it solve?
3. MAP     - Architecture diagram / concept map (Mermaid)
4. BUILD   - Step-by-step, explain WHY before showing HOW
5. CONNECT - How today's pieces relate (diagram)
6. RECALL  - Close everything, try to explain from memory
```

### 2. Cognitive Load Management (Sweller, 1988)

Working memory holds ~4 chunks (Cowan, 2001). Chunk concepts into groups of 4-5 max, minimize extraneous load, maximize germane load, sequence by dependency.

### 3. Active Recall > Passive Review (Roediger & Karpicke, 2006)

> If it feels easy, you're probably not learning.

After each concept: close the docs, try to explain from memory. Exercises are retrieval practice. Use fill-in-the-blank code cards, not multiple choice.

### 4. Dual Coding (Paivio, 1971)

Every lesson MUST include at least 1 comparison table, at least 1 Mermaid diagram, and annotated code.

### 5. Spaced Repetition (Ebbinghaus, 1885; Cepeda et al., 2008)

Without review, 66% forgotten in 24 hours. Optimal intervals: 1, 3, 7, 14, 30, 90 days. Each lesson begins with active recall of previous day's material (not re-teaching).

### 6. Interleaving (Rohrer & Taylor, 2007)

Mixing topics during practice beats blocked practice. The "desirable difficulty" principle: struggle during practice = durable memory.

### 7. Bloom's Taxonomy Progression (Anderson & Krathwohl, 2001)

| Level | Mode Coverage | Programming Example |
|-------|--------------|-------------------|
| 1. Remember | All modes | "List the React lifecycle methods" |
| 2. Understand | All modes | "Explain why useEffect runs after render" |
| 3. Apply | Speed -> Deep | "Build a todo app using useState" |
| 4. Analyze | Standard -> Deep | "Compare Redux vs Context API" |
| 5. Evaluate | Deep only | "Critique this architecture" |
| 6. Create | Deep + Source | "Design a new state management library" |

### 8. Feynman Technique Integration

After learning each concept, the user must explain it back in their own words. Teacher explains -> user explains back -> if gap, re-explain with different analogy -> if clear, proceed.

### 9. Zone of Proximal Development (Vygotsky, 1978)

Material should be challenging but achievable with guidance. Calibrate difficulty to background; provide scaffolding that can be progressively removed.

### 10. Chunking (Miller, 1956; Cowan, 2001)

Working memory: 4 +/- 1 chunks. Group 15 individual API methods into 3-4 meaningful chunks.

## Mode-Specific Calibration

### Speed Mode (3 days)
Bloom's 1-3. 3-4 chunks per lesson. Day 2 recalls Day 1; Day 3 recalls Day 1+2. Post-course: Day 5, 8, 13, 21.

### Standard Mode (7 days)
Bloom's 1-4. 4-5 chunks. Each lesson recalls 2-days-ago; Day 5 comprehensive. Post-course: Day 10, 17, 24, 38, 65. Adds deeper "why", pitfalls, acceptance criteria.

### Deep Mode (14 days)
Bloom's 1-6. 15-min recall each lesson; Day 7 + Day 14 comprehensive. Post-course: Day 17, 22, 29, 43, 70, 120. Adds source code analysis, design patterns, Feynman teach-back.

### Source Code Mode (open-ended)
Bloom's 4-6. Module-by-module: entry file -> init flow -> class hierarchy -> call chains -> design patterns -> architecture diagrams.

## Diagram Requirements

| Situation | Diagram Type | Mermaid Keyword |
|-----------|-------------|-----------------|
| Architecture overview | Flowchart | `flowchart TD/LR` |
| Request lifecycle | Sequence diagram | `sequenceDiagram` |
| Concept relationships | Class diagram | `classDiagram` |
| State transitions | State diagram | `stateDiagram-v2` |
| Concept dependency (learning order) | Flowchart | `flowchart LR` |

Note: Mermaid renders in IDE/web markdown viewers, not raw terminals. For CLI-only contexts, pair each diagram with a Markdown table or ASCII fallback.

## References

- `references/learning-science.md` - Full evidence base with citations
- `references/visual-learning.md` - Visual learning techniques and diagram usage
- `references/socratic.md` - Socratic questioning method
- `references/project-based.md` - Project-driven learning method
- `references/spaced-repetition.md` - Spaced repetition with SM-2/FSRS intervals
