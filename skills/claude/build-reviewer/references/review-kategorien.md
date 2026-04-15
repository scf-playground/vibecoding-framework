# Review-Kategorien

Detaillierte Checklisten pro Kategorie. Nicht jede Frage ist für jedes Projekt
relevant — Common Sense walten lassen.

## 1. Code-Qualität

- [ ] Keine auskommentierten Code-Blöcke (Zombie Code)
- [ ] Keine TODO/FIXME ohne Issue-Referenz
- [ ] Keine offensichtliche Duplikation (DRY-Verletzungen)
- [ ] Naming konsistent und sprechend (keine `data2`, `tmp`, `foo`)
- [ ] Funktionen tun eine Sache (keine 200-Zeilen-Monster)
- [ ] Dateien haben klare Verantwortung (kein "utils.ts mit 50 Funktionen")
- [ ] Keine "magic numbers" — Konstanten benannt
- [ ] Konsistente Formatierung (Prettier/Black/etc. eingehalten)

## 2. Konventionen

- [ ] CLAUDE.md Rules eingehalten (Do/Do NOT)
- [ ] Code-Standards aus dem Framework eingehalten (Naming, Imports, Comments)
- [ ] Ordnerstruktur entspricht der Vorgabe
- [ ] Sprache der Kommentare konsistent (nicht Mix aus DE/EN)
- [ ] Imports sortiert (extern → intern → relativ)
- [ ] Bei mehreren Sprachen im Projekt: konsistente Stile pro Sprache

## 3. Funktionalität

- [ ] Alle MVP-Features aus Phase 2 implementiert
- [ ] Happy Path funktioniert (manuell durchgespielt)
- [ ] Edge Cases bedacht:
  - Leere Inputs
  - Sehr grosse Inputs
  - Sonderzeichen / Unicode / Emojis
  - Negative Zahlen / Null-Werte
  - Concurrent Requests (bei APIs)
  - Race Conditions
- [ ] Fehlerfälle haben sinnvolle Reaktion (nicht crashen)

## 4. Error Handling

- [ ] Try/catch an API-Grenzen vorhanden
- [ ] Errors werden geloggt (nicht still verschluckt)
- [ ] User bekommt sinnvolle Fehlermeldungen (nicht "Error 500")
- [ ] Keine `catch (e) {}` ohne Behandlung
- [ ] Async/Promise Errors werden gefangen
- [ ] Error-Typen unterschieden (Validation vs. Server vs. NotFound)
- [ ] Konsistentes Error-Response-Format (z.B. `{ error: string, code: number }`)

## 5. Security

- [ ] Keine hardcodierten Secrets im Code
- [ ] `.env` in `.gitignore`
- [ ] User Input wird validiert (Zod, Pydantic, etc.)
- [ ] SQL Queries via Prepared Statements / ORM (kein String-Concat)
- [ ] Auth-Endpoints haben Rate Limiting (wenn relevant)
- [ ] Passwörter werden gehasht (bcrypt/argon2), nie plain
- [ ] Sensitive Daten nicht in Responses (z.B. `password_hash`)
- [ ] CORS sinnvoll konfiguriert
- [ ] Keine offenen Debug-Endpoints in Production
- [ ] User können nur eigene Daten zugreifen (Authorization-Check pro Query)

## 6. Performance

- [ ] Keine N+1 Queries (DB-Calls in Loops)
- [ ] Indices auf häufig gefilterten Spalten
- [ ] Pagination bei List-Endpoints
- [ ] Kein synchrones I/O wo async möglich
- [ ] Keine unnötigen Re-Renders (React) / Watcher (Vue)
- [ ] Bilder in sinnvoller Grösse (nicht 10 MB Original-PNGs)
- [ ] Bei Frontend: Bundle-Grösse im Blick (keine 5 MB JS)

## 7. Tests

- [ ] Tests vorhanden für kritische Logik
- [ ] Happy Path getestet
- [ ] Edge Cases getestet
- [ ] Tests laufen grün
- [ ] Test-Coverage angemessen (kein Dogma — kritische Pfade reichen oft)
- [ ] Bei APIs: mindestens Integration-Tests für Auth-Flow

Bei Projekten ohne Tests: Nicht zwingend als Critical melden — als Suggestion
mit Hinweis welche Bereiche am dringendsten Tests bräuchten.

## 8. Dokumentation

- [ ] README.md vorhanden mit:
  - Was das Projekt macht (1–2 Sätze)
  - Setup-Anleitung
  - Wie ausführen (dev + production)
- [ ] API-Endpoints dokumentiert (bei APIs)
- [ ] Environment Variables in `.env.example` mit Beschreibung
- [ ] CLAUDE.md aktuell (Stack/Struktur stimmt mit Code überein)
- [ ] Bei komplexen Funktionen: kurze Kommentare die WAS erklären

## 9. Deployment-Readiness

- [ ] Build läuft fehlerfrei (`npm run build` / `cargo build` / etc.)
- [ ] Keine Console-Errors oder Warnings beim Build
- [ ] `.env.example` vorhanden und vollständig
- [ ] Keine echten Secrets im Repo (Git History prüfen!)
- [ ] Production-Config getrennt von Dev-Config
- [ ] Logging in Production sinnvoll (nicht zu viel, nicht zu wenig)
- [ ] Health-Check-Endpoint vorhanden (bei APIs)
- [ ] Dockerfile / Compose-File funktioniert (falls relevant)
- [ ] Backup-Strategie definiert (bei Services mit DB)

## Severity-Einstufung

Faustregeln zur Einstufung von Findings:

**Critical:**
- Sicherheitslücke (Secret im Code, fehlende Auth, SQL Injection)
- Crash bei normalem Use Case
- Datenverlust möglich
- MVP-Feature funktioniert nicht

**Warning:**
- Bug in Edge Case
- Schlechte Performance bei realistischer Last
- Fehlende Validation
- Code-Qualität die Wartung erschwert
- Fehlende Tests für kritische Pfade

**Suggestion:**
- Refactoring-Möglichkeit
- Stilfragen
- Optionale Verbesserungen
- Nice-to-have Features
- Dokumentations-Lücken die nicht blockieren

## Faustregeln zur Anzahl Findings

Nicht jede Datei muss ein Finding produzieren. Anhaltspunkte:

- **Kleines Projekt** (< 1000 LOC): 0–3 Critical, 0–5 Warnings, 0–10 Suggestions
- **Mittleres Projekt** (1000–10’000 LOC): 0–5 Critical, 3–10 Warnings, 5–20 Suggestions
- **Grosses Projekt** (> 10’000 LOC): Sample-basiert reviewen, klar markieren was nicht reviewt wurde

Wenn die Liste länger wird: priorisieren statt verwässern.
