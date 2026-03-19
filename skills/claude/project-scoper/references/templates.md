# Templates

Vorlagen für die Outputs von Phase 1 und Phase 2.

## Problem-Statement Template

```
### Problem-Statement: [Projektname]

**Problem:** [1-2 Sätze die beschreiben WAS das Problem ist. Nicht die Lösung.]

**Zielnutzer:** [Wer hat dieses Problem? Konkret benennen.]

**Erfolgskriterium:** [Woran erkennt man, dass das Problem gelöst ist? Messbar.]
```

### Beispiele

**Gut — Homelab-Projekt:**
```
Problem-Statement: Homelab Dashboard

Problem: Ich verliere den Überblick über meine Homelab-Services — welche
Container laufen, welche Ports belegt sind, wann das letzte Backup war.
Jedes Mal muss ich per SSH auf verschiedene Hosts und Logs prüfen.

Zielnutzer: Ich selbst als Homelab-Admin.

Erfolgskriterium: Ein Dashboard zeigt mir auf einen Blick den Status
aller Services, ohne dass ich mich per SSH einloggen muss.
```

**Gut — Web-App:**
```
Problem-Statement: Rezept-Manager

Problem: Meine Rezepte sind über Notion, Screenshots und Bookmarks verstreut.
Ich finde sie nicht wenn ich sie brauche, und die Mengenangaben passen nie
für meine Haushaltsgrösse.

Zielnutzer: Ich und meine Partnerin.

Erfolgskriterium: Alle Rezepte an einem Ort, durchsuchbar, mit
automatischer Portionen-Anpassung.
```

**Gut — Automation:**
```
Problem-Statement: Backup-Monitor

Problem: Ich weiss nicht ob meine Backups tatsächlich durchlaufen.
Erst wenn ich Daten brauche, merke ich dass seit Wochen nichts gesichert wurde.

Zielnutzer: Ich als Homelab-Admin.

Erfolgskriterium: Tägliche Benachrichtigung wenn ein Backup fehlschlägt
oder länger als 24h nicht gelaufen ist.
```

**Schlecht:**
```
Problem: Ich will eine React App mit Tailwind und Supabase bauen.
```
→ Das ist eine Lösung, kein Problem. Rückfrage: "Was soll die App lösen?"

**Schlecht:**
```
Problem: Ich brauche ein besseres System.
```
→ Zu vage. Rückfrage: "Was genau funktioniert am aktuellen System nicht?"

## Requirements Template

```
### Requirements: [Projektname]

**Problem-Statement:** [Aus Phase 1 übernehmen]

**Projekttyp:** [Web-App / API / Homelab / Automation]

#### MVP (V1)
- [ ] Feature 1: [Kurze Beschreibung]
- [ ] Feature 2: [Kurze Beschreibung]
- [ ] Feature 3: [Kurze Beschreibung]

#### Später (V2+)
- Feature A: [Beschreibung]
- Feature B: [Beschreibung]

#### Constraints
- **Plattform:** [Web / Mobile / CLI / Docker / etc.]
- **Zeitrahmen:** [Grobe Schätzung oder Deadline]
- **Auth:** [Ja/Nein, welche Art]
- **Zugriff:** [Öffentlich / Intern / Beides]

#### Abhängigkeiten
- [Service/API]: [Wofür benötigt]

#### Nicht-funktionale Anforderungen
- [Performance, Sicherheit, Barrierefreiheit — nur wenn relevant]
```

### Beispiel: Komplett ausgefüllt

```
### Requirements: Homelab Dashboard

**Problem-Statement:** Ich verliere den Überblick über meine Homelab-Services.
Jedes Mal muss ich per SSH auf verschiedene Hosts und Logs prüfen.

**Projekttyp:** Web-App (Self-hosted)

#### MVP (V1)
- [ ] Container-Status: Alle Docker Container mit Status anzeigen
- [ ] Port-Übersicht: Welcher Service auf welchem Port
- [ ] Uptime: Wie lange läuft jeder Service
- [ ] Manueller Refresh via Button

#### Später (V2+)
- Auto-Refresh alle 30 Sekunden
- Backup-Status pro Service anzeigen
- Benachrichtigungen bei Ausfällen
- Ressourcen-Monitoring (CPU, RAM, Disk)

#### Constraints
- Plattform: Docker Container im Homelab (LXC auf Proxmox)
- Zeitrahmen: Wochenendprojekt
- Auth: Nein (nur intern erreichbar)
- Zugriff: Nur im lokalen Netz

#### Abhängigkeiten
- Docker API: Container-Status auslesen
- Proxmox API: Host-Informationen (optional für V2)

#### Nicht-funktionale Anforderungen
- Muss auf Mobile-Bildschirm lesbar sein
- Darf maximal 256MB RAM verbrauchen
```

## User Story Template (optional)

Für komplexere Projekte können Features als User Stories formuliert werden.
Nicht erzwingen — nur nutzen wenn der Scope es erfordert.

```
Als [Rolle] möchte ich [Aktion], damit [Nutzen].

Akzeptanzkriterien:
- [ ] [Konkretes, prüfbares Kriterium]
- [ ] [Weiteres Kriterium]
```

Beispiel:
```
Als Homelab-Admin möchte ich auf einen Blick sehen welche Container
laufen und welche gestoppt sind, damit ich Ausfälle sofort erkenne.

Akzeptanzkriterien:
- [ ] Alle Docker Container werden mit Name und Status angezeigt
- [ ] Laufende Container grün, gestoppte rot markiert
- [ ] Status wird beim Laden der Seite aktualisiert
```
