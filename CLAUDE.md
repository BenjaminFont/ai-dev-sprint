# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a documentation and template repository for AI-assisted development workflows. It contains Claude Code configuration templates, cheatsheets, and methodology guides for specs and implementation plans.

## Repository Structure

```
.claude/
├── agents/        # Subagent definitions (planner, specificator)
├── commands/      # Slash commands (/spec, /plan)
├── rules/         # Methodology guides (how-to-write-specs.md, how-to-write-plans.md)
specs/             # Feature specs and implementation plans go here
cheatsheet/        # Reference guides for CLI tools
```

## Workflow

### Creating Specs and Plans

1. **Specs first**: Use `/spec <feature>` to create a specification describing WHAT and WHY
2. **Plans second**: Use `/plan <spec>` to create an implementation plan describing HOW and WHEN
3. **Store in specs/**: Save specs as `specs/<feature>.md`, plans as `specs/<feature>-implementation-plan.md`

### Key Methodology

- Specs are 1-2 pages max, focus on guardrails and constraints
- Plans describe implementation steps, not task checklists
- Both reference the rule files in `.claude/rules/` for detailed guidance

## Conventions

- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`
- No emojis in code, comments, or documentation
- Prefer immutability
- File size: 200-400 lines typical, 800 max
