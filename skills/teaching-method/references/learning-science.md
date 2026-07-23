# Learning Science: Evidence Base

This document provides the scientific foundation for the teaching methodology. Every technique used in this framework is backed by peer-reviewed research.

## Evidence Strength Rating

| Rating | Meaning |
|--------|---------|
| **STRONG** | Multiple RCTs, meta-analyses, replicated findings |
| **MODERATE** | Some RCTs, consistent findings, more research needed |
| **INDIRECT** | No direct RCTs on the named technique, but built on proven principles |
| **WEAK** | Limited or conflicting evidence |

---

## 1. Spaced Repetition — STRONG

### The Forgetting Curve (Ebbinghaus, 1885)

Memory retention follows exponential decay: `R = e^(-t/S)`

| Time Elapsed | Retention (no review) |
|---|---|
| 20 min | ~58% |
| 1 hour | ~44% |
| 1 day | ~34% |
| 6 days | ~28% |
| 31 days | ~21% |

**Implication**: Without review, 66% of new knowledge is gone within 24 hours.

### Optimal Review Intervals (Cepeda et al., 2008)

Research-backed intervals for ~90% retention:

| Review # | Interval | Cumulative |
|----------|----------|------------|
| 1 | 1 day | 1 |
| 2 | 3 days | 4 |
| 3 | 7 days | 11 |
| 4 | 14 days | 25 |
| 5 | 30 days | 55 |
| 6 | 90 days | 145 |

### Spaced Repetition Algorithms

**SM-2 (SuperMemo 2)**: Used by traditional Anki. Ease factor starts at 2.5, adjusts based on recall quality. Problem: "ease hell" for difficult cards.

**FSRS (Free Spaced Repetition Scheduler)**: Modern algorithm (Ye, 2023), adopted by Anki v23.10+. Uses ML to personalize intervals. Model: Difficulty, Stability, Retrievability (DSR).

Forgetting curve: `R(t) = (1 + t/(9*S))^(-1)`

Recommended Anki settings for programming:
- Learning steps: `15m 1d 3d`
- Graduating interval: 4 days
- Easy interval: 7 days
- Interval modifier: 80-90%
- Maximum interval: 180 days

**References**:
- Ebbinghaus, H. (1885). *Über das Gedächtnis*
- Cepeda, N.J. et al. (2006, 2008). Distributed practice in recall tasks.
- Murre, J.M.J. & Dros, J. (2015). Replication and analysis of Ebbinghaus' forgetting curve.
- Ye, J. (2023). FSRS: A Modern Spaced Repetition Algorithm. open-spaced-repetition.org

---

## 2. Testing Effect / Active Recall — STRONG

Retrieving information from memory strengthens it far more than re-reading.

### Key Findings

**Roediger & Karpicke (2006)**: Students who practiced retrieval showed significantly better long-term retention than those who restudied. After 1 week, the testing group dramatically outperformed — even though the restudy group *felt* they learned more.

**Dunlosky et al. (2013)**: Rated practice testing as **"high utility"** — one of only two techniques to earn this rating.

### The Illusion of Competence

Re-reading feels like learning but produces weak, non-durable memory. Active recall feels hard but produces strong, durable memory.

| Method | 1-Week Retention | Effort During Study |
|--------|-----------------|-------------------|
| Re-reading | ~10-20% | Low (feels easy) |
| Highlighting | ~10-20% | Low (feels productive) |
| Active recall | ~50-70% | High (feels hard) |
| Spaced active recall | ~70-90% | Moderate (distributed) |

**References**:
- Roediger, H.L. & Karpicke, J.D. (2006). Test-Enhanced Learning. *Psychological Science*
- Dunlosky, J. et al. (2013). Improving Students' Learning With Effective Learning Techniques. *Psychological Science in the Public Interest*

---

## 3. Interleaving — STRONG

Mixing different topics during practice > practicing one topic at a time (blocked practice).

### Key Findings

**Rohrer & Taylor (2007)**: Students who interleaved math problems significantly outperformed blocked-practice students on delayed tests.

**Kornell & Bjork (2008)**: Showed interleaving benefits in artist-style identification.

### The Paradox

Interleaving feels harder during practice ("desirable difficulty"), leading learners to *prefer* blocked practice. This preference is itself an illusion of competence.

**References**:
- Rohrer, D. & Taylor, K. (2007). The shuffling of mathematics problems improves learning.
- Kornell, N. & Bjork, R.A. (2008). Learning concepts and categories: Is spacing the "enemy of induction"?
- Bjork, R.A. & Bjork, E.L. (1992). A new theory of disuse and an old theory of stimulus fluctuation.

---

## 4. Dual Coding Theory — STRONG

The mind processes information through two channels: verbal (language-based) and visual (imagery-based). Encoding in both simultaneously produces stronger memories.

### Key Findings

**Paivio (1971, 1986)**: Concrete words (activating both channels) are remembered better than abstract words.

**Picture Superiority Effect**: People remember pictures better than words, and pictures + text together beats either alone.

**Mayer's Multimedia Learning**: Combining text with relevant visuals improves learning outcomes vs. text-only.

**References**:
- Paivio, A. (1986). *Mental Representations: A Dual Coding Approach*
- Mayer, R.E. (2009). *Multimedia Learning*

---

## 5. Cognitive Load Theory — STRONG

Working memory is limited. Total load = Intrinsic + Extraneous + Germane.

| Load Type | Definition | Action |
|-----------|-----------|--------|
| Intrinsic | Inherent difficulty of material | Sequence to manage complexity |
| Extraneous | Load from poor instructional design | **MINIMIZE** |
| Germane | Productive effort toward schema construction | **MAXIMIZE** |

