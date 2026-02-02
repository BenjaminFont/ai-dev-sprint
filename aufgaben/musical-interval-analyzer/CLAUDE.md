# Musical Interval Analyzer

## Project Description

A Java application that calculates the musical interval between two piano notes.

- **Input**: Two notes (e.g. "C", "D")
- **Output**: The musical interval as a number (e.g. "1")

### Domain Knowledge

- The interval between any two adjacent keys on the piano (regardless of color) is 0.5 (a semitone).
- White keys: C, D, E, F, G, A, B
- Black keys are notated with # (sharp) or b (flat)

## Tech Stack

- Java 21
- Maven
- JUnit 5 + AssertJ for testing

## Package Structure

- `interval` -- main package
- Source: `src/main/java/interval/`
- Tests: `src/test/java/interval/`

## Development Methodology

This project follows strict TDD (Test-Driven Development) using the `/tdd-java` skill.
See `.claude/skills/tdd-java/SKILL.md` for the full TDD rules.

## Task Progression

Tasks are defined in individual step files under `tasks/`. Each step builds on the previous one.
**Only read the current step file.** Do not read ahead to future steps.

Current step tracking: check `tasks/CURRENT_STEP.md` for which step to work on.

## Conventions

- Conventional commits: `feat:`, `fix:`, `refactor:`, `test:`
- No emojis
- Test names describe behavior: `shouldReturnX_whenY`
- One assertion per test
