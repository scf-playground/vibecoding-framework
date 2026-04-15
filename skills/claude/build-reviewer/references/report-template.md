# Report-Template

Vorlage für den Output des Build-Reviewers. Wird als Markdown-Datei
gespeichert unter `docs/reviews/YYYY-MM-DD-review.md` oder dem User
direkt im Chat präsentiert.

## Vollständige Vorlage

```markdown
# Code Review: [Projektname]

**Datum:** YYYY-MM-DD
**Reviewer:** Build-Reviewer (Claude)
**Scope:** [Was wurde reviewt]
**Modus:** Quick / Standard / Deep
**Commit/Branch:** [SHA oder Branch-Name]

## Zusammenfassung

[3–5 Sätze Gesamteinschätzung. Was läuft gut, was sind die Hauptprobleme,
gibt es Blocker für Production?]

**Production-Ready:** Ja / Nein / Mit Einschränkungen

## Critical Issues (Must Fix)

### 1. [Kurztitel]
- **Location:** `src/routes/auth.ts:42`
- **Description:** Passwort wird im Klartext geloggt beim Login-Fehler
- **Suggested Fix:** Logging entfernen oder Passwort vor dem Log redacten
- **Risk:** Passwörter landen in Production-Logs

### 2. [Kurztitel]
- **Location:** `...`
- **Description:** ...
- **Suggested Fix:** ...

## Warnings (Should Fix)

### 1. [Kurztitel]
- **Location:** `src/services/recipe.service.ts:120`
- **Description:** N+1 Query beim Laden von Rezepten mit Tags
- **Suggested Fix:** `include: { tags: true }` in der Prisma Query
- **Impact:** Performance bei vielen Rezepten

## Suggestions (Nice to Have)

### 1. [Kurztitel]
- **Location:** `src/components/RecipeCard.tsx`
- **Description:** Komponente könnte in kleinere Stücke aufgeteilt werden
- **Suggested Fix:** Header, Body, Footer als Subkomponenten extrahieren

## Was gut läuft

- [Konkretes positives Feedback — kein Gefälligkeits-Lob]
- [Beispiel: "Konsequente Nutzung von Zod für Validierung"]
- [Beispiel: "Saubere Trennung Routes/Services/Types"]

## Top 5 Action Items

1. **[Critical]** Passwort-Logging entfernen (`src/routes/auth.ts:42`)
2. **[Critical]** Fehlende Auth auf DELETE /tasks/:id (`src/routes/tasks.ts:88`)
3. **[Warning]** N+1 Query in Recipe-Service beheben
4. **[Warning]** Tests für Auth-Flow ergänzen
5. **[Suggestion]** README mit Setup-Anleitung erweitern

## Test-Empfehlungen

[Welche Tests am dringendsten fehlen — nicht alle, nur die mit grösstem Hebel]

- Auth-Flow: Login, Logout, Token-Refresh
- Recipe-CRUD: Edge Cases mit fehlenden Feldern
- ...

## Deployment-Checkliste

Vor dem Deployment prüfen:

- [ ] Critical Issues behoben
- [ ] Build läuft fehlerfrei
- [ ] .env.example aktuell
- [ ] Keine Secrets in Git History
- [ ] Manuelle Tests des Happy Paths
- [ ] Backup-Strategie definiert
```

## Anwendungs-Hinweise

### Sektionen weglassen wenn leer

Wenn eine Severity-Stufe keine Findings hat, die Sektion komplett weglassen —
nicht "Keine Findings" schreiben. Das hält den Report übersichtlich.

Ausnahme: "Was gut läuft" sollte immer mindestens 1–2 Punkte enthalten, sonst
wirkt der Report demotivierend.

### Länge

- **Quick-Modus:** Maximal 1 Seite. Nur Zusammenfassung + Critical + Top 5.
- **Standard:** 2–3 Seiten. Alle Sektionen, aber jeweils kurz.
- **Deep:** Länge nach Bedarf. Kann auch 5+ Seiten werden.

### Reihenfolge der Findings

Innerhalb einer Severity nach Impact sortieren — das Wichtigste zuerst.
Nicht nach Datei oder Alphabet sortieren.

### Code-Snippets

Bei komplexen Findings ein kurzes Code-Snippet einbauen (max. 10 Zeilen):

```markdown
### Beispiel mit Snippet
- **Location:** `src/auth.ts:42`
- **Description:** Race Condition beim Token-Refresh
- **Code:**
  ```ts
  // Aktuell:
  const token = await getToken();
  if (isExpired(token)) {
    return refreshToken(); // Mehrere parallele Refreshes möglich
  }
  ```
- **Suggested Fix:** Mutex oder Promise-Cache für Refresh-Calls
```

### Action Items

Die "Top 5 Action Items" sind das Wichtigste im Report — das ist was der User
zuerst anschaut. Regeln:

- Maximal 5 Items (auch wenn mehr Findings existieren)
- Severity-Tag in eckigen Klammern voranstellen
- Konkret und actionable formulieren ("X tun" nicht "X verbessern")
- Location mitgeben für direktes Anspringen

Wenn der User mehr als 5 Items angehen will: auf die Detail-Sektionen verweisen.
