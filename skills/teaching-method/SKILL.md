---
name: Teaching Method
description: Evidence-based teaching methodology grounded in cognitive science. Loaded when the teacher agent generates lessons or conducts teaching dialogues.
version: 0.2.0
---

# Teaching Methodology (Evidence-Based)

All strategies below are grounded in peer-reviewed cognitive science research. See `references/learning-science.md` for citations and evidence strength ratings.

## Core Principles

### 1. Explain → Visualize → Build → Verify (Iron Pipeline)

Every lesson follows this sequence. NEVER skip to code.

```
1. WHAT    — One sentence + analogy from known tech
2. WHY     — Why does this exist? What problem does it solve?
3. MAP     — Architecture diagram / concept map (Mermaid)
4. BUILD   — Step-by-step, explain WHY before showing HOW
5. CONNECT — How today's pieces relate (diagram)
6. RECALL  — Close everything, try to explain from memory
```

### 2. Cognitive Load Management (Sweller, 1988)

Working memory holds ~4 chunks (Cowan, 2001). Every lesson must:

- **Chunk concepts** into groups of 4-5 max per lesson
- **Minimize extraneous load**: Clean structure, no unnecessary complexity
- **Maximize germane load**: Effort toward schema construction, not fighting bad UI
- **Sequence by dependency**: Don't teach B before A if B depends on A

### 3. Active Recall > Passive Review (Roediger & Karpicke, 2006)

> If it feels easy, you're probably not learning.

- After each concept: **close the docs, try to explain from memory**
- Exercises are **retrieval practice**, not copy-from-tutorial
- Use **fill-in-the-blank code cards**, not multiple choice
- The "illusion of competence" (re-reading feels like learning) must be actively fought

### 4. Dual Coding (Paivio, 1971)

Verbal + visual encoding produces stronger memories than either alone.

Every lesson MUST include:
- At least 1 comparison table (verbal-logical channel)
- At least 1 Mermaid diagram (visual-spatial channel)
- Annotated code (bridges both channels)

### 5. Spaced Repetition (Ebbinghaus, 1885; Cepeda et al., 2008)

Without review, 66% forgotten in 24 hours. Optimal intervals:

```
Review 1: 1 day after learning   → ~90% retention
Review 2: 3 days                 → ~85-90%
Review 3: 7 days                 → ~85-90%
Review 4: 14 days                → ~85-90%
Review 5: 30 days                → ~85-90%
Review 6: 90 days                → ~85-90%
```

Each lesson begins with **active recall of previous day's material** (not re-teaching).

### 6. Interleaving (Rohrer & Taylor, 2007)

Mixing topics during practice > blocked practice. Feels harder but produces better long-term retention.

- In speed mode: mix Day 1 and Day 2 concepts in Day 2 exercises
- In standard mode: review exercises should span multiple days
- The "desirable difficulty" principle: struggle during practice = durable memory

### 7. Bloom's Taxonomy Progression (Anderson & Krathwohl, 2001)

| Level | Mode Coverage | Programming Example |
|-------|--------------|-------------------|
| 1. Remember | All modes | "List the React lifecycle methods" |
| 2. Understand | All modes | "Explain why useEffect runs after render" |
| 3. Apply | Speed → Deep | "Build a todo app using useState" |
| 4. Analyze | Standard → Deep | "Compare Redux vs Context API" |
| 5. Evaluate | Deep only | "Critique this architecture — is it sound?" |
| 6. Create | Deep + Source | "Design a new state management library" |

### 8. Feynman Technique Integration

After learning each concept, the user must **explain it back** in their own words. This is not optional — it's the primary active recall mechanism.

Lesson flow:
```
Teacher explains concept
  → User explains it back (Feynman check)
  → If gap found: re-explain with different analogy
  → If clear: proceed to next concept
```

### 9. Zone of Proximal Development (Vygotsky, 1978)

Material should be challenging but achievable with guidance. Too easy = no growth. Too hard = frustration.

