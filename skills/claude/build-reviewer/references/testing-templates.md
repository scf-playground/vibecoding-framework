# Testing-Templates

Vorlagen für projektspezifische Testing-Checklisten. Der Skill wählt die
passenden Abschnitte basierend auf Projekttyp und Tech-Stack.

## Grundstruktur

Jede Checkliste folgt dieser Struktur:

```markdown
# Testing-Checkliste — [Projektname]

Vor der Auslieferung alle Punkte manuell durchgehen.
Bei jedem Punkt: ✅ bestanden, ❌ Fehler (notieren), ⏭️ nicht anwendbar.

## 1. Startup & Grundfunktion
[Projekt startet, Grundvoraussetzungen erfüllt]

## 2-N. [Je ein Abschnitt pro MVP-Feature]
[Konkrete Tests für jedes Feature]

## N+1. Edge Cases
[Grenzfälle und Fehlerbedingungen]

## N+2. Build & Distribution
[Deployment-spezifische Tests]
```

## Abschnitt: Startup

### Für alle Projekttypen

```markdown
## 1. Startup & Grundfunktion
- [ ] App startet ohne Fehler
- [ ] Hauptansicht wird angezeigt
```

### Zusätzlich bei Datenbank-Projekten

```markdown
- [ ] DB-Verbindung wird hergestellt
- [ ] App mit falschem DB-Pfad → Fehlermeldung, kein Crash
- [ ] App ohne Konfigurationsdatei → Fehlermeldung, kein Crash
- [ ] Statusbar/Log zeigt Verbindungsstatus
```

### Zusätzlich bei Desktop-Apps (PyInstaller)

```markdown
- [ ] App ohne benötigten Treiber → Fehlermeldung mit Hinweis
- [ ] App mit gesperrter Datenbank → sinnvolle Meldung oder Normalbetrieb
```

### Zusätzlich bei Web-Apps

```markdown
- [ ] Frontend lädt im Browser
- [ ] Backend-API antwortet (Health-Check)
- [ ] CORS: Frontend kann Backend erreichen
```

## Abschnitt: MVP-Features

Für jedes Feature im MVP-Scope der CLAUDE.md einen eigenen Testabschnitt
generieren. Jeder Test muss konkret und prüfbar sein.

**Gut:** `Artikelnummer-Filter → Teilsuche funktioniert (z.B. "3RT" findet alle 3RT...)`
**Schlecht:** `Suche funktioniert`

### Muster für Suchfunktionen

```markdown
## X. Suche / Filter
- [ ] Ohne Filter → Ergebnisse geladen (Anzahl plausibel)
- [ ] Filter A → Ergebnisse korrekt eingeschränkt
- [ ] Filter B → Ergebnisse korrekt eingeschränkt
- [ ] Kombinierte Filter → AND-Logik funktioniert
- [ ] Leere Suche → alle Ergebnisse oder sinnvoller Default
- [ ] Suche mit Sonderzeichen → kein Crash
- [ ] Zurücksetzen → Filter leer, Ergebnisse neu geladen
```

### Muster für CRUD-Operationen

```markdown
## X. Erstellen
- [ ] Formular öffnet mit leeren Feldern
- [ ] Pflichtfelder validiert (leeres Feld → Fehlermeldung)
- [ ] Speichern mit gültigen Daten → Eintrag in DB
- [ ] Nach Speichern: Eintrag in Liste/Ansicht sichtbar
- [ ] Abbrechen → kein Eintrag in DB

## X+1. Bearbeiten
- [ ] Formular öffnet mit vorausgefüllten Feldern
- [ ] Änderungen speichern → DB aktualisiert
- [ ] Nach Speichern: geänderte Daten in Ansicht sichtbar
- [ ] Abbrechen → keine Änderung in DB
```

### Muster für Detailansicht

```markdown
## X. Detailansicht
- [ ] Alle Felder korrekt angezeigt
- [ ] Verknüpfte Daten geladen (z.B. Hersteller-Name statt ID)
- [ ] Optionale Sektionen nur sichtbar wenn Daten vorhanden
- [ ] Navigation zurück → vorherige Ansicht intakt
```

### Muster für Auth / Berechtigungen

```markdown
## X. Berechtigungen
- [ ] Im Lesemodus: Schreibfunktionen deaktiviert
- [ ] Im Schreibmodus: alle Funktionen aktiv
- [ ] [Bei Rollen:] Rolle X sieht nur das erlaubte
- [ ] [Bei Login:] Falsches Passwort → Fehlermeldung
- [ ] [Bei Login:] Token abgelaufen → Redirect zu Login
```

## Abschnitt: Edge Cases

Immer projektspezifisch. Typische Muster:

### Daten-Edge-Cases

```markdown
## Edge Cases: Daten
- [ ] Eintrag mit vielen NULL-Werten → kein Crash
- [ ] Sonderzeichen in Textfeldern (Umlaute, Schrägstriche, Anführungszeichen)
- [ ] Sehr langer Text in Freitextfeldern → kein UI-Overflow
- [ ] Numerische Felder mit 0 oder negativen Werten
- [ ] Leere Datenbank / keine Ergebnisse → sinnvolle Anzeige
```

### UI-Edge-Cases

```markdown
## Edge Cases: UI
- [ ] Fenster/Browser auf Minimalgrösse → Layout bricht nicht
- [ ] Schnelles Doppelklicken auf Aktions-Buttons → kein Crash
- [ ] Sehr lange Werte in Dropdowns/Listen → lesbar
- [ ] Parallele Nutzung (2 Fenster/Tabs) → kein Konflikt
```

### Netzwerk-Edge-Cases (bei DB auf Netzlaufwerk)

```markdown
## Edge Cases: Netzwerk
- [ ] Netzlaufwerk kurz unterbrochen → Fehlermeldung, kein Crash
- [ ] DB-Datei von anderem User gesperrt → sinnvolle Meldung
```

## Abschnitt: Build & Distribution

### PyInstaller

```markdown
## Build & Distribution
- [ ] Build-Script läuft ohne Fehler
- [ ] Output in dist/ vorhanden
- [ ] .exe startet auf Rechner OHNE Python-Installation
- [ ] Konfigurationsdatei neben .exe wird gelesen
- [ ] Zugriff auf Netzressourcen funktioniert (DB, Dateien)
```

### Docker

```markdown
## Build & Distribution
- [ ] docker compose up startet ohne Fehler
- [ ] Container ist erreichbar (Port-Mapping korrekt)
- [ ] Daten überleben Container-Neustart (Volumes)
- [ ] Umgebungsvariablen korrekt geladen
- [ ] Container-Logs zeigen keine Fehler
```

### Standard (kein spezielles Packaging)

```markdown
## Build & Distribution
- [ ] README: Setup-Anleitung vollständig und korrekt
- [ ] Alle Dependencies in requirements.txt / package.json
- [ ] .env.example / config.ini.example vorhanden mit Kommentaren
- [ ] .gitignore: keine Secrets, keine Build-Artefakte im Repo
```
