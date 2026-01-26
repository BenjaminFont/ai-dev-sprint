# Claude Code Cheatsheet

## CLI Starten

```bash
claude                      # Interaktive Session
claude "prompt"             # Session mit Prompt starten
claude -p "prompt"          # Non-interaktiv, Output und Exit
claude -c                   # Letzte Session fortsetzen
claude -r "session-name"    # Spezifische Session fortsetzen
claude --model opus         # Model wählen (opus, sonnet, haiku)
```

## Slash Commands

| Befehl | Beschreibung |
|--------|--------------|
| `/help` | Hilfe anzeigen |
| `/init` | Projekt mit CLAUDE.md initialisieren |
| `/clear` | Konversation löschen |
| `/compact [focus]` | Kontext komprimieren |
| `/context` | Context-Nutzung visualisieren |
| `/cost` | Token-Verbrauch anzeigen |
| `/config` | Einstellungen öffnen |
| `/model` | Model wechseln |
| `/memory` | CLAUDE.md bearbeiten |
| `/mcp` | MCP Server verwalten |
| `/resume` | Session-Picker öffnen |
| `/rename <name>` | Session umbenennen |
| `/export [file]` | Konversation exportieren |
| `/plan` | Plan Mode aktivieren |
| `/vim` | Vim Mode aktivieren |
| `/tasks` | Background Tasks anzeigen |
| `/plugin` | Plugin Manager öffnen |

## Keyboard Shortcuts

| Shortcut | Aktion |
|----------|--------|
| `Ctrl+C` | Abbrechen |
| `Ctrl+L` | Terminal leeren |
| `Ctrl+R` | History durchsuchen |
| `Ctrl+O` | Verbose Output togeln |
| `Ctrl+G` | Prompt im Editor öffnen |
| `Ctrl+B` | Task in Background |
| `Esc Esc` | Rewind (Code/Konversation zurücksetzen) |
| `Shift+Tab` | Permission Mode wechseln |
| `Option+P` | Model wechseln (ohne Prompt zu löschen) |
| `\+Enter` | Mehrzeilige Eingabe |

## CLI Flags

```bash
# Output & Format
--output-format json|text|stream-json
--json-schema '{"type":"object"}'   # Validiertes JSON Output

# Tools einschränken
--tools "Bash,Edit,Read"            # Nur diese Tools
--allowedTools "Bash(git:*)"        # Auto-approve Pattern
--disallowedTools "Edit"            # Tools blockieren

# System Prompt
--append-system-prompt "text"       # Instruktionen anhängen
--system-prompt "text"              # System Prompt ersetzen

# Limits
--max-turns 5                       # Max Agentic Turns
--max-budget-usd 10                 # Kostenlimit

# Session
--continue                          # Letzte Session fortsetzen
--resume "id"                       # Spezifische Session
```

## Konfiguration

### Dateien (Priorität: hoch → niedrig)

| Datei | Beschreibung |
|-------|--------------|
| `.claude/settings.local.json` | Lokal, gitignored |
| `.claude/settings.json` | Projekt-Settings |
| `~/.claude/settings.json` | User-Settings |
| `.claude/CLAUDE.md` | Projekt-Kontext |
| `~/.claude/CLAUDE.md` | Persönliche Präferenzen |

### settings.json Beispiel

```json
{
  "model": "claude-sonnet-4-5-20250929",
  "permissions": {
    "allow": ["Bash(npm:*)", "Bash(git:*)"],
    "deny": ["Bash(rm -rf:*)"]
  },
  "env": {
    "NODE_ENV": "development"
  }
}
```

### Permission Patterns

```
Bash(npm run:*)      # Prefix-Match
Bash(git*)           # Glob-Match
Read(./src/**)       # Rekursiv
```

## MCP Server

```bash
# HTTP Server hinzufügen
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Stdio Server
claude mcp add --transport stdio --env KEY=val name -- npx package

# Verwalten
claude mcp list                     # Alle Server listen
claude mcp remove <name>            # Server entfernen
/mcp                                # Status prüfen (in Session)
```

## Plugins

```bash
/plugin                         # Plugin Manager öffnen (UI)
```

**Tabs:** Discover | Installed | Marketplaces | Errors

```bash
# Installation
claude plugin install <name>                  # User-Scope (default)
claude plugin install <name> --scope project  # Projekt-Scope

# Verwalten
claude plugin uninstall <name>
claude plugin enable <name>
claude plugin disable <name>
claude plugin update <name>

# Marketplaces
claude plugin marketplace add anthropics/claude-code
claude plugin marketplace list
claude plugin marketplace remove <name>
```

**Aufruf:** `/plugin-name:skill-name [args]` (namespaced)

## Skills (Custom Commands)

Erstelle: `~/.claude/skills/skill-name/SKILL.md`

```yaml
---
name: my-skill
description: Beschreibung wann der Skill verwendet wird
allowed-tools: Read, Bash
---

Anweisungen für den Skill...
```

Aufruf: `/my-skill [args]`

## Praktische Patterns

```bash
# Code Review
cat file.py | claude -p "Review diesen Code"

# Git Diff analysieren
git diff | claude -p "Erkläre die Änderungen"

# Strukturiertes Output
claude -p "Liste alle Funktionen" --output-format json

# Projekt erkunden
claude
> @src/main.ts analysieren
> Wie funktioniert die Auth?
```

## Environment Variables

```bash
ANTHROPIC_API_KEY          # API Key
ANTHROPIC_MODEL            # Default Model
CLAUDE_CODE_DISABLE_TELEMETRY=1  # Telemetrie aus
```

## Permission Modes

| Mode | Verhalten |
|------|-----------|
| Default | Fragt bei jedem Tool |
| Plan Mode | Erst planen, dann genehmigen |
| Auto-Accept | Alles automatisch erlauben |
| Don't Ask | Nicht-erlaubte Tools ablehnen |

Wechseln: `Shift+Tab` oder `Alt+M`
