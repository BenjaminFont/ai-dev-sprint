# Gemini CLI Cheatsheet

## Installation

```bash
npx @google/gemini-cli          # Ohne Installation
npm install -g @google/gemini-cli  # Global installieren
brew install gemini-cli         # macOS/Linux
```

## Authentifizierung

```bash
# Option 1: Google Login (OAuth) - beim Start auswählen
gemini

# Option 2: API Key
export GEMINI_API_KEY="YOUR_KEY"

# Option 3: Vertex AI (Enterprise)
export GOOGLE_API_KEY="YOUR_KEY"
export GOOGLE_GENAI_USE_VERTEXAI=true
```

## CLI Starten

```bash
gemini                          # Interaktive Session
gemini -p "prompt"              # Non-interaktiv, Output und Exit
gemini -m gemini-2.5-flash      # Model wählen
gemini --sandbox                # Sandbox Mode (Docker/Podman)
gemini --yolo                   # Alle Tool-Calls auto-genehmigen
gemini --checkpointing          # Snapshots vor Änderungen
echo "prompt" | gemini          # Pipe Input
```

## CLI Flags

| Flag | Beschreibung |
|------|--------------|
| `-p, --prompt` | Prompt direkt ausführen |
| `-m, --model` | Model wählen |
| `-i, --prompt-interactive` | Interaktiv mit Initial-Prompt |
| `-d, --debug` | Debug Output aktivieren |
| `--yolo` | Auto-approve alle Tools |
| `--sandbox` | Sandbox Mode (Docker) |
| `--checkpointing` | Restore-Punkte aktivieren |
| `--output-format json` | JSON Output |
| `--output-format stream-json` | Streaming JSON |
| `--include-directories` | Zusätzliche Verzeichnisse |

## Slash Commands

| Befehl | Beschreibung |
|--------|--------------|
| `/help` | Hilfe anzeigen |
| `/clear` | Terminal & Kontext leeren |
| `/compress` | Kontext zusammenfassen (Token sparen) |
| `/copy` | Letzte Antwort kopieren |
| `/stats` | Token-Verbrauch anzeigen |
| `/tools` | Verfügbare Tools listen |
| `/settings` | Einstellungen bearbeiten |
| `/theme` | Theme wechseln |
| `/vim` | Vim Mode aktivieren |
| `/init` | GEMINI.md generieren |
| `/restore` | Dateiänderungen rückgängig machen |
| `/quit` | Beenden |

### Chat/Session Management

| Befehl | Beschreibung |
|--------|--------------|
| `/chat save <tag>` | Konversation speichern |
| `/chat resume <tag>` | Konversation fortsetzen |
| `/chat list` | Gespeicherte Sessions anzeigen |
| `/chat delete <tag>` | Session löschen |
| `/chat share [file]` | Konversation exportieren |

### Memory (Kontext)

| Befehl | Beschreibung |
|--------|--------------|
| `/memory show` | Geladenen Kontext anzeigen |
| `/memory refresh` | GEMINI.md neu laden |
| `/memory add <text>` | Kontext hinzufügen |
| `/memory list` | Memory-Dateipfade anzeigen |

### MCP Server

| Befehl | Beschreibung |
|--------|--------------|
| `/mcp` | Konfigurierte Server listen |
| `/mcp desc` | Tool-Details anzeigen |
| `/mcp schema` | JSON-Schema anzeigen |
| `/extensions` | Aktive Extensions listen |

### Verzeichnisse

| Befehl | Beschreibung |
|--------|--------------|
| `/dir add <path>` | Verzeichnisse hinzufügen |
| `/dir show` | Hinzugefügte Verzeichnisse |

## Spezielle Prefixes

```bash
# @ - Dateien referenzieren
@./src/main.js              # Datei einbinden
@./screenshot.png           # Bild einbinden
@./src/                     # Ganzes Verzeichnis

# ! - Shell Commands
!git status                 # Shell-Befehl ausführen
!                           # Shell-Mode an/aus
```

## Keyboard Shortcuts

| Shortcut | Aktion |
|----------|--------|
| `Ctrl+L` | Terminal leeren |
| `Ctrl+V` | Text/Bild einfügen |
| `Ctrl+Y` | YOLO Mode togeln |
| `Ctrl+X` | Prompt im Editor öffnen |
| `Ctrl+Z` | Undo |
| `Ctrl+Shift+Z` | Redo |

## Konfiguration

### Dateien

| Datei | Beschreibung |
|-------|--------------|
| `~/.gemini/settings.json` | User-Settings |
| `.gemini/settings.json` | Projekt-Settings |
| `GEMINI.md` | Projekt-Kontext (wie CLAUDE.md) |
| `~/.gemini/GEMINI.md` | Globaler Kontext |
| `.geminiignore` | Dateien ausschließen |

### settings.json Beispiel

```json
{
  "theme": "dark",
  "autoAccept": true,
  "sandbox": "docker",
  "vimMode": false,
  "checkpointing": true,
  "usageStatisticsEnabled": true,
  "mcpServers": {}
}
```

## Custom Commands

Erstelle TOML-Dateien in:
- `~/.gemini/commands/` (global)
- `.gemini/commands/` (projekt)

Beispiel: `.gemini/commands/review.toml` → `/review`

Namespacing: `.gemini/commands/git/commit.toml` → `/git:commit`

## MCP Server

### In settings.json

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-github"],
      "timeout": 15000,
      "trust": false
    }
  }
}
```

### CLI Management

```bash
gemini mcp add <name> <config>
gemini mcp list
gemini mcp remove <name>
```

## Built-In Tools

| Tool | Beschreibung |
|------|--------------|
| `read_file()` | Dateien lesen |
| `write_file()` | Dateien schreiben |
| `replace()` | Text ersetzen |
| `list_directory()` | Verzeichnis listen |
| `glob()` | Datei-Pattern suchen |
| `search_file_content()` | In Dateien suchen |
| `google_web_search()` | Web-Suche |
| `web_fetch()` | Webseiten abrufen |
| `save_memory()` | Kontext speichern |

## Praktische Patterns

```bash
# Code Review
gemini -p "Review @./src/main.js"

# Git Diff analysieren
git diff | gemini -p "Erkläre die Änderungen"

# Schnelle Frage mit JSON Output
gemini -p "Liste alle Exports" --output-format json

# Sandbox für unsichere Operationen
gemini --sandbox -p "Führe Tests aus"

# Projekt initialisieren
gemini
> /init
```

## Rate Limits (Free Tier)

| Auth-Methode | Limit |
|--------------|-------|
| Google Login | 60 req/min, 1000 req/day |
| API Key | 1000 req/day |
| Vertex AI | Nach Projekt-Quota |

## Models

```bash
gemini -m gemini-2.5-pro        # Default, bessere Qualität
gemini -m gemini-2.5-flash      # Schneller, günstiger
```
