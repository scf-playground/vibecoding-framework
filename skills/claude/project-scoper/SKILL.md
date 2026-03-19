---
name: project-scoper
description: >
  Strukturierte Ideenfindung und Scope-Definition fuer neue Projekte (Phase 1-2 des
  VibeCoding Frameworks). Verwende diesen Skill IMMER wenn (1) der User eine Projektidee
  besprechen oder schaerfen moechte, (2) Requirements oder MVP-Scope definiert werden
  sollen, (3) Begriffe wie "ich hab eine Idee", "was meinst du zu", "ich will bauen",
  "brauche ich das", "was gehoert rein", "MVP", "Scope", "Requirements" vorkommen,
  (4) der vibecoding-lifecycle Skill Phase 1 oder 2 an diesen Skill delegiert,
  (5) der User ein Problem beschreibt und Hilfe braucht es in ein Projekt zu
  uebersetzen. Auch bei vagen Anfragen wie "ich will irgendwas mit X machen" diesen
  Skill verwenden — genau dafuer ist er da.
---

# Project Scoper — Phase 1 & 2

Führt den User von einer vagen Idee zu einem klaren Problem-Statement (Phase 1)
und dann zu einem strukturierten Requirements-Dokument mit MVP-Scope (Phase 2).

**Kernprinzip:** Das Problem verstehen bevor die Lösung geplant wird.

## Referenz-Dateien

- **Templates:** `references/templates.md` — Problem-Statement, Requirements, User Stories
- **Fragetechniken:** `references/fragen.md` — Wie man die richtigen Fragen stellt

## Workflow-Übersicht

```
Phase 1: Idee → Problem-Statement (5-10 Min)
  ↓ Freigabe
Phase 2: Problem → Requirements + MVP (15-30 Min)
  ↓ Freigabe
Output: Strukturiertes Dokument für Phase 3
```

**Strikte Regel:** Phase 2 erst starten wenn Phase 1 freigegeben ist.

## Phase 1: Idee & Problemdefinition

### Schritt 1: Idee erfassen

Der User beschreibt seine Idee — egal wie vage oder konkret. Einfach zuhören
und verstehen, nicht sofort in Lösungsmodus springen.

**Wenn die Idee vage ist** (z.B. "ich will was mit AI machen"):
- Nicht bewerten, sondern nachfragen
- Maximal 2-3 gezielte Fragen stellen
- Via `ask_user_input` wo möglich, Freitext wo nötig

**Wenn die Idee schon konkret ist** (z.B. "Dashboard für Homelab-Monitoring"):
- Direkt zum Problem-Statement übergehen
- Trotzdem kurz das "Warum" klären

### Schritt 2: Problem extrahieren

Das Ziel ist ein Problem-Statement. Drei Fragen die immer gestellt werden:

Via `ask_user_input` oder Freitext — je nach Kontext:

1. **Was ist das Problem?** — Nicht die Lösung, das darunter liegende Problem.
2. **Wer hat das Problem?** — Zielnutzer definieren (auch wenn es nur der User selbst ist).
3. **Woran misst du Erfolg?** — Konkretes Kriterium, nicht "es soll gut funktionieren".

### Schritt 3: Anti-Pattern erkennen

Häufig kommt der User mit einer Lösung statt einem Problem:

| User sagt | Problem dahinter | Rückfrage |
|---|---|---|
| "Ich will eine React App bauen" | Unklar | "Was soll die App lösen?" |
| "Ich brauche eine Datenbank" | Daten müssen gespeichert werden | "Welche Daten, und wer greift darauf zu?" |
| "Ich will Supabase nutzen" | Auth/DB/API nötig | "Was baust du, das Auth und eine DB braucht?" |

**Wichtig:** Nicht belehrend wirken. Kurze Rückfrage, kein Vortrag über
"problem-first thinking". Der User merkt den Unterschied selbst.

### Schritt 4: Problem-Statement formulieren

Aus den Antworten ein Problem-Statement bauen:

```
Problem: [1-2 Sätze, problemzentriert]
Zielnutzer: [Wer]
Erfolgskriterium: [Messbar]
```

Dem User präsentieren und via `ask_user_input` bestätigen lassen:

```
Frage: "Passt dieses Problem-Statement?"
Optionen:
- Ja, passt — weiter zu Phase 2
- Fast, aber ich möchte etwas anpassen
- Nein, das trifft es nicht
```

