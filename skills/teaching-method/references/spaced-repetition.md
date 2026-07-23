# Spaced Repetition System

## The Science

Without review, 66% of new knowledge is forgotten within 24 hours (Ebbinghaus, 1885). Spaced repetition combats this by reviewing at optimal intervals — just before you're about to forget.

## Optimal Review Intervals (Cepeda et al., 2008)

Research-backed intervals targeting ~90% retention:

```
Review 1:  1 day    -> retention ~90%
Review 2:  3 days   -> retention ~85-90%
Review 3:  7 days   -> retention ~85-90%
Review 4:  14 days  -> retention ~85-90%
Review 5:  30 days  -> retention ~85-90%
Review 6:  90 days  -> retention ~85-90%
```

Each successful review exponentially increases the interval.

## SM-2 Algorithm (Traditional Anki)

```
New card:     1 day after first review
After "Good": new_interval = old_interval x ease_factor
Ease factor:  starts at 2.5, adjusts based on recall quality
```

Rating effects:
- **Again** (fail): interval resets to 1 day
- **Hard**: interval x 1.2, ease decreases
- **Good**: interval x ease_factor
- **Easy**: interval x ease_factor x 1.3, ease increases

Problem: "Ease hell" — difficult cards get stuck with low ease.

## FSRS Algorithm (Modern Anki 23.10+)

Uses machine learning to personalize intervals from your review history.

**DSR Model**: D (Difficulty), S (Stability, days until retrievability drops to 90%), R (Retrievability, current recall probability).

**Forgetting curve**: `R(t) = (1 + t/(9*S))^(-1)`

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

- **3-Day Speed**: Day 2 recalls Day 1; Day 3 recalls Day 1+2. Post-course: Day 5, 8, 13, 21.
- **7-Day Standard**: each lesson recalls 2-days-ago; Day 5 comprehensive. Post-course: Day 10, 17, 24, 38, 65.
- **14-Day Deep**: 15-min recall each lesson; Day 7 + Day 14 comprehensive. Post-course: Day 17, 22, 29, 43, 70, 120.

## Flashcard Design Rules

### DO
- Ask questions, not statements: "How do you set up a Redux store?"
- Use fill-in-the-blank for code: `const [state, ____] = useState(initial)`
- One concept per card (chunking)

### DON'T
- Don't use multiple choice (passive recognition != active recall)
- Don't put entire paragraphs on a card
- Don't create cards for things you already know well

## References

- Ebbinghaus, H. (1885). Uber das Gedachtnis
- Cepeda, N.J. et al. (2006, 2008). Distributed practice research
- Ye, J. (2023). FSRS algorithm. open-spaced-repetition.org
- Dunlosky, J. et al. (2013). Effective learning techniques.
