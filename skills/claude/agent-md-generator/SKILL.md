---
name: agent-md-generator
description: >
  Generiert CLAUDE.md und .cursorrules Dateien automatisch aus den Ergebnissen der
  Phasen 1-3 des VibeCoding Frameworks. Verwende diesen Skill IMMER wenn (1) der
  User eine CLAUDE.md erstellen oder aktualisieren moechte, (2) eine .cursorrules
  Datei gebraucht wird, (3) der architect-prompter Skill Phase 4 an diesen Skill
  delegiert, (4) Begriffe wie "CLAUDE.md", ".cursorrules", "Projektkontext fuer
  Cursor", "Kontextdatei" vorkommen, (5) der User sagt "generier mir die CLAUDE.md"
  oder "erstell den Projektkontext". Auch wenn ein bestehendes Projekt eine CLAUDE.md
  braucht oder eine bestehende aktualisiert werden soll, diesen Skill verwenden.
---

# Claude MD Generator

Generiert die zentrale Kontextdatei (`CLAUDE.md`) und optional eine `.cursorrules`
für ein Projekt. Diese Dateien werden von Cursor AI und Claude Code automatisch
gelesen und geben dem AI-Agent den vollständigen Projektkontext.

**Kernprinzip:** Die CLAUDE.md ist das Gedächtnis des AI-Agents. Je besser sie ist,
desto weniger muss man in jedem Prompt wiederholen.

## Referenz-Dateien

- **Beispiele:** `references/beispiele.md` — Vollständige CLAUDE.md Beispiele nach Projekttyp

## Wann wird dieser Skill aufgerufen?

### Szenario A: Neues Projekt (aus dem Lifecycle)
Der `architect-prompter` hat Phase 3 abgeschlossen und übergibt:
- Problem-Statement (Phase 1)
- Requirements + MVP-Scope (Phase 2)
- Tech-Stack + Ordnerstruktur + Datenmodell (Phase 3)

→ CLAUDE.md und .cursorrules automatisch generieren.

### Szenario B: Bestehendes Projekt ohne CLAUDE.md
Der User hat ein Projekt aber keine Kontextdatei.

→ Projektinfos abfragen und CLAUDE.md generieren.

### Szenario C: CLAUDE.md aktualisieren
Das Projekt hat sich weiterentwickelt, die CLAUDE.md ist veraltet.

→ Bestehende CLAUDE.md laden, Änderungen einarbeiten.

## Workflow

### Szenario A: Aus dem Lifecycle

Wenn alle Infos aus Phase 1-3 vorliegen:

1. Infos aus den Phasen zusammensammeln (sind im Konversationskontext)
2. CLAUDE.md generieren (Englisch)
3. .cursorrules generieren (Englisch, Kurzversion)
4. Beide Dateien als Download bereitstellen
5. Via `ask_user_input` bestätigen lassen

Kein langes Fragen — die Infos sind da, direkt generieren.

### Szenario B: Bestehendes Projekt

Schrittweise die nötigen Infos sammeln:

1. **Projektname und Ziel** — Freitext: "Was macht das Projekt?"
2. **Tech-Stack** — Via `ask_user_input` oder Freitext
3. **Ordnerstruktur** — User kann sie einfügen oder beschreiben
4. **Konventionen** — Via `ask_user_input`:
   ```
   Frage: "Welche Konventionen gelten?"
   Typ: multi_select
   Optionen:
   - TypeScript strict mode
   - ESLint + Prettier
   - Tailwind CSS
   - Konventionelle Commits
   ```
5. **Besondere Regeln** — Freitext: "Gibt es Regeln die der Agent beachten soll?"

Dann generieren wie in Szenario A.

### Szenario C: Aktualisierung

1. User beschreibt was sich geändert hat
2. Bestehende CLAUDE.md im Kontext analysieren
3. Gezielte Abschnitte aktualisieren
4. Aktualisierte Version präsentieren

## CLAUDE.md Struktur

Die CLAUDE.md wird immer auf Englisch generiert — AI-Tools arbeiten besser damit.

