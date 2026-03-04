# Claude Code Subagents Cheatsheet

## Was sind Subagents?

Isolierte KI-Assistenten mit eigenem Kontextfenster, eigenen Tools und eigenem System-Prompt. Claude delegiert Aufgaben an Subagents und bekommt nur das Ergebnis zurueck -- der Hauptkontext bleibt sauber.

## Eingebaute Subagents

| Subagent | Model | Tools | Zweck |
|----------|-------|-------|-------|
| **Explore** | Haiku | Read-only | Schnelle Codebase-Suche und Analyse |
| **Plan** | Inherited | Read-only | Kontext sammeln fuer Planungsmodus |
| **general-purpose** | Inherited | Alle | Komplexe mehrstufige Aufgaben |
| **Bash** | Inherited | Bash | Terminal-Commands in separatem Kontext |
| **statusline-setup** | Sonnet | Read, Edit | Statusline Konfiguration |
| **claude-code-guide** | Haiku | Read, WebFetch | Claude Code Dokumentation |

## Custom Subagents erstellen

### Dateistruktur

```
.claude/agents/my-agent.md    # Projekt-Scope (Team, via Git)
~/.claude/agents/my-agent.md  # User-Scope (alle Projekte)
```

### AGENT.md Format

```markdown
---
name: code-reviewer
description: Code-Review-Spezialist. Proaktiv nach Code-Aenderungen verwenden.
tools: Read, Grep, Glob, Bash
disallowedTools: Write, Edit
model: sonnet
---

Du bist ein Senior Code Reviewer.

Bei Aufruf:
1. `git diff` ausfuehren
2. Geaenderte Dateien analysieren
3. Strukturiertes Review ausgeben
```

### Frontmatter Felder

| Feld | Pflicht | Beschreibung |
|------|---------|--------------|
| `name` | Ja | Eindeutiger Name (Kleinbuchstaben, Bindestriche) |
| `description` | Ja | Wann Claude diesen Subagent nutzen soll |
| `tools` | Nein | Erlaubte Tools (Standard: alle vom Parent) |
| `disallowedTools` | Nein | Explizit verbotene Tools |
| `model` | Nein | `sonnet`, `opus`, `haiku`, `inherit` (Standard) |
| `permissionMode` | Nein | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan` |
| `maxTurns` | Nein | Maximum Agentic Turns |
| `memory` | Nein | Persistent Memory Scope: `user`, `project`, `local` |
| `background` | Nein | Als Background Task laufen: `true`/`false` |
| `isolation` | Nein | Worktree Isolation: `worktree` |
| `mcpServers` | Nein | MCP Server Namen (Array) |
| `skills` | Nein | Skills die in den Subagent-Kontext geladen werden |
| `hooks` | Nein | Lifecycle-Hooks (PreToolUse, PostToolUse, Stop, etc.) |

## Subagents in Skills

Mit `context: fork` laeuft ein Skill als isolierter Subagent:

```yaml
---
name: deep-research
description: Thema gruendlich recherchieren
context: fork
agent: Explore
allowed-tools: Read, Grep, Glob
---

Recherchiere $ARGUMENTS gruendlich.
```

`agent` Optionen: `Explore`, `Plan`, `general-purpose`, oder Custom-Name aus `.claude/agents/`.

## Ausfuehrungsmodi

| Modus | Verhalten |
|-------|-----------|
| **Foreground** | Blockiert Hauptkonversation, kann Rueckfragen stellen |
| **Background** | Laeuft parallel (`Ctrl+B`), erbt Pre-Approved Permissions, keine Rueckfragen |

Bis zu 7 Subagents koennen parallel laufen.

## Wann Subagents verwenden

**Ja:**
- Aufgabe produziert viel Output (Tests, Logs, Recherche)
- Arbeit ist abgeschlossen und liefert ein Ergebnis zurueck
- Tool-Zugriff soll eingeschraenkt werden
- Parallele Recherche in verschiedenen Bereichen

**Nein:**
- Haeufiges Hin-und-Her mit dem User noetig
- Schnelle, gezielte Aenderung
- Kontext aus der Hauptkonversation wird benoetigt

## CLI & Konfiguration

```bash
# Ad-hoc Subagents per CLI Flag
claude --agents '{"reviewer": {"description": "...", "tools": ["Read"]}}'

# Subagents deaktivieren (WICHTIG: Agent() nicht Task()!)
claude --disallowedTools "Agent(Explore)" "Agent(my-agent)"

# Spezifischen Agent nutzen
claude --agent "my-custom-agent"

# Agent Management
/agents                              # Interactive Agent Config
```

```json
// settings.json -- Subagents blockieren
{
  "permissions": {
    "deny": ["Agent(Explore)", "Agent(my-custom-agent)"]
  }
}
```

**WICHTIG**: Ab v2.1.63+ heißt das Tool `Agent()`, nicht mehr `Task()`!

## Features

### Persistent Memory
Subagents können eigenes Memory haben:
```yaml
---
memory: user   # user, project, oder local
---
```

### Worktree Isolation
Für Git-isolierte Umgebungen:
```yaml
---
isolation: worktree
---
```

### MCP Server Access
```yaml
---
mcpServers: ["github", "postgres"]
---
```

## Kontext-Isolation

- Jeder Subagent hat eigenen, isolierten Kontext
- Nur das Ergebnis fliesst in die Hauptkonversation zurueck
- Subagents koennen keine weiteren Subagents spawnen (kein Nesting)
- Transcripts: `~/.claude/projects/{project}/{session}/subagents/agent-{id}.jsonl`

## Agent Teams vs Subagents

**Agent Teams** sind ein SEPARATES Feature (nicht zu verwechseln):
- Mehrere Agents mit Peer-to-Peer Kommunikation
- Eigene Konfiguration in `.claude/agent-teams/`
- Siehe separate Dokumentation

## Scope-Prioritaet

1. `--agents` CLI Flag (nur aktuelle Session)
2. `.claude/agents/` (Projekt)
3. `~/.claude/agents/` (User)
4. Plugin Agents (niedrigste)
