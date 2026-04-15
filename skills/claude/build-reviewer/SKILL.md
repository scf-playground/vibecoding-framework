---
name: build-reviewer
description: >
  Strukturiertes Code-Review, Test-Empfehlungen und Deployment-Readiness-Check
  (Phase 6 des VibeCoding Frameworks). Verwende diesen Skill IMMER wenn (1) der
  User eine Implementierung pruefen oder reviewen lassen moechte, (2) ein Projekt
  vor dem Deployment steht, (3) Begriffe wie "Code-Review", "Review", "pruefe
  meinen Code", "ist das produktionsreif", "kann ich das deployen",
  "Code-Qualitaet", "Edge Cases", "Security-Check" vorkommen, (4) der
  vibecoding-lifecycle Skill Phase 6 an diesen Skill delegiert, (5) der User nach
  einem Cursor- oder Claude-Code-Build wissen will ob der Code gut ist. Auch bei
  kurzen Anfragen wie "schau mal drueber" oder "passt das so?" auf Code diesen
  Skill verwenden.
---

# Build Reviewer — Phase 6

Strukturiertes Review von implementiertem Code. Prüft Qualität, Funktionalität,
Security und Deployment-Readiness. Liefert einen klaren Report mit priorisierten
Action Items.

**Kernprinzip:** Lieber wenige fundierte Findings als eine endlose Liste. Der
User soll nach dem Review wissen WAS er als nächstes tun muss.

## Referenz-Dateien

- **Kategorien:** `references/review-kategorien.md` — Detaillierte Checklisten pro Kategorie
- **Report-Format:** `references/report-template.md` — Vorlage für den Output

## Workflow-Übersicht

```
1. Kontext laden       → CLAUDE.md, Repo-Struktur, Requirements
2. Scope klären        → Was soll reviewt werden?
3. Review-Tiefe wählen → Quick / Standard / Deep
4. Review durchführen  → Systematisch nach Kategorien
5. Report ausgeben     → Strukturiertes Markdown
6. Action Items        → Optional als Issues / Notion-Tasks
```

## Tool-Hinweis: Interaktive Fragen

Dieser Skill stellt mehrfach Auswahlfragen an den User. Je nach Interface:

- **Mit `ask_user_input` Widget** (z.B. Claude Chat): Tool nutzen.
- **Ohne Widget** (z.B. Claude Code im Terminal): Frage als normalen Text mit
  nummerierten Optionen stellen, auf Antwort warten bevor weiter.

Beispiel ohne Widget:

```
Welche Review-Tiefe?
1. Quick    — Critical Issues only (5–10 Min)
2. Standard — Alle Kategorien, wichtige Findings (15–30 Min)
3. Deep     — Vollständig mit Verbesserungsvorschlägen (30–60 Min)

[1/2/3]
```

## Schritt 1: Kontext laden

Bevor irgendwas reviewt wird, den Projektkontext verstehen:

1. **CLAUDE.md lesen** — Falls vorhanden im Projekt-Root
2. **Repo-Struktur erfassen** — Welche Sprachen, welches Framework, welche Ordner
3. **Requirements** — Falls verlinkt oder im README, MVP-Scope verstehen