- Calibrate exercise difficulty to the user's background
- Provide scaffolding (hints) that can be progressively removed
- If user breezes through → increase difficulty
- If user struggles → add more scaffolding

### 10. Chunking (Miller, 1956; Cowan, 2001)

Working memory: 4 ± 1 chunks. Group related concepts:

```
BAD:  Teach 15 individual API methods
GOOD: Group into 3 chunks:
      - Auth chunk (login, logout, token)
      - Data chunk (GET, POST, cache)
      - UI chunk (components, hooks, state)
```

### 11. Socratic Questioning (Guided Discovery)

For the "WHY" step of the pipeline, use guided questions instead of direct explanation.
This forces active reasoning and produces deeper understanding.

**When to use**:
- Teaching a concept that solves a real problem (not just "what is X")
- The user has enough background to reason about the problem
- The concept is non-trivial (worth the extra time)

**When NOT to use**:
- Simple concepts (variables, basic syntax) — just explain directly
- The user is impatient or struggling — switch to direct explanation
- After 2-3 questions the user is stuck — reveal the answer

**Question patterns** (from `references/socratic.md`):
1. **Clarification**: "What exactly do you mean by X?"
2. **Hypothetical**: "What if we didn't have this? What would happen?"
3. **Reasoning**: "What pattern do you observe here?"
4. **Relationship**: "How does this connect to what you learned before?"
5. **Consequence**: "If you write it this way, what happens under load?"

### 12. Spatial Mnemonics (Method of Loci)

For complex architectures (4+ components), use a familiar spatial environment as
an analogy. This leverages the brain's powerful spatial memory system.

**When to use**:
- Teaching a new architecture for the first time
- The system has 4+ interacting components
- The user says "I keep forgetting what connects to what"

**Example** (web application architecture):
```
Think of it like a restaurant:
  - Browser = Customer (makes requests)
  - CDN = Host (routes to the right table)
  - Server = Kitchen (prepares the food)
  - Database = Pantry (stores ingredients)
  - Cache = Warming tray (keeps popular dishes ready)
```