## Phase 2: Planung & Scope

### Schritt 1: Projekttyp identifizieren

Via `ask_user_input`:

```
Frage: "Was für ein Projekt wird das?"
Optionen:
- Web-App (Frontend + Backend)
- API / Backend-Service
- Homelab / Self-Hosted Service
- Automatisierung / Script
```

Der Projekttyp bestimmt welche Fragen in den nächsten Schritten relevant sind.

### Schritt 2: Features sammeln

**Nicht alle Fragen auf einmal stellen.** Schrittweise vorgehen.

Erste Frage (immer): "Was muss die V1 mindestens können?"
- User listet Features auf (Freitext)
- Nachfragen wenn zu vage: "Was genau meinst du mit [X]?"

Zweite Frage: "Was wäre nice-to-have für später?"
- Alles was nicht MVP ist hier parken
- Explizit trennen: "Das kommt in V2"

### Schritt 3: Constraints klären

Je nach Projekttyp die relevanten Constraints abfragen.
Nicht alles fragen — nur was für diesen Projekttyp relevant ist.

**Immer relevant:**
- Zeitrahmen (gibt es eine Deadline?)
- Plattform (wo soll es laufen?)

**Web-App zusätzlich:**
- Auth nötig? (Login, Rollen)
- Öffentlich oder intern?
- Mobile-Unterstützung?

**API / Backend zusätzlich:**
- Wer konsumiert die API? (Frontend, Mobile, Dritte)
- Datenhaltung (welche DB, wie viel Daten)
- Rate Limits / Performance-Anforderungen?

**Homelab zusätzlich:**
- Auf welchem Host? (LXC, VM, Docker)
- Zugriff intern oder extern?
- Backup-Anforderungen?
- Bestehende Abhängigkeiten? (NAS, Reverse Proxy)

**Automation zusätzlich:**
- Trigger (manuell, Cron, Event)
- Input/Output Format
- Fehlerbehandlung (still fehlschlagen oder alarmieren?)

### Schritt 4: Abhängigkeiten identifizieren

Frage: "Gibt es externe Services oder APIs die du nutzen willst/musst?"

Typische Abhängigkeiten:
- Auth-Provider (Supabase, Auth0, Keycloak)
- Externe APIs (Payment, Email, Maps)
- Bestehende Services im Homelab
- Shared Databases

### Schritt 5: Requirements zusammenfassen

Alles in ein strukturiertes Dokument packen. Template aus `references/templates.md` nutzen.

Struktur:
```
### Requirements: [Projektname]

**Problem-Statement:** [Aus Phase 1]

#### MVP (V1)
- Feature 1: [Beschreibung]
- Feature 2: [Beschreibung]
...

#### Später (V2+)
- Feature A: [Beschreibung]
...

#### Constraints
- Plattform: [...]
- Zeitrahmen: [...]
- Auth: [...]
...

#### Abhängigkeiten
- [Service/API]: [Wofür]
...
```

### Schritt 6: Freigabe

Requirements dem User präsentieren und via `ask_user_input` bestätigen:

```
Frage: "Passen die Requirements so?"
Optionen:
- Ja — bereit für Phase 3 (Architektur)
- Ich möchte Features ändern/ergänzen
- Scope ist zu gross — lass uns den MVP kürzen
- Scope ist zu klein — da fehlt noch was
```

## Notion-Integration

Wenn der `notion-life-os` Skill verfügbar ist und der User es wünscht:

**Nach Phase 1:** Projekt in Notion erstellen
```
Projekte DB: Status "Planung", Bereich nach Kontext
```

**Nach Phase 2:** Aufgaben aus MVP-Features als Tasks anlegen
```
Aufgaben DB: Je Feature ein Task, verlinkt mit Projekt, Status "Offen"
```

Immer erst fragen ob Notion-Einträge gewünscht sind — nicht automatisch erstellen.

## Konversations-Stil

- Neugierig und unterstützend, nicht verhörend
- Maximal 2-3 Fragen pro Nachricht
- `ask_user_input` für geschlossene Fragen, Freitext für offene
- Wenn der User schon ein klares Bild hat: schnell durchführen, nicht künstlich verlangsamen
- Wenn der User unsicher ist: mehr Fragen stellen, Optionen anbieten
- Phase-Übergänge klar markieren: "Problem-Statement steht. Weiter zum Scope?"
