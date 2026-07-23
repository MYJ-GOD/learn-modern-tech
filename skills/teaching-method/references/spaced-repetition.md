# Spaced Repetition System

## The Science

Without review, 66% of new knowledge is forgotten within 24 hours (Ebbinghaus, 1885). Spaced repetition combats this by reviewing at optimal intervals — just before you're about to forget.

## Optimal Review Intervals (Cepeda et al., 2008)

Research-backed intervals targeting ~90% retention:

```
Review 1:  1 day    → retention ~90%
Review 2:  3 days   → retention ~85-90%
Review 3:  7 days   → retention ~85-90%
Review 4:  14 days  → retention ~85-90%
Review 5:  30 days  → retention ~85-90%
Review 6:  90 days  → retention ~85-90%
```

Each successful review exponentially increases the interval. This is why spaced repetition is the most efficient study technique known to science.

## SM-2 Algorithm (Traditional Anki)

How intervals are calculated:

```
New card:     1 day after first review
After "Good": new_interval = old_interval × ease_factor
Ease factor:  starts at 2.5, adjusts based on recall quality
```

Rating effects:
- **Again** (fail): interval resets to 1 day
- **Hard**: interval × 1.2, ease decreases
- **Good**: interval × ease_factor
- **Easy**: interval × ease_factor × 1.3, ease increases

Problem: "Ease hell" — difficult cards get stuck with low ease, causing disproportionate reviews.

## FSRS Algorithm (Modern Anki 23.10+)

Uses machine learning to personalize intervals from your review history.

**DSR Model**:
- **D (Difficulty)**: Intrinsic difficulty of the card (0-1 scale)
- **S (Stability)**: How long memory lasts (days) — time at which retrievability drops to 90%
- **R (Retrievability)**: Current probability of successful recall

**Forgetting curve**: `R(t) = (1 + t/(9*S))^(-1)`

**Optimal interval**: `I(r) = S/9 * (r^(-1/f) - 1)` where f ~ 1 and r = target retention (default 0.9)

**Advantage**: Uses ~19 personalized parameters learned from your actual review data. Targets a user-specified retention rate.

## Recommended Anki Settings for Programming

```
Learning steps:     15m 1d 3d
Graduating interval: 4 days
Easy interval:       7 days
Interval modifier:   80-90%
Maximum interval:    180 days
Algorithm:           FSRS (if Anki 23.10+)
```

## Integration with Course Modes

### 3-Day Speed Course

```
Day 1: Learn 3-4 core concepts
       → Create flashcard-style summaries (question format)
       → Evening: quick self-test

Day 2: START with 5-min active recall of Day 1
       → Learn next 3-4 concepts
       → Interleaved practice (mix Day 1 + Day 2)
       → Update flashcards

Day 3: START with 5-min active recall of Day 1+2
       → Apply all concepts in project
       → Final self-test
       → Gap identification

Post-course review schedule:
  Day 5  → Review all (20 min)
  Day 8  → Review weak areas (15 min)
  Day 13 → Quick review (10 min)
  Day 21 → Maintenance check (5 min)
```

### 7-Day Standard Course

```
Each lesson starts with 10-min active recall of 2-days-ago material
Day 5: Comprehensive review of Days 1-4
Day 7: Full course review

Post-course:
  Day 10, 17, 24, 38, 65
```

### 14-Day Deep Course

```
Each lesson starts with 15-min active recall
Day 7: Week 1 comprehensive review
Day 14: Full course review + gap identification

Post-course:
  Day 17, 22, 29, 43, 70, 120
```

## Flashcard Design Rules

### DO

- Ask questions, not statements: "How do you set up a Redux store?" not "Redux setup: 1, 2, 3"
- Use fill-in-the-blank for code: `const [state, ____] = useState(initial)`
- One concept per card (chunking)
- Include context: "In React hooks, what does..."

### DON'T

- Don't use multiple choice (passive recognition ≠ active recall)
- Don't put entire paragraphs on a card
- Don't create cards for things you already know well
- Don't review cards you can't answer by reading the answer — skip and come back

## References

- Ebbinghaus, H. (1885). *Über das Gedächtnis*
- Cepeda, N.J. et al. (2006, 2008). Distributed practice research
- Ye, J. (2023). FSRS algorithm. open-spaced-repetition.org
- Dunlosky, J. et al. (2013). Effective learning techniques. *Psychological Science in the Public Interest*