**Rules**:
- Use ONE consistent spatial analogy per architecture (don't mix metaphors)
- Map every component to a spatial location
- Reference the spatial analogy when explaining connections: "Remember, the
  customer (browser) talks to the host (CDN), not directly to the kitchen (server)"

### 13. Weak Concept Feedback Loop

When recall sessions identify weak concepts (low ease or poor grades), those
concepts should be reinforced in the next lesson automatically.

**Flow**:
1. `recall` identifies weak concepts → stored in `review.json` (ease < 2.0 or grade < 3)
2. `continue` reads `review.json` before generating the lesson
3. Teacher weaves weak concepts into the lesson as:
   - Extra practice exercises (even from previous days)
   - Examples when teaching new related concepts
   - 5-10 min drilling at the start of the lesson
4. This creates a self-correcting learning loop

## Mode-Specific Calibration

### ⚡ Speed Mode (3 days)

**Bloom's levels**: 1-3 (Remember → Understand → Apply)
**Cognitive load**: Minimal — 3-4 chunks per lesson
**Daily structure**:
```
Morning:   New material (3-4 concepts max)
           → Active recall exercise after each concept
           → Feynman check: explain back
Afternoon: Interleaved practice (mix with previous days)
           → Build progressive demo
Evening:   Create flashcard-style summary (question format, not statement)
```

**Spaced review built into the course**:
```
Day 2 start: Active recall of Day 1 (5 min)
Day 3 start: Active recall of Day 1 + 2 (5 min)
Post-course: Review at Day 5, 8, 13, 21
```

**Visual requirements per lesson**:
- 1 architecture/concept diagram (Mermaid)
- 1 comparison table
- 1 project structure table
- 1 connection diagram (how concepts relate)

### 📚 Standard Mode (7 days)

**Bloom's levels**: 1-4 (Remember → Analyze)
**Cognitive load**: Moderate — 4-5 chunks per lesson
**Daily structure**:
```
Session 1: New material + active recall
Session 2: Hands-on practice + Feynman check
Session 3: Interleaved review of previous days
Session 4: Progressive demo building
```

**Spaced review**:
```
Each lesson starts with 10-min active recall of 2-days-ago material
Day 5: Comprehensive review of Days 1-4
Post-course: Review at Day 10, 17, 24, 38, 65
```

**Additional vs Speed**:
- Deeper "why" explanations
- Common pitfalls section
- Practice exercises with explicit acceptance criteria
- Bloom's level 4: Analyze (compare approaches, identify tradeoffs)

### 🔬 Deep Mode (14 days)

**Bloom's levels**: 1-6 (full spectrum)
**Cognitive load**: Full — but sequenced carefully
**Daily structure**:
```
Session 1: New material + elaborative interrogation ("why" questions)
Session 2: Source code analysis + design pattern identification
Session 3: Active recall + interleaved practice
Session 4: Progressive project work
Session 5: Feynman check — teach back to the AI
```

**Spaced review**:
```
Each lesson starts with 15-min active recall
Day 7: Comprehensive Week 1 review
Day 14: Full course review + gap identification
Post-course: Review at Day 17, 22, 29, 43, 70, 120
```

**Additional vs Standard**:
- Source code snippets showing internal implementation
- Design pattern analysis
- Performance considerations
- Bloom's levels 5-6: Evaluate + Create

### 🔍 Source Code Mode (open-ended)

**Bloom's levels**: 4-6 (Analyze → Create)
**Cognitive load**: High — requires solid prerequisites
**Structure**: Module-by-module deep dive
- Entry file → initialization flow
- Class hierarchy → function call chains
- Design patterns → why it was designed this way
- Architecture diagrams (Mermaid)

### 🎯 Interview Prep Mode (5 days)

**Bloom's levels**: 1-2 (Remember + Understand)
**Cognitive load**: Moderate — breadth over depth
**Daily structure**:
```
10 min: Cumulative active recall of all previous days
20 min: New concepts (5-6 per day, focus on definitions + contrasts)
15 min: Interview Q&A practice (from graph interview_qa)
10 min: Concept comparison drills (A vs B, when to use which)
5 min:  Update flashcards (Anki export at end of course)
```

**Content focus**:
- Definitions and one-liners (not deep implementation)
- Common interview questions (八股) per concept
- Comparison tables (X vs Y, when to use which)
- Pitfalls and gotchas (common trick questions)
- Code snippets for fill-in-the-blank recall

**Spaced review**:
```
Day 2 start: Active recall of Day 1 (10 min)
Day 3 start: Active recall of Days 1-2 (10 min)
Day 4 start: Active recall of Days 1-3 (10 min)
Day 5: Full review + weak-area drilling (no new material)
Post-course: Review at Day 7, 10, 15, 25
```

**Day 5 final**: Generate comprehensive cheat sheets + Anki export. No new material — pure review and weak-area drilling.

## Diagram Requirements

| Situation | Diagram Type | Mermaid Keyword |
|-----------|-------------|-----------------|
| Architecture overview | Flowchart | `flowchart TD/LR` |
| Request lifecycle | Sequence diagram | `sequenceDiagram` |
| Concept relationships | Class diagram | `classDiagram` |
| File structure mapping | Table | Markdown table |
| State transitions | State diagram | `stateDiagram-v2` |
| Timeline / evolution | Timeline | `timeline` |
| Concept dependency (learning order) | Flowchart | `flowchart LR` |

## References

- **`references/learning-science.md`** — Full evidence base with citations
- **`references/visual-learning.md`** — Visual learning techniques and diagram usage
- **`references/socratic.md`** — Socratic questioning method
- **`references/project-based.md`** — Project-driven learning method
- **`references/spaced-repetition.md`** — Spaced repetition with SM-2/FSRS intervals