### Key Applications

- Don't teach React hooks + TypeScript generics + webpack simultaneously (high intrinsic load)
- Remove unnecessary UI clutter from tutorials (reduce extraneous load)
- Use worked examples to free cognitive resources for germane processing

**References**:
- Sweller, J. (1988). Cognitive load during problem solving. *Cognitive Science*
- Sweller, J., van Merrienboer, J.J.G. & Paas, F.G.W.C. (1998). Cognitive architecture and instructional design. *Educational Psychology Review*

---

## 6. Chunking — STRONG

Working memory holds ~4 ± 1 chunks (Cowan, 2001). Grouping items into meaningful chunks expands effective capacity.

### Key Findings

**Miller (1956)**: "The Magical Number Seven, Plus or Minus Two" — original estimate of 7±2.

**Cowan (2001)**: Revised to **4 ± 1** when rehearsal strategies are controlled.

**Application**: Don't teach 15 individual API methods. Group into 3-4 chunks.

**References**:
- Miller, G.A. (1956). The Magical Number Seven, Plus or Minus Two. *Psychological Review*
- Cowan, N. (2001). The magical number 4 in short-term cognition.

---

## 7. Bloom's Taxonomy — STRONG (as framework)

Revised taxonomy (Anderson & Krathwohl, 2001):

| Level | Cognitive Process | Programming Example |
|-------|------------------|-------------------|
| 1. Remember | Retrieve from long-term memory | "List React lifecycle methods" |
| 2. Understand | Construct meaning | "Explain why useEffect runs after render" |
| 3. Apply | Carry out or use a procedure | "Build a todo app with useState" |
| 4. Analyze | Break into components, identify relationships | "Compare Redux vs Context API" |
| 5. Evaluate | Make judgments based on criteria | "Critique this architecture" |
| 6. Create | Put elements together to form a new whole | "Design a state management library" |

**References**:
- Anderson, L.W. & Krathwohl, D.R. (2001). *A Taxonomy for Learning, Teaching, and Assessing*
- Bloom, B.S. (1956). *Taxonomy of Educational Objectives*

---

## 8. Method of Loci — STRONG

Ancient mnemonic: visualize a familiar spatial environment and place items to remember at specific locations.

### Key Findings

**Dresler et al. (2017)**: Memory athletes showed different functional brain connectivity. After 6 weeks of training, naive participants developed similar patterns.

**Maguire et al. (2003)**: World Memory Championship competitors did NOT have structural brain differences — they used spatial memory strategies.

**Key insight**: Memory champions are "made, not born."

**References**:
- Dresler, M. et al. (2017). Mnemonic training reshapes brain networks. *Neuron*
- Maguire, E.A. et al. (2003). Routes to remembering. *Nature Neuroscience*

---

## 9. Feynman Technique — INDIRECT

4-step method: (1) Choose concept, (2) Explain simply, (3) Identify gaps, (4) Simplify.

Built on proven principles:
- **Protégé Effect**: Preparing to teach leads to better learning strategies
- **Generation Effect**: Explaining in own words strengthens encoding
- **Metacognition**: Forces confrontation with what you don't know

No direct RCTs on the named technique, but each component is evidence-based.

---

## 10. Zone of Proximal Development — STRONG (theoretical)

**Vygotsky (1978)**: The gap between what a learner can do alone and what they can achieve with guidance.

**Scaffolding** (Wood, Bruner & Ross, 1976): Temporary support that bridges the ZPD.

Application: Material should be challenging but achievable with guidance. Too easy = no growth. Too hard = frustration.

**References**:
- Vygotsky, L. (1978). *Mind in Society*
- Wood, D., Bruner, J.S. & Ross, G. (1976). The role of tutoring in problem solving.

---

## 11. Deliberate Practice — STRONG (with caveats)

**Ericsson et al. (1993)**: Best violinists had ~10,000 hours of deliberate practice by age 20.

**Macnamara et al. (2014)**: Meta-analysis — deliberate practice explains 26% of variance in games, 21% in music, 18% in sports, but only 4% in education.

**Key distinction**: Deliberate practice ≠ naive practice. Targeted practice on weakest skills with immediate feedback.

**References**:
- Ericsson, K.A., Krampe, R.T. & Tesch-Römer, C. (1993). The role of deliberate practice.
- Ericsson, K.A. & Pool, R. (2016). *Peak: Secrets from the New Science of Expertise*
- Macnamara, B.N., Hambrick, D.Z. & Oswald, F.L. (2014). Deliberate practice and performance.

---

## 12. Mastery Learning — MODERATE-STRONG

**Bloom (1968)**: Students progress only after demonstrating proficiency. Bloom's 2-Sigma Problem (1984): 1-on-1 tutoring + mastery-based instruction produced students 2 standard deviations above conventional classroom.

**References**:
- Bloom, B.S. (1968). Learning for Mastery. *Evaluation Comment*
- Bloom, B.S. (1984). The 2 Sigma Problem
- Kulik, C.C., Kulik, J.A. & Bangert-Drowns, R.L. (1990). Effectiveness of mastery learning programs.

---

## Technique Selection Guide

| Learning Goal | Best Techniques |
|--------------|----------------|
| Memorize API syntax | Spaced repetition + active recall + chunking |
| Understand architecture | Dual coding (diagrams + text) + elaborative interrogation |
| Build practical skills | Progressive projects + deliberate practice |
| Long-term retention | Spaced repetition + interleaving |
| Speed learning | 80/20 + chunking + Feynman technique |
| Deep understanding | Bloom's levels 4-6 + source code analysis |
