# Claude Code Memory Cheatsheet

## Was ist Memory?

Markdown-Dateien die Claude beim Start liest -- Projektregeln, persoenliche Praeferenzen, Team-Konventionen. Persistiert ueber Sessions hinweg.

## Memory-Hierarchie

Prioritaet von hoch nach niedrig. Hoehere ueberschreiben niedrigere.

| Typ | Speicherort | Geteilt mit |
|-----|-------------|-------------|
| **Managed Policy** | macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md`<br>Windows: `%ProgramData%\ClaudeCode\CLAUDE.md`<br>Linux: `/etc/claude/CLAUDE.md` | Alle User (Organisation) |
| **Project Memory** | `./CLAUDE.md` oder `./.claude/CLAUDE.md` (beide gleichwertig) | Team (via Git) |
| **Project Rules** | `./.claude/rules/*.md` (OHNE `paths:` gelten immer) | Team (via Git) |
| **User Memory** | `~/.claude/CLAUDE.md` | Nur du (alle Projekte) |
| **User Rules** | `~/.claude/rules/*.md` | Nur du (alle Projekte) |
| **Local Memory** | `./CLAUDE.local.md` | Nur du (dieses Projekt) |
| **Auto Memory** | `~/.claude/projects/<project>/memory/` | Automatische Notizen |

`CLAUDE.local.md` wird automatisch in `.gitignore` aufgenommen.

**WICHTIG**: User-Level Memory/Rules haben NIEDRIGERE Priorität als Project Memory/Rules!

## Wann was verwenden

| Ich will... | Verwende |
|-------------|----------|
| Team-Coding-Standards definieren | `./CLAUDE.md` |
| Regeln nach Thema aufteilen | `.claude/rules/*.md` |
| Meine persoenlichen Praeferenzen | `~/.claude/CLAUDE.md` |
| Meine lokalen Sandbox-URLs / Testdaten | `./CLAUDE.local.md` |
| Firmenweite Policies ausrollen | Managed Policy |

## Auto Memory (NEU)

Claude lernt automatisch aus Fehlern und speichert Erkenntnisse:

```
~/.claude/projects/<project>/memory/
├── MEMORY.md          # Hauptnotizen (max 200 Zeilen, wird immer geladen)
└── debugging.md       # Topic Files (nach Bedarf referenziert)
```

```bash
# Auto Memory browsen/bearbeiten
/memory

# Deaktivieren
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1 claude
```

**Best Practice**: MEMORY.md unter 200 Zeilen halten, Details in Topic Files auslagern.

## Quick Start

```bash
# Projekt-Memory bootstrappen
/init

# Memory browsen/bearbeiten (inkl. Auto Memory)
/memory

# Geladene Memory-Dateien anzeigen
/context
```

## CLAUDE.md schreiben

**Ziel**: Unter 200 Zeilen bleiben (Context-Kosten!)

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

## Architektur
Details: @docs/architecture.md
```

**Best Practices**:
- Spezifisch sein: "2-Space Indentation" statt "Code sauber formatieren"
- Lange Details in separate Dateien mit `@path` importieren
- Regelmaessig pruefen und veraltete Infos entfernen

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

Rules **OHNE** `paths:` werden IMMER geladen.
Rules **MIT** `paths:` werden nur on-demand geladen, wenn Claude an matchenden Dateien arbeitet.

```markdown
---
paths:
  - "src/api/**/*.ts"
  - "lib/api/**/*.{ts,tsx}"
---
# API Rules
- Input Validation an allen Endpoints
- Standard Error-Response-Format verwenden
```

| Pattern | Matcht |
|---------|--------|
| `**/*.ts` | Alle .ts Dateien in allen Verzeichnissen |
| `src/**/*` | Alles unter src/ |
| `src/**/*.{ts,tsx}` | .ts und .tsx unter src/ (Brace Expansion) |
| `{src,lib}/**/*.ts` | .ts unter src/ oder lib/ |

**Symlinks** in `.claude/rules/` werden supported (circular detection).

### User-Level Rules

Persoenliche Rules die fuer alle Projekte gelten:

```
~/.claude/rules/
├── preferences.md
└── workflows.md
```

**Haben NIEDRIGERE Priorität als Project Rules** (werden vor Project Rules geladen, aber Project Rules überschreiben sie).

## Imports

CLAUDE.md Dateien koennen andere Dateien importieren mit `@path`:

```markdown
Siehe @README fuer Projekt-Uebersicht und @package.json fuer npm Commands.

# Workflow
- Git Workflow: @docs/git-instructions.md
- Persoenliche Prefs: @~/.claude/my-project-instructions.md
```

**Verhalten**:
- Relative und absolute Pfade (relative resolven zum Source-File)
- Imports werden EXPANDED (Inhalt eingebettet, nicht nur referenziert)
- Rekursive Imports (max 5 Ebenen tief)
- Approval-Dialog beim ersten Import (Security)
- Werden NICHT in Code-Blocks oder Inline-Code ausgewertet

## Rekursive Suche

Claude sucht CLAUDE.md und CLAUDE.local.md rekursiv aufwaerts ab dem cwd bis zur Root. Arbeitet man in `foo/bar/`, werden beide geladen:
- `foo/CLAUDE.md`
- `foo/bar/CLAUDE.md`

CLAUDE.md in Unterverzeichnissen werden on-demand geladen wenn Claude dort Dateien liest.

## Zusaetzliche Verzeichnisse & Managed Policies

```bash
# Memory aus zusaetzlichem Verzeichnis laden
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 claude --add-dir ../shared-config
```

### Managed Policies (Enterprise)

Admins können bestimmte CLAUDE.md files excluden:

```json
// Managed Policy settings
{
  "claudeMdExcludes": ["**/CLAUDE.local.md", "team-a/**"]
}
```

**WICHTIG**: Managed Policy CLAUDE.md selbst kann nicht excluded werden.

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
