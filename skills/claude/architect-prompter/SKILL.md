---
name: architect-prompter
description: >
  Architektur-Entscheidungen und Prompt-Generierung fuer AI-Coding-Tools (Phase 3-4
  des VibeCoding Frameworks). Verwende diesen Skill IMMER wenn (1) ein Tech-Stack
  gewaehlt werden soll, (2) eine Projektarchitektur oder Ordnerstruktur definiert
  werden muss, (3) eine CLAUDE.md oder .cursorrules erstellt werden soll, (4) ein
  strukturierter Prompt fuer Cursor, Claude Code oder Antigravity gebraucht wird,
  (5) der vibecoding-lifecycle Skill Phase 3 oder 4 an diesen Skill delegiert,
  (6) Begriffe wie "Tech-Stack", "Architektur", "Ordnerstruktur", "CLAUDE.md",
  ".cursorrules", "Prompt fuer Cursor" vorkommen. Auch wenn der User sagt "schreib
  mir einen Prompt" oder "welchen Stack soll ich nehmen" diesen Skill verwenden.
---

# Architect Prompter — Phase 3 & 4

Führt den User von den Requirements zu einer Architektur-Entscheidung (Phase 3)
und generiert daraus eine CLAUDE.md sowie einen strukturierten Prompt für
Cursor AI oder Claude Code (Phase 4).

**Kernprinzip:** Die Architektur bestimmt den Prompt — nicht umgekehrt.

## Referenz-Dateien

- **Stack-Guide:** `references/stack-guide.md` — Tech-Stack-Empfehlungen nach Projekttyp
- **Prompt-Schema:** `references/prompt-schema.md` — Prompt-Struktur und Beispiele
- **CLAUDE.md Template:** `references/claude-md-template.md` — Vorlage für die Kontextdatei

## Workflow-Übersicht

```
Phase 3: Requirements → Architektur-Entscheidung (15-30 Min)
  ↓ Freigabe
Phase 4: Architektur → CLAUDE.md + Prompt (10-20 Min)
  ↓ Freigabe
Output: CLAUDE.md + kopierbarer Prompt für Cursor/CC
```

**Strikte Regel:** Phase 4 erst starten wenn Architektur in Phase 3 freigegeben ist.

## Input

Dieser Skill erwartet als Input die Ergebnisse von Phase 1-2:
- Problem-Statement
- Requirements mit MVP-Scope
- Projekttyp (Web-App / API / Homelab / Automation)
- Constraints und Abhängigkeiten

Wenn diese Infos fehlen: Nachfragen, nicht raten.

## Phase 3: Architektur & Tech-Stack

### Schritt 1: Stack-Vorschläge

Basierend auf Projekttyp und Requirements 1-2 Stack-Vorschläge machen.
Referenz: `references/stack-guide.md` für bewährte Kombinationen.

Für jeden Vorschlag:
- **Stack:** Konkrete Tools und Frameworks benennen
- **Begründung:** Warum dieser Stack für dieses Projekt?
- **Trade-offs:** Was ist der Nachteil? Wo sind die Grenzen?

Via `ask_user_input` wählen lassen:

```
Frage: "Welcher Stack?"
Optionen:
- [Vorschlag A: z.B. Next.js + Prisma + SQLite]
- [Vorschlag B: z.B. SvelteKit + Drizzle + PostgreSQL]
- Ich habe schon einen Stack im Kopf
```

### Schritt 2: Ordnerstruktur

Nach der Stack-Wahl eine Ordnerstruktur vorschlagen.
Als Baumdarstellung präsentieren — nicht als Fliesstext.

Regeln:
- Maximal 3 Ebenen tief zeigen
- Jeder Ordner mit kurzem Kommentar was reingehört
- Typische Dateien mitzeigen (z.B. `index.ts`, `schema.prisma`)

Beispiel:
```
projekt/
├── src/
│   ├── routes/         # API-Endpoints
│   ├── services/       # Business-Logik
│   ├── middleware/      # Auth, Validation
│   └── types/          # TypeScript Interfaces
├── prisma/
│   └── schema.prisma   # Datenmodell
├── CLAUDE.md           # Projekt-Kontext für AI
└── package.json
```

### Schritt 3: Datenmodell

