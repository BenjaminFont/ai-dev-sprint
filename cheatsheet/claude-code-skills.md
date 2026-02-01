# Claude Code Skills Cheatsheet

## Was ist ein Skill?

Eine Markdown-Datei, die Claude ein wiederverwendbares Verhalten beibringt. Ein Prompt-Template, aufrufbar mit `/skill-name`.

## Dateistruktur

```
.claude/skills/<skill-name>/SKILL.md   # Projekt-Scope
~/.claude/skills/<skill-name>/SKILL.md  # User-Scope (alle Projekte)
```

## Minimales SKILL.md

```yaml
---
name: review
description: Wann dieser Skill verwendet werden soll (Claude liest das)
---

Anweisungen hier. Normales Markdown.
```

## Frontmatter Felder

Alle Felder sind optional. Nur `description` wird empfohlen.

| Feld | Zweck |
|------|-------|
| `name` | Anzeigename (Standard: Ordnername). Nur Kleinbuchstaben, Zahlen, Bindestriche. |
| `description` | Sagt Claude UND dem User wann der Skill passt |
| `allowed-tools` | Tools die der Skill OHNE Nachfrage nutzen darf |
| `model` | Model Override (opus, sonnet, haiku) |
| `context` | `fork` = Skill laeuft als isolierter Subagent |
| `agent` | Subagent-Typ bei `context: fork` (Explore, Plan, general-purpose, oder Custom aus `.claude/agents/`) |
| `user-invocable` | `false` = Nur per Auto-Invocation, nicht via `/name` |
| `disable-model-invocation` | `true` = Nur manuell via `/name`, kein Auto-Trigger |
| `argument-hint` | Autocomplete-Hinweis (z.B. `[issue-number]`) |
| `hooks` | Lifecycle-Hooks (PreToolUse, PostToolUse, Stop) nur fuer diesen Skill |

## Argumente

| Syntax | Bedeutung |
|--------|-----------|
| `$ARGUMENTS` | Alle Argumente als String |
| `$ARGUMENTS[0]`, `$ARGUMENTS[1]` | Einzelnes Argument (0-basiert) |
| `$0`, `$1` | Shorthand fuer `$ARGUMENTS[N]` |
| `${CLAUDE_SESSION_ID}` | Aktuelle Session-ID |

```markdown
## Variables
FILE: $0 - Die zu pruefende Datei
MODE: $1 or "quick" if not specified
```

Aufruf: `/review src/main.ts thorough`

## Wann einen Skill schreiben

- Gleiches Prompt-Muster wird wiederholt ueber Sessions hinweg
- Konsistentes Output-Format gewuenscht (z.B. Review-Verdikt, Diagramme)
- Mehrstufiger Workflow soll kodifiziert werden
- Wiederverwendbarer Prompt fuers Team (Projekt-Scope)

## Wann KEINEN Skill schreiben

- Einmalige Aufgaben (einfach den Prompt tippen)
- Simples Aliasing (Shell-Aliases nutzen)
- Echte Code-Logik noetig (Hooks oder MCP Server verwenden)

## Gute Skills schreiben

**Do:**
- Description beantwortet "wann soll Claude das nutzen?"
- Workflow als nummerierte Schritte
- Output-Format explizit definieren
- Variablen mit Fallback-Defaults

**Don't:**
- Vage Descriptions ("hilft bei Code")
- Code-Logik in Skills (sind Prompts, keine Scripts)
- Duplizieren was CLAUDE.md bereits abdeckt
- Ueberkonstrainieren -- Claude soll sich an Kontext anpassen

## Skill vs andere Erweiterungen

| Mechanismus | Verwenden wenn |
|-------------|---------------|
| **Skill** | Wiederverwendbarer Prompt mit strukturiertem Workflow |
| **CLAUDE.md** | Projektweite Regeln die immer gelten |
| **Hook** | Shell-Commands bei Tool-Events (pre/post) |
| **MCP Server** | Claude Zugriff auf externe APIs/Daten geben |
| **Rules** | Modulare Regeln in `.claude/rules/` (wie CLAUDE.md-Bausteine) |
| **Subagent** | Background-Task mit eigenem Tool-Set |

## Kurzbeispiele

**Code erklaeren:**
```yaml
---
name: explain
description: Erklaert Code mit Diagrammen. Verwenden bei "wie funktioniert das?"
---
1. Mit einer Analogie starten
2. ASCII-Diagramm zeichnen
3. Schritt fuer Schritt durchgehen
4. Haeufigen Fehler hervorheben
```

**Spec schreiben:**
```yaml
---
name: spec
description: Feature-Spezifikation erstellen
---
## Variables
FEATURE: $0 - Nachfragen falls nicht angegeben.

1. Bestehende Specs in `specs/` als Format-Referenz lesen
2. Klaerende Fragen stellen
3. Spec nach `.claude/rules/how-to-write-specs.md` entwerfen
4. Speichern unter `specs/[feature].md`
```

## Aufruf

```bash
/my-skill                  # Ohne Argumente
/my-skill some-arg         # Mit Argument
/my-skill arg0 arg1        # Mehrere Argumente
```

## Auto-Invocation

Claude ruft Skills automatisch auf wenn die `description` zur User-Anfrage passt.

Steuerung:
- `disable-model-invocation: true` -- Skill nur manuell via `/name`
- `user-invocable: false` -- Skill nur automatisch, nicht via `/name`

## Subagent-Modus

Mit `context: fork` laeuft ein Skill isoliert -- eigener Kontext, kein Zugriff auf Konversation. Das `agent` Feld bestimmt die Ausfuehrungsumgebung.

```yaml
---
name: deep-research
description: Thema gruendlich recherchieren
context: fork
agent: Explore
allowed-tools: Read, Grep, Glob
---

Recherchiere $ARGUMENTS:
1. Relevante Dateien finden
2. Code analysieren
3. Ergebnisse mit Datei-Referenzen zusammenfassen
```

`agent` Optionen: `Explore`, `Plan`, `general-purpose`, oder Custom-Subagent aus `.claude/agents/`. Standard: `general-purpose`.

## Dynamischer Kontext

Mit `` !`command` `` werden Shell-Commands VOR dem Senden an Claude ausgefuehrt. Der Output ersetzt den Platzhalter.

```yaml
---
name: pr-summary
description: Pull Request zusammenfassen
context: fork
agent: Explore
---
- PR Diff: !`gh pr diff`
- Geaenderte Dateien: !`gh pr diff --name-only`

Fasse diesen Pull Request zusammen.
```

## Zusatzdateien

Ein Skill-Ordner kann neben `SKILL.md` weitere Dateien enthalten (Templates, Referenzdocs, Beispiele). Claude hat Zugriff darauf waehrend der Skill-Ausfuehrung.
