# Task Progression Rules

## CRITICAL: Do Not Read Ahead

- ONLY read the step file indicated in `tasks/CURRENT_STEP.md`
- NEVER read future step files (e.g. if current step is 03, do not read step-04.md or beyond)
- NEVER glob or list all step files to read their contents
- NEVER summarize or reference steps you haven't reached yet
- Each step is intentionally isolated to preserve the learning progression

## Why This Matters

This project is designed as a progressive TDD exercise. Reading ahead defeats the purpose by:
- Influencing design decisions with future knowledge
- Breaking the incremental design approach that TDD enables
- Removing the learning opportunity of discovering requirements step by step

## How to Progress

1. Read `tasks/CURRENT_STEP.md` to find the current step number
2. Read only that step file
3. Complete the step using TDD
4. Update `CURRENT_STEP.md` to the next step number
5. Repeat