# Anti-Patterns

Häufige Fehler im VibeCoding-Workflow und wie man sie vermeidet.

## Phase-Skipper

**Problem:** Direkt in Cursor springen ohne Planung.
**Symptom:** "Bau mir eine App die X kann" als erster Prompt.
**Lösung:** Immer mit Phase 1 starten. Auch bei kleinen Projekten: 5 Minuten
Problem-Statement schreiben spart Stunden Debugging.

**Ausnahme:** Bei trivialen Änderungen an bestehenden Projekten (Typo fixen,
Farbe ändern) ist der volle Workflow übertrieben. Faustregel: Wenn die
Änderung in einem Satz beschreibbar ist, kann man direkt in Phase 5 einsteigen.

## Lösung-zuerst-Denker

**Problem:** Mit dem Tech-Stack starten statt mit dem Problem.
**Symptom:** "Ich will React + Supabase + Tailwind + Vercel nutzen."
**Lösung:** Stack-Entscheidung gehört in Phase 3, NACH den Requirements.
Manchmal ist die beste Lösung gar keine App sondern ein Bash-Script.

## Prompt-Minimalist

**Problem:** Zu kurze, vage Prompts an Cursor/CC.
**Symptom:** "Bau die Login-Seite", "Fix the bug", "Add dark mode"
**Lösung:** Strukturierte Prompts mit PREPARATION, TASK, CONSTRAINTS.
Ein 20-Zeilen-Prompt spart 5 Iterationen a 2 Minuten.

### Beispiel-Transformation:
```
SCHLECHT: "Bau eine API für Todos"

GUT:
## PREPARATION
Read CLAUDE.md for project context and conventions.

## TASK
Implement the Todo API endpoints as specified in the requirements:
- GET /todos — list user's todos, support ?status=done|pending
- POST /todos — create todo (title required, description optional)
- PATCH /todos/:id — update title, description, or status
- DELETE /todos/:id — soft delete (set deleted_at timestamp)

## CONSTRAINTS
- All endpoints require authentication (JWT middleware)
- Users can only access their own todos
- Consistent error response format: { error: string, code: number }
- Use Prisma ORM for database operations
- Follow code-standards for naming and error handling
```

## Kontext-Amnesie

**Problem:** Nicht genug Kontext im Prompt mitgeben.
**Symptom:** AI produziert Code der nicht zum Projekt passt.
**Lösung:** CLAUDE.md pflegen und im PREPARATION-Block referenzieren.
Der Agent kennt nur was er lesen kann — nicht was du im Kopf hast.

## Alles-auf-einmal-Syndrom

**Problem:** Gesamtes Projekt in einem Prompt generieren lassen.
**Symptom:** 2000-Zeilen-Response die halb funktioniert.
**Lösung:** Feature für Feature. Erst DB-Schema, dann API, dann Frontend.
Jeder Schritt: generieren → prüfen → committen → nächster Schritt.

## Fix-this-Loop

**Problem:** Immer wieder "fix this" schicken wenn etwas nicht funktioniert.
**Symptom:** 15 Iterationen für einen Bug, Code wird immer schlimmer.
**Lösung:** Neuen Chat starten mit frischem Kontext:
- Was ist das erwartete Verhalten?
- Was passiert stattdessen?
- Welche Fehlermeldung erscheint?
- Was hast du schon versucht?

Nach 3 gescheiterten Fix-Versuchen: Stoppen, Problem neu analysieren,
eventuell Ansatz überdenken statt weiterfixen.

## Commit-Verweigerer

**Problem:** Stundenlang coden ohne zu committen.
**Symptom:** Riesiger Diff, schwer zu reviewen, kein Rollback möglich.
**Lösung:** Nach jedem abgeschlossenen Feature/Fix committen.
Faustregel: Wenn du den Commit nicht in einem Satz beschreiben kannst,
ist er zu gross.

## Copy-Paste-Entwickler

**Problem:** Code aus AI-Output kopieren ohne zu verstehen.
**Symptom:** Projekt funktioniert, aber niemand weiss warum.
**Lösung:** Mindestens den High-Level-Flow verstehen. Bei kritischem Code
(Auth, Datenzugriff, Payments): Zeile für Zeile durchgehen.
VibeCoding heisst nicht "blind vertrauen".

## Scope-Creep

**Problem:** Während der Umsetzung immer neue Features dazunehmen.
**Symptom:** V1 wird nie fertig, Projekt wird immer komplexer.
**Lösung:** Strikt am MVP-Scope aus Phase 2 festhalten. Neue Ideen notieren
(Notion Notizen-DB) aber nicht sofort umsetzen. V1 shippen, dann V2 planen.

## Team-Anti-Patterns

### Kein gemeinsamer Standard
**Problem:** Jeder promptet anders, Code-Stil variiert.
**Lösung:** Gemeinsames Framework-Repo, gleiche CLAUDE.md, gleiche Standards.

### Parallele Änderungen ohne Absprache
**Problem:** Zwei Leute lassen AI den gleichen Code umschreiben.
**Lösung:** Feature-Branches, klare Aufgabenteilung, kurze Sync-Zyklen.
