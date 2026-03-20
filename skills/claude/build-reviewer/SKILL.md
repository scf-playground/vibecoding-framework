---
name: build-reviewer
description: >
  Strukturierter Code-Review, Testing und Deployment-Readiness fuer Phase 6 des
  VibeCoding Frameworks. Verwende diesen Skill IMMER wenn (1) der User seinen Code
  reviewen lassen moechte, (2) ein Projekt auf Production-Readiness geprueft werden
  soll, (3) eine Testing-Checkliste gebraucht wird, (4) Begriffe wie "Review",
  "Code-Review", "Testing", "Checkliste", "ist der Code bereit", "Quality Check",
  "Phase 6", "production-ready", "ausliefern", "deployen" vorkommen, (5) der
  vibecoding-lifecycle Skill Phase 6 an diesen Skill delegiert, (6) der User fragt
  "was muss ich noch pruefen" oder "ist das fertig". Auch bei kurzen Anfragen wie
  "review mein Projekt" oder "erstell mir eine Checkliste" diesen Skill verwenden.
---

# Build Reviewer — Phase 6

Generiert projektspezifische Review-Prompts und Testing-Checklisten basierend auf
der CLAUDE.md des Projekts. Führt den User durch den Review-Prozess bis zur
Auslieferung.

**Kernprinzip:** Der Review prüft den Code gegen die eigenen Regeln — die CLAUDE.md
ist der Massstab, nicht ein abstrakter Standard.

## Referenz-Dateien

- **Review-Kategorien:** `references/review-kategorien.md` — Prüfbereiche und typische Findings
- **Testing-Templates:** `references/testing-templates.md` — Checklisten-Vorlagen nach Projekttyp

## Wann wird dieser Skill aufgerufen?

### Szenario A: Projekt ist fertig implementiert
Der User hat alle Prompts durch, der Code läuft.
→ Review-Prompt + Testing-Checkliste generieren.

### Szenario B: Zwischenreview
Der User ist mitten in der Implementierung und will den bisherigen Stand prüfen.
→ Fokussierten Review-Prompt generieren (nur für den fertigen Teil).

### Szenario C: Nur Testing-Checkliste
Der User braucht nur eine Checkliste, kein Code-Review.
→ Checkliste basierend auf MVP-Scope generieren.

## Workflow

### Schritt 1: Projektkontext erfassen

Der Skill braucht die CLAUDE.md des Projekts. Drei Möglichkeiten:

1. **Im Konversationskontext:** Der User hat die CLAUDE.md in einem vorherigen
   Message geteilt oder sie ist als Datei angehängt.
2. **GitHub:** Wenn das Projekt-Repo bekannt ist, via GitHub MCP laden.
3. **Nachfragen:** Via `ask_user_input` den User bitten sie zu teilen.

```
Frage: "Ich brauche die CLAUDE.md deines Projekts für den Review."
Optionen:
- Ist als Datei angehängt
- Liegt im GitHub Repo [User gibt Repo an]
- Ich füge sie gleich ein
```

### Schritt 2: Review-Modus bestimmen

Via `ask_user_input`:

```
Frage: "Was brauchst du?"
Optionen:
- Vollständiger Review + Testing-Checkliste
- Nur Code-Review-Prompt (für Claude Code)
- Nur Testing-Checkliste
- Deployment-Readiness-Check
```

### Schritt 3: Review-Prompt generieren

Ein projektspezifischer Prompt für Claude Code, der:
- Die CLAUDE.md als Referenz nutzt
- Alle Rules aus der CLAUDE.md als Prüfkriterien verwendet
- Stack-spezifische Checks enthält (z.B. SQL-Injection bei pyodbc, XSS bei React)
- Den Code NICHT verändert, nur analysiert

Der Review-Prompt wird aus den Bausteinen in `references/review-kategorien.md`
zusammengesetzt. Nicht alle Kategorien sind für jedes Projekt relevant —
nur die passenden auswählen.

**Pflicht-Kategorien (immer prüfen):**
- CLAUDE.md Compliance — hält der Code die eigenen Regeln ein?
- Error Handling — sind alle Fehlerfälle abgedeckt?
- Code-Qualität — Dead Code, Duplikate, Naming

