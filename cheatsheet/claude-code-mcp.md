# Claude Code MCP Cheatsheet

## Was ist MCP?

Model Context Protocol -- ein offener Standard, der Claude mit externen Tools, Datenbanken und APIs verbindet. MCP Server stellen Claude Tools bereit, die es on-demand nutzt.

## Transport-Typen

| Typ | Wann verwenden |
|-----|---------------|
| `http` | Remote-Server (empfohlen) |
| `sse` | Remote-Server (deprecated, http bevorzugen) |
| `stdio` | Lokale Prozesse, CLI-Tools, Custom Scripts |

## Server hinzufuegen

```bash
# HTTP (Remote)
claude mcp add --transport http <name> <url>
claude mcp add --transport http notion https://mcp.notion.com/mcp

# HTTP mit Auth-Header
claude mcp add --transport http api https://api.example.com/mcp \
  --header "Authorization: Bearer <token>"

# Stdio (Lokal)
claude mcp add --transport stdio <name> -- <command> [args...]
claude mcp add --transport stdio --env API_KEY=xxx airtable \
  -- npx -y airtable-mcp-server

# Aus JSON-Config
claude mcp add-json <name> '<json>'
claude mcp add-json weather '{"type":"http","url":"https://api.weather.com/mcp"}'

# Von Claude Desktop importieren
claude mcp add-from-claude-desktop
```

Alle Optionen (`--transport`, `--env`, `--scope`, `--header`) muessen VOR dem Server-Namen stehen. `--` trennt Server-Name von Command/Args.

## Server verwalten

```bash
claude mcp list              # Alle Server anzeigen
claude mcp get <name>        # Details eines Servers
claude mcp remove <name>     # Server entfernen
/mcp                         # Status pruefen (in Session)
```

## Scopes

| Scope | Speicherort | Sichtbarkeit |
|-------|-------------|-------------|
| `local` | `~/.claude.json` (Standard) | Nur du, nur dieses Projekt |
| `project` | `.mcp.json` im Projekt-Root | Team (einchecken in Git) |
| `user` | `~/.claude.json` | Nur du, alle Projekte |

```bash
claude mcp add --transport http --scope project paypal https://mcp.paypal.com/mcp
claude mcp add --transport http --scope user hubspot https://mcp.hubspot.com/anthropic
```

Prioritaet bei Namenskonflikten: local > project > user.

## .mcp.json (Project Scope)

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "local-tool": {
      "command": "npx",
      "args": ["-y", "@some/mcp-server"],
      "env": {
        "API_KEY": "${API_KEY}",
        "BASE_URL": "${BASE_URL:-https://default.example.com}"
      }
    }
  }
}
```

Env-Vars in `.mcp.json`: `${VAR}` oder `${VAR:-default}`. Funktioniert in `command`, `args`, `env`, `url`, `headers`.

## OAuth / Authentifizierung

```bash
# 1. Server hinzufuegen
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# 2. In Session authentifizieren
/mcp
# -> "Authenticate" waehlen, Browser oeffnet sich
```

Tokens werden sicher gespeichert und automatisch erneuert. "Clear authentication" in `/mcp` zum Widerrufen.

## Ressourcen per @-Mention

MCP Server koennen Ressourcen bereitstellen, die per `@` referenzierbar sind:

```
> Analysiere @github:issue://123
> Vergleiche @postgres:schema://users mit @docs:file://database/user-model
```

`@` tippen zeigt verfuegbare Ressourcen im Autocomplete.

## MCP Prompts als Commands

MCP Server koennen Prompts exponieren, die als `/`-Commands erscheinen:

```
/mcp__github__list_prs
/mcp__github__pr_review 456
```

## Claude Code als MCP Server

```bash
claude mcp serve
```

Andere Apps (z.B. Claude Desktop) koennen sich damit verbinden und Claude Code's Tools nutzen (Read, Edit, LS, etc.).

## Environment Variables

| Variable | Zweck |
|----------|-------|
| `MCP_TIMEOUT` | Startup-Timeout in ms (z.B. `MCP_TIMEOUT=10000 claude`) |
| `MAX_MCP_OUTPUT_TOKENS` | Max Output-Groesse (Standard: 25000, Warnung ab 10000) |
| `ENABLE_TOOL_SEARCH` | `auto` (Standard), `auto:N` (Custom-Threshold), `true`, `false` |

## Tool Search

Bei vielen MCP-Tools aktiviert sich Tool Search automatisch (wenn Tools >10% des Kontexts belegen). Claude laedt dann Tools on-demand statt alle vorab.

```bash
ENABLE_TOOL_SEARCH=auto:5 claude   # Ab 5% aktivieren
ENABLE_TOOL_SEARCH=false claude    # Deaktivieren
```

Erfordert Sonnet 4+ oder Opus 4+. Haiku nicht unterstuetzt.

## Permissions fuer MCP Tools

```json
{
  "permissions": {
    "allow": ["mcp__github__create_issue"],
    "deny": ["mcp__dangerous__delete_all"]
  }
}
```

## Troubleshooting

- `/mcp` in Session zeigt Status aller Server
- `claude mcp get <name>` zeigt Konfigurationsdetails
- `claude mcp reset-project-choices` setzt Approval-Entscheidungen fuer `.mcp.json` zurueck
- Windows (nicht WSL): `cmd /c` Wrapper noetig fuer `npx` (`-- cmd /c npx -y @some/package`)
- Server antwortet nicht: `MCP_TIMEOUT` erhoehen
- Output zu gross: `MAX_MCP_OUTPUT_TOKENS` erhoehen
