# Phasen-Referenz

Detaillierte Beschreibung aller 6 Phasen mit Templates und Beispielen.

## Phase 1: Problem-Statement Template

```
### Problem-Statement: [Projektname]

**Problem:** [1-2 Sätze die das Problem beschreiben, NICHT die Lösung]

**Zielnutzer:** [Wer hat dieses Problem?]

**Erfolgskriterium:** [Woran erkennt man dass das Problem gelöst ist?]
```

**Beispiel gut:**
```
Problem: Ich verliere den Überblick über meine Homelab-Services — welche
Container laufen, welche Ports belegt sind, wann das letzte Backup war.

Zielnutzer: Ich selbst als Homelab-Admin.

Erfolgskriterium: Ein Dashboard das mir auf einen Blick den Status aller
Services zeigt, ohne dass ich mich per SSH einloggen muss.
```

**Beispiel schlecht:**
```
Problem: Ich will ein Dashboard mit React und Tailwind bauen.
```
→ Das ist eine Lösung, kein Problem. Zurückfragen: "Was willst du damit lösen?"

## Phase 2: Requirements Template

```
### Requirements: [Projektname]

#### MVP (V1)
- [ ] Feature 1: [Beschreibung]
- [ ] Feature 2: [Beschreibung]
- [ ] Feature 3: [Beschreibung]

#### Später (V2+)
- Feature A: [Beschreibung]
- Feature B: [Beschreibung]

#### Constraints
- Plattform: [Web / Mobile / CLI / Self-hosted]
- Zeitrahmen: [Grobe Schätzung]
- Abhängigkeiten: [Externe APIs, Services, etc.]
- Budget: [Falls relevant]
```

## Phase 3: Architektur-Entscheidung Template

```
### Architektur: [Projektname]

#### Tech-Stack
- Frontend: [Framework + Sprache]
- Backend: [Framework + Sprache]
- Datenbank: [Typ + konkretes Produkt]
- Auth: [Strategie]
- Hosting/Deploy: [Ziel]

#### Begründung
[Warum dieser Stack für dieses Projekt?]

#### Ordnerstruktur
[Baumdarstellung der geplanten Struktur]

#### Datenmodell
[Wichtigste Entitäten und Beziehungen]
```

## Phase 4: CLAUDE.md Struktur

Die CLAUDE.md ist die zentrale Kontextdatei die Cursor AI und Claude Code
bei jedem Prompt lesen. Sie muss alles enthalten was der Agent braucht um
im Projektkontext zu arbeiten.

```markdown
# [Projektname]

## Projektziel
[Problem-Statement aus Phase 1]

## Tech-Stack
[Aus Phase 3]

## Ordnerstruktur
[Aus Phase 3]

## Konventionen
- Sprache: [z.B. TypeScript strict mode]
- Formatting: [z.B. Prettier mit Standard-Config]
- Naming: [z.B. camelCase für Variablen, PascalCase für Komponenten]
- Kommentare: [z.B. technisch und knapp, WAS nicht WARUM]

## Referenzen
- Code Standards: [Pfad oder Link zum Repo]
- UI Design System: [Falls relevant]
- API Patterns: [Falls relevant]

## Wichtige Regeln
- [Projektspezifische Regeln]
- [Was der Agent NICHT tun soll]
```

## Phase 5: Umsetzungs-Tipps

### Reihenfolge für typische Web-Apps:
1. Projekt-Setup (Boilerplate, Dependencies, Config)
2. Datenmodell / DB-Schema
3. Backend-API (Endpoints, Auth)
4. Frontend-Grundstruktur (Routing, Layout)
5. Features einzeln implementieren
6. Styling und UX verfeinern

### Reihenfolge für Homelab-Services:
1. Docker Compose File
2. Volumes und Netzwerk-Config
3. Environment Variables
4. Reverse Proxy Setup
5. Backup-Strategie
6. Monitoring

### Prompt-Tipps während der Umsetzung:
- "Implementiere Feature X gemäss der CLAUDE.md Spezifikation"
- "Prüfe die aktuelle Implementierung gegen die Requirements in CLAUDE.md"
- Bei Bugs: Fehlermeldung + erwartetes Verhalten + tatsächliches Verhalten
- Nie: "Fix this", "Make it work", "Debug the code"

## Phase 6: Review-Checkliste

### Code-Qualität
- [ ] Keine auskommentierten Code-Blöcke
- [ ] Keine TODO-Kommentare ohne Issue-Referenz
- [ ] Error Handling vollständig
- [ ] Keine hardcodierten Secrets oder Credentials
- [ ] Typen/Interfaces vollständig (bei TypeScript)

### Funktionalität
- [ ] Alle MVP-Features funktionieren
- [ ] Happy Path getestet
- [ ] Error Cases getestet
- [ ] Responsive / verschiedene Bildschirmgrössen (bei Web)

### Dokumentation
- [ ] README mit Setup-Anleitung
- [ ] Environment Variables dokumentiert
- [ ] API-Endpoints dokumentiert (bei APIs)

### Deployment
- [ ] Build läuft fehlerfrei
- [ ] Environment Variables für Production gesetzt
- [ ] Git Repository sauber (kein .env, keine node_modules)
