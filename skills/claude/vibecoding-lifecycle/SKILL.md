---
name: vibecoding-lifecycle
description: >
  Master-Workflow fuer AI-gestuetztes Entwickeln (VibeCoding). Steuert den gesamten
  Entwicklungs-Lifecycle von der Idee bis zum fertigen Deployment in 6 Phasen mit
  strikten Freigabe-Gates. Verwende diesen Skill IMMER wenn (1) der User ein neues
  Projekt starten oder planen moechte, (2) Begriffe wie "neues Projekt", "neue App",
  "ich will bauen", "Projektstart", "Idee umsetzen", "lass uns planen" vorkommen,
  (3) der User von Cursor AI, Claude Code, Antigravity oder VibeCoding spricht und
  eine neue Umsetzung plant, (4) der User fragt wie er besser/strukturierter
  entwickeln kann, (5) der User eine CLAUDE.md oder .cursorrules erstellen will.
  Auch bei kurzen Anfragen wie "neues Projekt: Task Manager" oder "ich hab eine Idee
  fuer eine App" diesen Skill verwenden. Dieser Skill orchestriert andere Skills
  (project-scoper, architect-prompter, agent-md-generator, build-reviewer) und
  erzwingt dass keine Phase uebersprungen wird.
---

# VibeCoding Lifecycle — Master Skill

Orchestriert den gesamten Entwicklungs-Lifecycle in 6 Phasen. Jede Phase hat ein
klares Ziel, definierte Inputs/Outputs und ein Freigabe-Gate. Keine Phase wird
übersprungen.

**Leitprinzip:** Je mehr Denkarbeit VOR dem Coding passiert, desto sauberer das Ergebnis.

## Referenz-Dateien

Lese je nach Phase die passende Referenz aus `references/`:

- **Phasen-Details:** `references/phasen.md` — Detaillierte Beschreibung aller 6 Phasen
- **Tool-Guide:** `references/tool-guide.md` — Wann welches Tool einsetzen
- **Anti-Patterns:** `references/anti-patterns.md` — Häufige Fehler und wie man sie vermeidet

## Workflow-Übersicht

```
Phase 1: Idee & Problem     → Problem-Statement
Phase 2: Planung & Scope    → Requirements + MVP-Definition
Phase 3: Architektur & Stack → Tech-Entscheidung + Ordnerstruktur
  ↓ Komplexitäts-Gate (S/M/L)
Phase 4: Prompt Engineering  → CLAUDE.md + Prompt(s)
Phase 5: Umsetzung          → Funktionierender Code
Phase 6: Review & Testing   → Production-ready Code
```

**Strikte Regel:** Zwischen jeder Phase auf explizite Bestätigung warten.
Kein Phasensprung ohne OK via `ask_user_input`.

## Aktivierung

Wenn dieser Skill triggert:

1. Begrüssung + kurze Erklärung des Workflows (1-2 Sätze, nicht mehr)
2. Prüfen ob der User ein komplett neues Projekt startet oder mitten drin einsteigt
3. Bei neuem Projekt: Phase 1 starten
4. Bei bestehendem Projekt: Fragen wo der User steht und dort einsteigen

Via `ask_user_input` klären:

```
Frage: "Was möchtest du tun?"
Optionen:
- Neues Projekt von Grund auf starten
- Bestehendes Projekt weiterentwickeln
- Nur eine CLAUDE.md / Prompt erstellen
- Framework-Übersicht zeigen
```

## Phase 1: Idee & Problemdefinition

**Ziel:** Vom vagen "ich will was bauen" zu einem klaren Problem-Statement.

**Ablauf:**
1. User beschreibt seine Idee (Freitext)
2. Gezielt nachfragen — maximal 3 Fragen, nicht mehr:
   - Was ist das Problem das gelöst wird?
   - Wer benutzt es?
   - Woran erkennst du dass es funktioniert?
3. Problem-Statement formulieren (2-3 Sätze)
4. Dem User präsentieren und bestätigen lassen

