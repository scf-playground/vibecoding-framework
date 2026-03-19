# CLAUDE.md Template

Vorlage für die Projekt-Kontextdatei. Wird in Englisch generiert.
Cursor AI liest diese Datei automatisch wenn sie im Projekt-Root liegt.
Claude Code liest sie ebenfalls.

## Vollständiges Template

```markdown
# [Project Name]

## Goal
[Problem-Statement aus Phase 1 — 2-3 Sätze, was das Projekt löst]

## Tech Stack
- **Frontend:** [Framework, Sprache, Styling]
- **Backend:** [Framework, Runtime]
- **Database:** [Typ, ORM]
- **Auth:** [Strategie]
- **Deployment:** [Ziel]

## Project Structure
[Ordnerstruktur als Baum — aus Phase 3]

## Data Model
[Wichtigste Entitäten, Felder, Relationen — aus Phase 3]

## MVP Scope
[Features der V1 — aus Phase 2]
- [ ] Feature 1
- [ ] Feature 2
- [ ] Feature 3

## Conventions

### Code Style
- [Sprach-spezifische Konventionen]
- [Naming: camelCase / snake_case / etc.]
- [Formatting: Prettier / Black / etc.]

### Comments
- Technical and concise — describe WHAT, not WHY
- No conversational comments like "Here we do X because Y is better"

### Error Handling
- [Pattern für Error Responses]
- [Logging-Strategie]

### Git
- [Commit-Konventionen]
- [Branch-Strategie wenn relevant]

## Rules
- [Projektspezifische Regeln]
- [Was der Agent NICHT tun soll]
- [Bevorzugte Libraries]
- [Verbotene Patterns]

## References
- Code Standards: [Pfad zum Skill oder Repo-Datei]
- UI Design System: [Falls relevant]
- API Patterns: [Falls relevant]
```

## Hinweise zum Ausfüllen

### Goal
- Immer das Problem beschreiben, nicht die Lösung
- 2-3 Sätze reichen — der Agent braucht Kontext, keinen Roman

### Tech Stack
- Konkrete Versionen angeben wenn relevant (z.B. "Node.js 20+")
- Bei Libraries: Name + Zweck (z.B. "Zod for request validation")

### Project Structure
- Maximal 3 Ebenen tief
- Nur die wichtigsten Dateien zeigen
- Kommentare hinter den Ordnern

### Conventions
- Nur Regeln die vom Standard abweichen
- Wenn Code-Standards als Skill/Datei existieren: referenzieren statt duplizieren

### Rules
- "Do NOT..." Regeln sind extrem wichtig für saubere Ergebnisse
- Häufige Rules:
  - "Do NOT install additional dependencies without asking"
  - "Do NOT modify files outside the specified scope"
  - "Do NOT use any deprecated APIs"
  - "Always use TypeScript strict mode"
  - "Never use `any` type"

## .cursorrules Template

Die `.cursorrules` ist kürzer und fokussiert auf Konventionen.
Cursor lädt sie automatisch aus dem Projekt-Root.

```
You are working on [Project Name], a [kurze Beschreibung].

Tech Stack: [Stack in einem Satz]

Code Conventions:
- [Naming]
- [Formatting]
- [Error Handling Pattern]
- [Import Ordering]

Rules:
- [Do NOT...]
- [Always...]
- [Prefer X over Y]

When writing code, follow the CLAUDE.md in the project root for
full context on structure, data model, and scope.
```

## Wann CLAUDE.md vs .cursorrules

| Datei | Zweck | Gelesen von |
|---|---|---|
| CLAUDE.md | Vollständiger Projektkontext | Claude Code, Cursor (Claude-Modell) |
| .cursorrules | Kurze Konventionen | Cursor (alle Modelle) |

**Empfehlung:** Immer beide erstellen. CLAUDE.md für den vollen Kontext,
.cursorrules als Kurzversion für schnelle Prompts.
