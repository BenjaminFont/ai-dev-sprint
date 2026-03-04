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

### Häufig genutzt
| Befehl | Beschreibung |
|--------|--------------|
| `/help` | Hilfe anzeigen |
| `/login` / `/logout` | Authentifizierung |
| `/init` | Projekt mit CLAUDE.md initialisieren |
| `/review` | Pull Request reviewen (braucht `gh` CLI) |
| `/diff` | Interactive Diff Viewer |
| `/memory` | Auto Memory browsen/bearbeiten |

### Session Management
| Befehl | Beschreibung |
|--------|--------------|
| `/clear` | Konversation löschen |
| `/compact [focus]` | Kontext komprimieren |
| `/resume` | Session-Picker öffnen |
| `/rename <name>` | Session umbenennen |
| `/fork [name]` | Session forken |
| `/export [file]` | Konversation exportieren |
| `/rewind` | Zu Checkpoint zurück |

### Konfiguration
| Befehl | Beschreibung |
|--------|--------------|
| `/config` | Einstellungen öffnen |
| `/model` | Model wechseln |
| `/permissions` | Permissions anzeigen/ändern |
| `/mcp` | MCP Server verwalten |
| `/plugin` | Plugin Manager öffnen |
| `/agents` | Subagent Konfiguration |
| `/skills` | Verfügbare Skills listen |
| `/keybindings` | Keybindings bearbeiten |
| `/theme` | Color Theme ändern |

### Tools & Analysis
| Befehl | Beschreibung |
|--------|--------------|
| `/context` | Context-Nutzung visualisieren |
| `/cost` | Token-Verbrauch anzeigen |
| `/usage` | Plan Usage & Rate Limits |
| `/stats` | Session History visualisieren |
| `/insights` | Session-Analyse Report |
| `/status` | Version, Model, Account Info |
| `/doctor` | Installation diagnostizieren |
| `/security-review` | Security Issues analysieren |

### Modi & Features
| Befehl | Beschreibung |
|--------|--------------|
| `/plan` | Plan Mode aktivieren |
| `/vim` | Vim Mode toggle |
| `/sandbox` | Sandbox Mode toggle |
| `/tasks` | Background Tasks anzeigen |
| `/output-style [style]` | Output Style (Default/Explanatory/Learning) |

### Integration
| Befehl | Beschreibung |
|--------|--------------|
| `/desktop` / `/app` | Session in Claude Desktop fortsetzen |
| `/ide` | IDE Integration verwalten |
| `/remote-control` | Remote Control von claude.ai |
| `/pr-comments [PR]` | GitHub PR Comments fetchen |

## Keyboard Shortcuts

| Shortcut | Aktion | Platform Notes |
|----------|--------|----------------|
| `Ctrl+C` | Abbrechen | |
| `Ctrl+L` | Terminal leeren | |
| `Ctrl+R` | History durchsuchen | |
| `Ctrl+O` | Verbose Output toggle | |
| `Ctrl+G` | Prompt im Editor öffnen | |
| `Ctrl+B` | Task in Background | |
| `Ctrl+F` | Alle Background Agents killen | |
| `Ctrl+T` | Task List toggle | |
| `Ctrl+V` / `Cmd+V` | Bild aus Clipboard pasten | `Alt+V` auf Linux |
| `Esc Esc` | Rewind/Checkpoint | |
| `Shift+Tab` / `Alt+M` | Permission Mode wechseln | |
| `Option+P` / `Alt+P` | Model wechseln | `Option` macOS, `Alt` Win/Linux |
| `Option+T` / `Alt+T` | Extended Thinking toggle | |
| `\+Enter` | Mehrzeilige Eingabe | |

## CLI Flags

```bash
# Output & Format
--output-format json|text|stream-json
--input-format text|stream-json
--json-schema '{"type":"object"}'   # Validiertes JSON Output
--include-partial-messages          # Streaming Events includen

# Tools einschränken
--tools "Edit,Read"                 # Nur diese Tools
--allowedTools "Bash(git:*)"        # Auto-approve Pattern
--disallowedTools "Edit"            # Tools blockieren

# System Prompt
--append-system-prompt "text"       # Instruktionen anhängen
--system-prompt "text"              # System Prompt ersetzen
--append-system-prompt-file path    # Von Datei laden
--system-prompt-file path           # System Prompt aus Datei

# Limits & Control
--max-turns 5                       # Max Agentic Turns
--max-budget-usd 10                 # Kostenlimit
--verbose                           # Verbose Logging
--debug                             # Debug Mode mit Category-Filter

# Session Management
--continue / -c                     # Letzte Session fortsetzen
--resume "id" / -r                  # Spezifische Session
--session-id "uuid"                 # Custom Session ID
--fork-session                      # Neue Session ID beim Resume
--no-session-persistence            # Session nicht speichern
--teleport "session-id"             # Web Session im Terminal fortsetzen

# Subagents & Agents
--agent "agent-name"                # Spezifischen Agent nutzen
--agents '{"name":{"description":"...","tools":["Read"]}}' # Custom Agents
--disallowedTools "Agent(Explore)"  # Subagents blockieren

# Integration
--chrome / --no-chrome              # Chrome Browser enable/disable
--ide                               # Auto-connect zu IDE
--remote                            # Web Session auf claude.ai
--add-dir path                      # Zusätzliches Working Directory

# MCP
--mcp-config path                   # MCP Server JSON laden
--strict-mcp-config                 # Nur diese MCP Server nutzen

# Permissions
--allow-dangerously-skip-permissions # Permission Bypass Option

# Config
--settings path-or-json             # Settings JSON
--setting-sources list              # Spezifische Setting Sources
--plugin-dir path                   # Custom Plugin Directory

# Init & Hooks
--init / --init-only                # Init Hooks ausführen
--maintenance                       # Maintenance Hooks und Exit
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

### Wichtigste
```bash
ANTHROPIC_API_KEY                    # API Key (oder /login nutzen)
ANTHROPIC_MODEL                      # Default Model
CLAUDE_CODE_DISABLE_TELEMETRY=1      # Telemetrie aus
```

### Weitere (Auswahl)
```bash
# Performance
ENABLE_TOOL_SEARCH=auto|auto:5|false # Tool Search Control
MCP_TIMEOUT=10000                     # MCP Startup Timeout (ms)
MAX_MCP_OUTPUT_TOKENS=25000           # Max MCP Output Size
BASH_DEFAULT_TIMEOUT_MS=120000        # Bash Timeout

# MCP
ENABLE_CLAUDEAI_MCP_SERVERS=false    # Claude.ai MCP Server Integration

# Memory
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1    # Auto Memory deaktivieren
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 # Zusätzliche CLAUDE.md dirs

# Verhalten
CLAUDE_CODE_EFFORT_LEVEL=low|medium|high # Effort Level
```

## Permission Modes

| Mode | Verhalten |
|------|-----------|
| `default` | Fragt bei jedem Tool |
| `plan` | Erst planen, dann alle Steps genehmigen |
| `acceptEdits` | File Edits automatisch erlauben, Rest fragen |
| `dontAsk` | Pre-approved Tools erlauben, Rest ablehnen |
| `bypassPermissions` | ALLES automatisch erlauben (gefährlich!) |

Wechseln: `Shift+Tab` oder `Alt+M`

```json
// settings.json
{
  "permissionMode": "acceptEdits",
  "permissions": {
    "allow": ["Bash(git:*)"],
    "deny": ["Bash(rm -rf:*)"]
  }
}
```