**Output:** Problem-Statement — kurz, klar, problemzentriert (nicht lösungszentriert).

**Anti-Pattern erkennen:** Wenn der User direkt mit einer Lösung kommt ("Ich will eine
React App mit Supabase"), zurücklenken: "Lass uns erst das Problem klären, dann den
Stack." Nicht belehrend, sondern als kurze Rückfrage.

**Freigabe-Gate:** Problem-Statement via `ask_user_input` bestätigen lassen bevor weiter.

## Phase 2: Planung & Scope

**Ziel:** Requirements und MVP-Scope definieren.

**Wenn der `project-scoper` Skill verfügbar ist:** Diesen aufrufen und die Phase an
ihn delegieren. Er führt den User strukturiert durch Requirements-Erfassung.

**Wenn nicht verfügbar, selbst durchführen:**

1. Features sammeln — via `ask_user_input` und Freitext
   - Was muss die V1 können? (MVP)
   - Was wäre nice-to-have für später?
2. Constraints klären:
   - Zeitrahmen
   - Plattform (Web, Mobile, CLI, Self-hosted)
   - Abhängigkeiten (externe APIs, Auth-Provider, etc.)
3. Requirements zusammenfassen als strukturierte Liste

**Output:** Requirements-Dokument mit klarer MVP-Abgrenzung.

**Freigabe-Gate:** Requirements via `ask_user_input` bestätigen lassen.

## Phase 3: Architektur & Tech-Stack

**Ziel:** Tech-Stack wählen und Systemarchitektur definieren.

**Wenn der `architect-prompter` Skill verfügbar ist:** Phase 3-4 an ihn delegieren.
Er enthält auch das Komplexitäts-Gate.

**Wenn nicht verfügbar, selbst durchführen:**

1. Basierend auf Requirements: 1-2 Stack-Vorschläge machen
2. Für jeden Vorschlag: Tech-Stack + Begründung + Trade-offs
3. User wählt via `ask_user_input`
4. Ordnerstruktur definieren
5. Datenhaltung und Zugriff klären

**Referenz-Skills einbeziehen:** Wenn relevant, Inhalte aus dem GitHub Repo referenzieren:
- `code-standards` für Sprach-Konventionen
- `docker-compose` + `homelab-kontext` für Homelab-Projekte
- `ui-design` Standards für Frontend-Projekte

**Output:** Tech-Stack-Entscheidung + Ordnerstruktur + Architektur-Skizze.

**Freigabe-Gate:** Architektur via `ask_user_input` bestätigen lassen.

## Komplexitäts-Gate (zwischen Phase 3 und 4)

**Ziel:** Bestimmen ob das Projekt einen Einzelprompt oder eine Prompt-Sequenz braucht.

Nach der Architektur-Freigabe die Projektkomplexität bewerten:
- 5 Faktoren à 0-2 Punkte (Schichten, Endpoints, Datenmodell, Integrationen, Deployment)
- S (0-3) = Einzelprompt, M (4-6) = 2-3 Prompts, L (7+) = 4-6 Prompts

Details: `architect-prompter/references/prompt-sequenz.md`
Projekttyp-Vorlagen: `projekttypen/web-app/` und `projekttypen/api-backend/`

Dem User die Einschätzung transparent mitteilen und bestätigen lassen.
Das verhindert das "Alles-auf-einmal-Syndrom" (siehe `references/anti-patterns.md`).

## Phase 4: Prompt Engineering

**Ziel:** CLAUDE.md und Prompt(s) für Cursor/Claude Code generieren.

**Wenn der `agent-md-generator` Skill verfügbar ist:** CLAUDE.md-Generierung an
ihn delegieren.

**Wenn nicht verfügbar, selbst durchführen:**

1. CLAUDE.md zusammenbauen aus den Ergebnissen von Phase 1-3
2. Bei **Grösse S:** Einen einzelnen Prompt generieren
3. Bei **Grösse M/L:** Eine nummerierte Prompt-Sequenz generieren:
   - Übersicht aller Schritte mit Titel und Prüfpunkt
   - Nur den ersten Prompt als kopierbaren Block liefern
   - Nachfolgende Prompts erst auf Anfrage (damit Kontext aktuell bleibt)

Die CLAUDE.md wird als Datei erstellt und dem User präsentiert.

**Output:** CLAUDE.md + Einzelprompt ODER CLAUDE.md + Prompt-Sequenz.

**Freigabe-Gate:** CLAUDE.md und Prompt(s) via `ask_user_input` bestätigen lassen.

## Phase 5: Umsetzung

**Ziel:** Code generieren mit Cursor AI oder Claude Code.

**Dieser Skill begleitet die Umsetzung, führt sie aber nicht selbst durch.**
Die eigentliche Code-Generierung passiert in Cursor/Claude Code mit dem Prompt
aus Phase 4.

**Bei Prompt-Sequenz:** Der User kommt nach jedem Schritt zurück für den nächsten
Prompt. Dabei kann er beschreiben was sich geändert hat oder was angepasst werden
muss — das fliesst in den nächsten Prompt ein.

**Regeln die der User beachten soll:**
1. Schritt für Schritt — nicht alles auf einmal generieren lassen
2. Nach jedem Feature: prüfen und committen bevor weiter
3. Bei Problemen: neuen Prompt mit Kontext statt "fix this"
4. Git Commits nach jedem abgeschlossenen Schritt

**Tool-Empfehlung** (via `ask_user_input` wenn unklar):

| Situation | Tool |
|---|---|
| Neues Projekt aufsetzen | Cursor AI (Composer) |
| Feature implementieren | Cursor AI |
| Schnelle Änderungen | Claude Code |
| Debugging | Claude Code |
| Multi-File Operationen | Cursor AI |

**Wenn der User in Claude Chat ist:** Darauf hinweisen dass Phase 5 in Cursor/CC
stattfindet. CLAUDE.md und Prompt bereitstellen zum Kopieren.

## Phase 6: Review & Testing

**Ziel:** Code prüfen, testen, production-ready machen.

**Wenn der `build-reviewer` Skill verfügbar ist:** Review an ihn delegieren.

**Wenn nicht verfügbar, selbst durchführen:**

1. Review-Prompt generieren (strukturiert wie Prompt Crafter):
   - Code-Qualität prüfen
   - Edge Cases identifizieren
   - Security-Basics checken
2. Testing-Checkliste bereitstellen
3. Dokumentations-Check

**Checkliste:**
- [ ] Code-Review durchgeführt
- [ ] Kernfunktionalität manuell getestet
- [ ] Edge Cases geprüft
- [ ] README aktualisiert
- [ ] Git: Saubere Commits, getaggt

**Feedback-Loop:** Bei Problemen → zurück zu Phase 4 (neuer Prompt) oder Phase 5.

## Notion-Integration

Wenn der `notion-life-os` Skill verfügbar ist und der User es wünscht:

1. **Nach Phase 1:** Projekt in Notion Projekte-DB erstellen (Status: Planung)
2. **Nach Phase 2:** Aufgaben aus Requirements als Tasks anlegen
3. **Nach Phase 6:** Projektstatus auf "Aktiv" setzen

Nicht automatisch Notion-Einträge erstellen — immer zuerst fragen ob gewünscht.

## Konversations-Stil

- Knapp und direkt — keine langen Erklärungen wenn nicht nötig
- Fragen immer über `ask_user_input` stellen, nicht als Textlisten
- Phase-Übergänge klar markieren: "Phase 2 abgeschlossen. Weiter zu Phase 3?"
- Bei Abweichungen vom Workflow: kurz darauf hinweisen, nicht blockieren
- Wenn der User ungeduldig wird und Phasen überspringen will: einmal darauf
  hinweisen dass Planung Zeit spart, dann respektieren und weitermachen
