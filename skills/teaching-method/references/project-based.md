# Project-Based Learning

## Core Idea

Learning is NOT "finish all theory first, then do a project." It's **start the project from Day 1**, add one feature each day, and learn through building.

## Progressive Demo Design

### Design Principles

1. **Day 1 must run**: The first demo must be minimal but runnable
2. **Daily increment**: Not independent examples — each day builds on the previous
3. **End result is real**: After the course, the demo is a complete, usable project
4. **Feature priority**: Core features first, enhancements later

### Design Template

```
Day 1: Hello World (runnable)
  → Minimal runnable example
  → Understand project structure
  → Can change one line and see the effect

Day 2: Core Feature A
  → Add first real feature on Day 1's code
  → Introduce framework's core concept

Day 3: Core Feature B
  → Continue building
  → Introduce second core concept
  → Modules start interacting

Day 4-5: Enhancement
  → Data persistence
  → User interaction
  → Error handling

Day 6: Best Practices
  → Code refactoring
  → Performance optimization
  → Testing

Day 7: Deploy + Summary
  → Runs in production
  → Retrospective on the whole project
```

### Example: Next.js Full-Stack Blog

```
Day 1: Pages + Routing (App Router)
Day 2: Static content + Markdown rendering
Day 3: Database (Prisma + SQLite)
Day 4: CRUD (Server Actions)
Day 5: Authentication (NextAuth)
Day 6: AI Summary (OpenAI SDK)
Day 7: Deploy to Vercel
```

## Progressive Demo vs Traditional Teaching

| Traditional | Project-Based |
|-------------|--------------|
| Learn all concepts first | Write code from Day 1 |
| Theory separated from practice | Practice IS the learning |
| "What can I do with this?" | Build something real |
| Easy to quit halfway | Daily progress feels rewarding |

## Notes

- Demo cannot be too complex. Day 1 must be simple enough to run.
- Feature selection should be practical: most commonly used, not most flashy.
- Each day's demo code must be independently runnable — don't depend on future days.
