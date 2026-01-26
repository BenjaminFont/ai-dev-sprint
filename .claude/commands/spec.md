---
description: Generate a detailed specification for a feature through collaboration with the engineer
---

## Variables
FEATURE: $ARGUMENT0 - IMPORTANT: If no feature is described, stop and ask for one.
THINKING-MODE: $ARGUMENT1 or "think hard" if not specified

## Instructions

Follow the spec methodology in `.claude/rules/how-to-write-specs.md`.

## Workflow

1. [THINKING-MODE]

2. **Gather Requirements**
   - Ask clarifying questions about [FEATURE]
   - Understand the primary use case and why it's needed
   - Identify success criteria and constraints (security, performance, compatibility)
   - Clarify scope boundaries — what should NOT be included

3. **Explore the Codebase**
   - Find similar existing features for patterns using Glob/Grep
   - Identify where this feature integrates
   - Discover architectural constraints that affect the design

4. **Draft the Spec**
   - Overview (2-4 sentences: what and why)
   - Key Constraints & Design Decisions
   - Usage examples (1-2 concrete examples)
   - Testing (success criteria and key scenarios)

5. **Output**
   - Save the spec to `specs/[feature-name].md`
   - Update `specs/README.md` if it exists

## Guiding Principles

- **Collaborate**: Specs emerge from conversation — ask questions, don't assume.
- **Stay brief**: 1-2 pages max. If longer, it's too detailed.
- **Focus on guardrails**: Constraints and decisions that prevent mistakes.
- **Be concrete**: Use real examples, not abstract descriptions.
- **Avoid implementation**: No code snippets, file paths, or step-by-step instructions.

## Questions to Consider

- What problem does this solve for the user?
- What's the expected input and output?
- Are there security or performance requirements?
- How will we know it's working correctly?  


