---
description: Generate a detailed implementation plan for a feature based on a spec file
---

## Variables
SPEC: $ARGUMENT0 - IMPORTANT: If no spec is provided, stop and ask for one.
THINKING-MODE: $ARGUMENT1 or "think hard" if not specified

## Instructions

Follow the planning methodology in `.claude/rules/how-to-write-plans.md`.

## Workflow

1. [THINKING-MODE]

2. **Understand Context**
   - Read the spec file for [SPEC] if it exists in `specs/`
   - Ask clarifying questions about the implementation approach if needed

3. **Explore the Codebase**
   - Find affected files and components using Glob/Grep
   - Review existing implementations for patterns to follow
   - Identify integration points and dependencies

4. **Draft the Plan**
   - Create implementation steps with specific files and dependencies
   - Document key decision points explaining approach choices
   - Include testing strategy hints
   - Flag risks and unknowns

5. **Output**
   - Save the plan to `specs/[feature-name]-implementation-plan.md`

## Guiding Principles

- **Explore first**: Don't plan in a vacuum — read the codebase.
- **Prefer extending**: Minimize changes; extend existing code over rewriting.
- **Follow conventions**: Match existing project patterns.
- **Stay practical**: Enable confident, incremental implementation.
- **Adapt**: Flag unknowns rather than overspecifying.  


