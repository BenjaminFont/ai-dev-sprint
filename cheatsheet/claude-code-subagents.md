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
| `skills` | Nein | Skills die in den Subagent-Kontext geladen werden |
| `hooks` | Nein | Lifecycle-Hooks (PreToolUse, PostToolUse, Stop) |

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

# Subagents deaktivieren
claude --disallowedTools "Task(Explore)" "Task(my-agent)"
```

```json
// settings.json -- Subagents blockieren
{
  "permissions": {
    "deny": ["Task(Explore)", "Task(my-custom-agent)"]
  }
}
```

## Kontext-Isolation

- Jeder Subagent hat eigenen, isolierten Kontext
- Nur das Ergebnis fliesst in die Hauptkonversation zurueck
- Subagents koennen keine weiteren Subagents spawnen (kein Nesting)
- Transcripts: `~/.claude/projects/{project}/{session}/subagents/agent-{id}.jsonl`

## Scope-Prioritaet

1. `--agents` CLI Flag (nur aktuelle Session)
2. `.claude/agents/` (Projekt)
3. `~/.claude/agents/` (User)
4. Plugin Agents (niedrigste)