Wenn keine CLAUDE.md vorhanden ist:
- Nicht abbrechen — auf Basis des Codes selbst reviewen
- Im Report explizit darauf hinweisen ("Kein CLAUDE.md gefunden — Konventionen
  wurden aus dem Code abgeleitet")

## Schritt 2: Scope klären

Frage an den User:

```
Was soll reviewt werden?
- Komplettes Projekt
- Einzelnes Feature / Modul
- Pull Request / Diff
- Spezifische Datei(en)
```

Bei Diff/PR: Auch den umliegenden Kontext laden, nicht nur die geänderten
Zeilen — sonst fehlen wichtige Bezüge.

## Schritt 3: Review-Tiefe wählen

Frage an den User:

```
Review-Tiefe?
- Quick (5–10 Min)    — Nur Critical Issues: Crashes, Security, MVP-Bruch
- Standard (15–30 Min) — Alle Kategorien, wichtige Findings
- Deep (30–60 Min)     — Alle Kategorien, alle Findings, Verbesserungsvorschläge
```

Tiefen-Empfehlung je nach Anlass:
- Vor Deployment → Standard oder Deep
- Mid-Development Sanity Check → Quick
- "Lass uns mal aufräumen" → Deep

## Schritt 4: Review durchführen

Systematisch alle Kategorien aus `references/review-kategorien.md` durchgehen.
Pro Finding notieren:

- **Severity:** `critical` / `warning` / `suggestion`
- **Location:** Datei + Zeile (z.B. `src/routes/auth.ts:42`)
- **Description:** Was ist das Problem
- **Suggested Fix:** Konkreter Lösungsvorschlag

Bei **Quick-Modus**: Nur `critical` notieren.
Bei **Standard**: `critical` + `warning`.
Bei **Deep**: Alle drei Severities.

**Kategorien-Übersicht:**
- Code-Qualität (Duplikation, Naming, Dead Code, Komplexität)
- Konventionen (CLAUDE.md / Code-Standards eingehalten?)
- Funktionalität (MVP-Scope erfüllt, Edge Cases)
- Error Handling
- Security (Input Validation, Secrets, Auth)
- Performance (N+1 Queries, Pagination, etc.)
- Tests (Vorhanden? Decken kritische Pfade ab?)
- Dokumentation (README, API Docs, Env Vars)
- Deployment-Readiness (Build, .env.example, Secrets nicht im Repo)

Details und Checklisten pro Kategorie: siehe Referenz-Datei.

## Schritt 5: Report ausgeben

Format aus `references/report-template.md` verwenden. Kurzform:

```markdown
# Code Review: [Projektname]

**Datum:** YYYY-MM-DD
**Scope:** [...]
**Modus:** Quick / Standard / Deep

## Zusammenfassung
[3–5 Sätze Gesamteinschätzung]
**Production-Ready:** Ja / Nein / Mit Einschränkungen

## Critical Issues (Must Fix)
[Findings mit Severity = critical]

## Warnings (Should Fix)
[Findings mit Severity = warning]

## Suggestions (Nice to Have)
[Findings mit Severity = suggestion — nur in Standard/Deep]

## Top 5 Action Items
[Priorisierte Liste der nächsten Schritte]
```

Report entweder als Datei im Projekt ablegen unter
`docs/reviews/YYYY-MM-DD-review.md` oder dem User direkt im Chat ausgeben —
je nach Kontext und Präferenz.

## Schritt 6: Action Items (optional)

Frage an den User:

```
Action Items übernehmen?
- Als GitHub Issues anlegen (mit Labels nach Severity)
- Als Notion Tasks anlegen (wenn notion-life-os Skill verfügbar)
- Nein — ich kümmere mich selbst
```

**Bei GitHub:**
- Title: Finding-Description (kurz)
- Body: Location + Description + Suggested Fix
- Labels: `critical` / `warning` / `suggestion`

**Bei Notion:**
- Tasks im Aufgaben-DB anlegen
- Mit dem Projekt verlinken
- Priorität nach Severity setzen

## Was dieser Skill NICHT tut

- Keine automatischen Code-Änderungen — der User entscheidet was umgesetzt wird
- Kein Refactoring durchführen — nur identifizieren
- Keine Tests schreiben — nur fehlende Tests aufzeigen
- Keine Deployment-Aktionen — nur Readiness prüfen

Für die Umsetzung: User mit Cursor / Claude Code arbeiten lassen, ggf. mit dem
`debug-helper` oder `architect-prompter` Skill für gezielte Prompts.

## Konversations-Stil

- Findings konkret und actionable, keine vagen "verbessere dies"
- Bei Critical Issues klar benennen, nicht beschönigen
- Bei Suggestions knapp halten — User soll nicht überwältigt werden
- Report immer als ganzes Dokument, nicht häppchenweise im Chat
- Wenn Code wirklich gut ist: das auch sagen — nicht künstlich Findings konstruieren
- Severity ehrlich einstufen — nicht alles als "critical" markieren um wichtig zu wirken