### Pflicht-Abschnitte (immer enthalten)

```markdown
# [Project Name]

## Goal
[Problem-Statement — was das Projekt löst, 2-3 Sätze]

## Tech Stack
[Konkrete Tools, Frameworks, Versionen]

## Project Structure
[Ordnerstruktur als Baum mit Kommentaren]

## Conventions
[Code-Stil, Naming, Formatting, Kommentare]

## Rules
[Was der Agent tun und NICHT tun soll]
```

### Optionale Abschnitte (wenn relevant)

```markdown
## Data Model
[Entitäten, Felder, Relationen — wenn DB vorhanden]

## MVP Scope
[Feature-Liste mit Checkboxen — bei neuen Projekten]

## API Endpoints
[Endpoint-Übersicht — bei APIs]

## Environment Variables
[Benötigte ENV-Variablen mit Beschreibung]

## References
[Links zu Standards, Design System, Docs]

## Team
[Rollen, Zuständigkeiten — bei Teamprojekten]
```

### Rules-Abschnitt: Best Practices

Der Rules-Abschnitt ist der wichtigste für saubere AI-Outputs.
Immer als "Do / Do NOT" Regeln formulieren:

```markdown
## Rules
- Always use TypeScript strict mode
- Always handle errors explicitly, never use empty catch blocks
- Do NOT install additional dependencies without asking
- Do NOT modify files outside the specified scope
- Do NOT use `any` type — use proper interfaces
- Do NOT leave console.log statements in production code
- Prefer named exports over default exports
- Prefer composition over inheritance
```

**Projekttyp-spezifische Regeln einbauen:**

Web-App:
```markdown
- All components must be responsive (mobile-first)
- Use Tailwind utility classes, no custom CSS files
- All user input must be validated with Zod
```

API:
```markdown
- All endpoints return consistent error format: { error: string, code: number }
- All list endpoints support pagination
- Soft delete via deleted_at, never hard delete
```

Homelab:
```markdown
- All containers use the standard Docker Compose format from the framework
- Environment variables via .env file, never hardcoded
- Volumes mount to /mnt/nas/ paths as defined in homelab-kontext
```

## .cursorrules Generierung

Die `.cursorrules` ist eine Kurzversion — maximal 20-30 Zeilen.
Sie enthält nur Konventionen und Regeln, keinen vollständigen Kontext.

```
You are working on [Project Name], a [1-Satz-Beschreibung].

Tech: [Stack in einem Satz]

Conventions:
- [Naming-Konvention]
- [Formatting-Regel]
- [Error-Handling-Pattern]

Rules:
- [Do NOT...]
- [Always...]
- [Prefer X over Y]

For full project context, read CLAUDE.md in the project root.
```

## Output

Beide Dateien werden als herunterladbare Dateien erstellt:
- `CLAUDE.md` — Vollständiger Projektkontext
- `.cursorrules` — Kurzversion für Cursor

Nach der Generierung via `ask_user_input`:
```
Frage: "CLAUDE.md und .cursorrules sind bereit."
Optionen:
- Passt — ich kopiere sie ins Projekt
- CLAUDE.md anpassen
- .cursorrules anpassen
- Beides anpassen
```

## Qualitäts-Checks

Vor der Ausgabe intern prüfen:

1. **Vollständigkeit:** Sind alle Pflicht-Abschnitte vorhanden?
2. **Konsistenz:** Stimmt der Tech-Stack mit der Ordnerstruktur überein?
3. **Rules:** Mindestens 3 "Do NOT"-Regeln vorhanden?
4. **Sprache:** CLAUDE.md und .cursorrules auf Englisch?
5. **Länge:** CLAUDE.md zwischen 50-200 Zeilen? (Nicht zu kurz, nicht zu lang)

## Konversations-Stil

- Bei Szenario A: Direkt generieren, keine unnötigen Fragen
- Bei Szenario B: Effizient Infos sammeln, maximal 3-4 Fragen
- Bei Szenario C: Gezielt fragen was sich geändert hat
- Dateien immer als fertige Files liefern, nicht als Code-Block im Chat
