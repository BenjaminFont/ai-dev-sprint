# Claude Code Memory Cheatsheet

## Was ist Memory?

Markdown-Dateien die Claude beim Start liest -- Projektregeln, persoenliche Praeferenzen, Team-Konventionen. Persistiert ueber Sessions hinweg.

## Memory-Hierarchie

Prioritaet von hoch nach niedrig. Hoehere ueberschreiben niedrigere.

| Typ | Speicherort | Geteilt mit |
|-----|-------------|-------------|
| **Managed Policy** | `/Library/Application Support/ClaudeCode/CLAUDE.md` (macOS) | Alle User (Organisation) |
| **Project Memory** | `./CLAUDE.md` oder `./.claude/CLAUDE.md` | Team (via Git) |
| **Project Rules** | `./.claude/rules/*.md` | Team (via Git) |
| **User Memory** | `~/.claude/CLAUDE.md` | Nur du (alle Projekte) |
| **Local Memory** | `./CLAUDE.local.md` | Nur du (dieses Projekt) |

`CLAUDE.local.md` wird automatisch in `.gitignore` aufgenommen.

## Wann was verwenden

| Ich will... | Verwende |
|-------------|----------|
| Team-Coding-Standards definieren | `./CLAUDE.md` |
| Regeln nach Thema aufteilen | `.claude/rules/*.md` |
| Meine persoenlichen Praeferenzen | `~/.claude/CLAUDE.md` |
| Meine lokalen Sandbox-URLs / Testdaten | `./CLAUDE.local.md` |
| Firmenweite Policies ausrollen | Managed Policy |

## Quick Start

```bash
# Projekt-Memory bootstrappen
/init

# Memory-Dateien bearbeiten
/memory

# Geladene Memory-Dateien anzeigen
/context
```

## CLAUDE.md schreiben

```markdown
# Projekt-Kontext

## Build & Test
- `npm run build` zum Bauen
- `npm test` fuer alle Tests
- `npm run lint` fuer Linting

## Konventionen
- Conventional Commits: feat:, fix:, refactor:, docs:
- Max 400 Zeilen pro Datei
- Immutability bevorzugen
```

Spezifisch sein. "2-Space Indentation" statt "Code sauber formatieren".

## Project Rules

Modulare, themenspezifische Regeln in `.claude/rules/`. Werden automatisch beim Start geladen (gleiche Prioritaet wie `.claude/CLAUDE.md`).

```
.claude/rules/
├── frontend/
│   ├── react.md
│   └── styles.md
├── backend/
│   ├── api.md
│   └── database.md
└── testing.md
```

Alle `.md`-Dateien werden rekursiv entdeckt. Subdirectories und Symlinks funktionieren.

### Path-spezifische Rules

Rules koennen per Frontmatter auf bestimmte Dateien eingeschraenkt werden. Diese werden erst geladen wenn Claude an matchenden Dateien arbeitet.

```markdown
---
paths:
  - "src/api/**/*.ts"
---
# API Rules
- Input Validation an allen Endpoints
- Standard Error-Response-Format verwenden
```

| Pattern | Matcht |
|---------|--------|
| `**/*.ts` | Alle .ts Dateien in allen Verzeichnissen |
| `src/**/*` | Alles unter src/ |
| `src/**/*.{ts,tsx}` | .ts und .tsx unter src/ |
| `{src,lib}/**/*.ts` | .ts unter src/ oder lib/ |

Rules ohne `paths` gelten immer. Mit `paths` nur on-demand.

### User-Level Rules

Persoenliche Rules die fuer alle Projekte gelten:

```
~/.claude/rules/
├── preferences.md
└── workflows.md
```

Werden vor Project Rules geladen (niedrigere Prioritaet).

## Imports

CLAUDE.md Dateien koennen andere Dateien importieren mit `@path`:

```markdown
Siehe @README fuer Projekt-Uebersicht und @package.json fuer npm Commands.

# Workflow
- Git Workflow: @docs/git-instructions.md
- Persoenliche Prefs: @~/.claude/my-project-instructions.md
```

- Relative und absolute Pfade
- Rekursive Imports (max 5 Ebenen tief)
- Werden NICHT in Code-Blocks oder Inline-Code ausgewertet

## Rekursive Suche

Claude sucht CLAUDE.md und CLAUDE.local.md rekursiv aufwaerts ab dem cwd bis zur Root. Arbeitet man in `foo/bar/`, werden beide geladen:
- `foo/CLAUDE.md`
- `foo/bar/CLAUDE.md`

CLAUDE.md in Unterverzeichnissen werden on-demand geladen wenn Claude dort Dateien liest.

## Zusaetzliche Verzeichnisse

```bash
# Memory aus zusaetzlichem Verzeichnis laden
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 claude --add-dir ../shared-config
```

## Best Practices

**Do:**
- Haeufig genutzte Commands dokumentieren (build, test, lint)
- Architektur-Patterns und Namenskonventionen festhalten
- Rules nach Thema aufteilen (eine Datei pro Thema)
- Spezifisch sein ("tabs, 4 spaces" statt "gut formatieren")

**Don't:**
- Riesige Monolith-CLAUDE.md (Rules nutzen zum Aufteilen)
- Offensichtliches dokumentieren das Claude ohnehin weiss
- Veraltete Informationen stehen lassen -- regelmaessig pruefen