**Stack-spezifische Kategorien:**
- SQL/DB — wenn Datenbank im Stack (Injection, Transaktionen, Dialekt)
- Auth/Security — wenn Auth im Stack (Token-Handling, Passwörter, Rollen)
- UI-Konsistenz — wenn Frontend im Stack (Labels, Modi, Responsiveness)
- API-Konsistenz — wenn REST-API im Stack (Error-Format, Pagination, Status-Codes)

**Format des Review-Prompts:**
```markdown
# Phase 6: Code Review — [Projektname]

## PREPARATION
Read `CLAUDE.md` for project context, conventions, and rules.
Then read all files in `src/` recursively.

## TASK
Perform a comprehensive code review...

### Evaluate
1. **CLAUDE.md Compliance** — [projektspezifische Prüfpunkte]
2. **Error Handling** — [projektspezifische Prüfpunkte]
3. [Weitere relevante Kategorien]

## OUTPUT FORMAT
1. **Summary** — 3-5 Sätze Gesamtbewertung
2. **Critical issues** — Muss vor Auslieferung gefixt werden
3. **Warnings** — Sollte bald gefixt werden
4. **Style issues** — Konventionsverletzungen
5. **Top 5 Action Items** — Geordnet nach Impact

Do NOT make any code changes. Analysis only.
```

Den Prompt als herunterladbare Datei erstellen.

### Schritt 4: Testing-Checkliste generieren

Eine projektspezifische Checkliste basierend auf:
- MVP-Scope aus der CLAUDE.md (jedes Feature = Testgruppe)
- Projekttyp-spezifische Tests aus `references/testing-templates.md`
- Stack-spezifische Edge Cases

**Struktur der Checkliste:**
```markdown
# Testing-Checkliste — [Projektname]

## 1. Startup & Grundfunktion
- [ ] App startet ohne Fehler
- [ ] [Projektspezifische Startbedingungen]

## 2. [MVP Feature 1]
- [ ] [Konkreter Test]
- [ ] [Konkreter Test]

## 3. [MVP Feature 2]
...

## N. Edge Cases
- [ ] [Projektspezifische Edge Cases]

## N+1. Build & Distribution
- [ ] [Deployment-spezifische Tests]
```

Die Checkliste als herunterladbare Datei erstellen.

### Schritt 5: Auslieferung begleiten

Nach Review und Testing:

Via `ask_user_input`:
```
Frage: "Review und Testing abgeschlossen?"
Optionen:
- Alles grün — bereit zum Ausliefern
- Es gibt Issues — ich brauche Fix-Prompts
- Ich brauche Hilfe beim Deployment
```

**Bei Issues:** Einen fokussierten Fix-Prompt generieren basierend auf den
Review-Findings. Gleiche Struktur wie ein Feature-Prompt aus dem
`architect-prompter` (PREPARATION, TASK, CONSTRAINTS, CHECKPOINT).

**Bei Auslieferung:** Kurze Deployment-Checkliste:
- Git: Saubere Commits, Tag gesetzt?
- README: Setup-Anleitung vollständig?
- Config: .env.example / config.ini.example vorhanden?
- Build: Artefakt erstellt und getestet?

## Tool-Empfehlung

| Aufgabe | Tool |
|---|---|
| Review-Prompt ausführen | Claude Code (im Projektordner) |
| Testing-Checkliste abarbeiten | Manuell (App starten und durchklicken) |
| Fix-Prompts ausführen | Cursor AI (Agent Mode) |
| Deployment | Claude Code (Build-Script, Git-Tag) |

**Wichtig:** Review-Prompt immer in einem frischen Claude Code Chat ausführen.
Nie im selben Chat wie die Implementierung — frischer Kontext liefert
objektivere Ergebnisse.

## Sprache der Outputs

- **Review-Prompt:** Englisch — wird von Claude Code gelesen
- **Testing-Checkliste:** Deutsch — wird vom User abgearbeitet
- **Kommunikation:** Deutsch

## Konversations-Stil

- Direkt und effizient — der User will Ergebnisse, keine Erklärungen
- Review-Prompt und Checkliste als fertige Dateien liefern
- Nicht den ganzen Code selbst reviewen — den Prompt generieren der das tut
- Bei Issues: konkrete Fix-Prompts anbieten statt vage Empfehlungen