Wenn das Projekt eine Datenbank braucht:
- Wichtigste Entitäten und ihre Beziehungen skizzieren
- Nicht als vollständiges Schema — als Übersicht
- Format: Entität → Felder → Relationen

### Schritt 4: Architektur-Entscheidung bestätigen

Zusammenfassung präsentieren:
```
Tech-Stack: [Stack]
Ordnerstruktur: [Baum]
Datenmodell: [Entitäten]
Deployment: [Ziel]
```

Via `ask_user_input` bestätigen:
```
Frage: "Passt die Architektur?"
Optionen:
- Ja — weiter zu CLAUDE.md und Prompt
- Stack ändern
- Struktur anpassen
- Datenmodell anpassen
```

## Phase 4: Prompt Engineering

### Schritt 1: CLAUDE.md generieren

Die CLAUDE.md ist die zentrale Kontextdatei die Cursor AI und Claude Code
bei jedem Prompt lesen. Template aus `references/claude-md-template.md` nutzen.

Inhalt zusammenbauen aus Phase 1-3:
- Projektziel (Phase 1)
- MVP-Features (Phase 2)
- Tech-Stack + Ordnerstruktur (Phase 3)
- Konventionen aus Code-Standards

**Relevante Standards einbeziehen:**
Wenn im GitHub Framework-Repo Standards definiert sind, diese referenzieren:
- `code-standards` Skill → Naming, Formatting, Error Handling
- `ui-design` Standards → Design Tokens, Komponenten (wenn Frontend)
- `git-workflow` Standards → Branch-Strategie, Commits

CLAUDE.md als Datei erstellen und dem User präsentieren.

### Schritt 2: .cursorrules generieren (optional)

Wenn der User Cursor AI nutzt, zusätzlich eine `.cursorrules` erstellen.
Diese ist kürzer als die CLAUDE.md und fokussiert auf:
- Sprach-Konventionen (Naming, Formatting)
- Verbotene Patterns
- Bevorzugte Libraries

### Schritt 3: Initialen Prompt generieren

Einen strukturierten Prompt für die erste Umsetzung in Cursor/CC erstellen.
Schema aus `references/prompt-schema.md` verwenden.

Der Prompt muss:
- Die CLAUDE.md im PREPARATION-Block referenzieren
- Den Aufgabentyp korrekt klassifizieren (new-project, feature, etc.)
- Spezifisch und abgegrenzt sein
- Relevante Skills referenzieren

Prompt als kopierbarer Code-Block präsentieren.

### Schritt 4: Freigabe

Via `ask_user_input`:
```
Frage: "CLAUDE.md und Prompt bereit — wie weiter?"
Optionen:
- Alles passt — ich gehe jetzt in Cursor/CC
- CLAUDE.md anpassen
- Prompt anpassen
- Beides anpassen
```

## Aufgabentyp-Erkennung

Nicht jede Anfrage ist ein neues Projekt. Der Skill erkennt den Aufgabentyp:

| Typ | Erkennung | Prompt-Schwerpunkt |
|---|---|---|
| `new-project` | "Neues Projekt", Setup, von Grund auf | Stack, Struktur, MVP-Scope |
| `feature` | Neue Funktionalität in bestehendem Projekt | Betroffene Dateien, Verhalten, Edge Cases |
| `bugfix` | Fehler, "funktioniert nicht" | Erwartetes vs. tatsächliches Verhalten |
| `refactor` | Aufräumen, Optimieren | Ziel-Zustand, keine Verhaltensänderung |
| `review` | Prüfen, Analysieren | Prüf-Kriterien, Output-Format |

Bei `feature`, `bugfix`, `refactor`, `review`: Phase 3 überspringen (Stack steht)
und direkt einen passenden Prompt generieren.

## Sprache der Outputs

- **CLAUDE.md:** Englisch — wird von AI-Tools gelesen
- **Prompts:** Englisch — AI-Tools arbeiten besser mit englischen Prompts
- **Kommunikation mit dem User:** Deutsch

## Konversations-Stil

- Entscheidungen klar präsentieren mit Begründung
- Nicht mehr als 2 Stack-Optionen anbieten (Decision Fatigue vermeiden)
- Trade-offs ehrlich benennen — nicht alles schönreden
- CLAUDE.md und Prompt als fertige Dateien liefern, nicht als "Entwurf"
- Wenn der User seinen Stack schon kennt: nicht diskutieren, direkt übernehmen
