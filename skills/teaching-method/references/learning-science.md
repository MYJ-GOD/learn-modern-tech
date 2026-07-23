# Learning Science: Evidence Base

This document provides the scientific foundation for the teaching methodology. Every technique used in this framework is backed by peer-reviewed research.

## Evidence Strength Rating

| Rating | Meaning |
|--------|---------|
| **STRONG** | Multiple RCTs, meta-analyses, replicated findings |
| **MODERATE** | Some RCTs, consistent findings, more research needed |
| **INDIRECT** | No direct RCTs on the named technique, but built on proven principles |

---

## 1. Spaced Repetition — STRONG

Memory retention follows exponential decay (Ebbinghaus, 1885): `R = e^(-t/S)`. Without review, 66% of new knowledge is gone within 24 hours. Optimal intervals (Cepeda et al., 2008): 1, 3, 7, 14, 30, 90 days for ~90% retention.

**SM-2**: ease factor starts at 2.5. **FSRS** (Ye, 2023, Anki 23.10+): ML-personalized, DSR model, `R(t) = (1 + t/(9*S))^(-1)`.

## 2. Testing Effect / Active Recall — STRONG

Retrieving from memory strengthens it far more than re-reading (Roediger & Karpicke, 2006). Rated "high utility" by Dunlosky et al. (2013).

| Method | 1-Week Retention | Effort |
|--------|-----------------|--------|
| Re-reading | ~10-20% | Low (feels easy) |
| Active recall | ~50-70% | High (feels hard) |
| Spaced active recall | ~70-90% | Moderate |

## 3. Interleaving — STRONG

Mixing topics during practice beats blocked practice (Rohrer & Taylor, 2007; Kornell & Bjork, 2008). Feels harder ("desirable difficulty") but produces better long-term retention.

## 4. Dual Coding Theory — STRONG

Verbal + visual channels encoded together produce stronger memories (Paivio, 1986; Mayer, 2009). Picture superiority effect.

## 5. Cognitive Load Theory — STRONG

Total load = Intrinsic + Extraneous + Germane (Sweller, 1988). Minimize extraneous, maximize germane, sequence to manage intrinsic.

## 6. Chunking — STRONG

Working memory holds ~4 +/- 1 chunks (Cowan, 2001; revised from Miller's 7+/-2, 1956). Group 15 API methods into 3-4 chunks.

## 7. Bloom's Taxonomy — STRONG (as framework)

Anderson & Krathwohl (2001): 1. Remember, 2. Understand, 3. Apply, 4. Analyze, 5. Evaluate, 6. Create.

## 8. Method of Loci — STRONG

Spatial mnemonic. Memory champions are "made, not born" (Dresler et al., 2017; Maguire et al., 2003).

## 9. Feynman Technique — INDIRECT

Explain simply -> identify gaps -> simplify. Built on Protege Effect, Generation Effect, Metacognition.

## 10. Zone of Proximal Development — STRONG (theoretical)

Vygotsky (1978): gap between solo and guided ability. Scaffolding (Wood, Bruner & Ross, 1976).

## 11. Deliberate Practice — STRONG (with caveats)

Ericsson et al. (1993). Macnamara et al. (2014) meta-analysis: explains 26% variance in games, 4% in education. Targeted practice on weak skills with feedback.

## 12. Mastery Learning — MODERATE-STRONG

Bloom (1968, 1984): 2-Sigma Problem — 1-on-1 tutoring + mastery instruction produced students 2 SD above conventional classroom.

## Technique Selection Guide

| Learning Goal | Best Techniques |
|--------------|----------------|
| Memorize API syntax | Spaced repetition + active recall + chunking |
| Understand architecture | Dual coding + elaborative interrogation |
| Build practical skills | Progressive projects + deliberate practice |
| Long-term retention | Spaced repetition + interleaving |
| Speed learning | 80/20 + chunking + Feynman technique |
| Deep understanding | Bloom's levels 4-6 + source code analysis |
