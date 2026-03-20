# Prompt-Sequenz

Logik für die Aufteilung komplexer Projekte in eine geordnete Prompt-Folge.
Verhindert das "Alles-auf-einmal-Syndrom" — das häufigste Anti-Pattern bei
neuen Projekten.

## Warum Sequenzen?

AI-Coding-Tools haben ein begrenztes Kontext-Budget. Ein einzelner Prompt der
20+ Dateien anfordert, produziert:
- Abgeschnittene oder oberflächliche Outputs bei späteren Dateien
- Inkonsistente Imports zwischen Modulen
- Vergessenes Error-Handling in hinteren Teilen
- Fehlende Validierung bei den letzten Endpoints

**Faustregel:** Wenn der erwartete Output mehr als ~800 Zeilen Code umfasst,
braucht es eine Sequenz statt eines Einzelprompts.

## Komplexitäts-Bewertung

Nach Phase 3 (Architektur steht) wird die Projektkomplexität eingeschätzt.
Das Ergebnis bestimmt ob ein Einzelprompt oder eine Sequenz generiert wird.

### Scoring-Heuristik

| Faktor | 0 Punkte | 1 Punkt | 2 Punkte |
|---|---|---|---|
| **Schichten** | Nur Backend ODER Frontend | Backend + Frontend | Backend + Frontend + Auth |
| **Endpoints/Pages** | 1-3 | 4-8 | 9+ |
| **Datenmodell** | 1-2 Entitäten | 3-5 Entitäten | 6+ oder Legacy-DB |
| **Integrationen** | Keine | 1 lokale (ODBC, lokale Legacy-DB, Dateisystem) | 1+ externe API ODER 2+ lokale |
| **Deployment** | Standard (npm start, python main.py) | Docker oder einfaches Packaging | Portabel (.exe, embedded Server, ODBC-Abhängigkeit) |

#### Hinweise zum Scoring

**Integrationen differenzieren:**
- Eine lokale Legacy-DB via ODBC/JDBC ist aufwändiger als "keine Integration"
  (Treiber, SQL-Dialekt, Record-Locking), aber weniger komplex als externe APIs
  (Netzwerk, Auth-Tokens, Rate-Limits). Darum: lokale Integration = 1, externe = 2.
- Zwei lokale Integrationen (z.B. ODBC + Dateisystem-Watcher) addieren sich
  ebenfalls auf 2 Punkte.

**Deployment differenzieren:**
- "Standard" = der übliche Weg für den Stack (npm start, python main.py, Docker).
  Docker allein ist keine Sonderkomplexität wenn der Stack das als Standard nutzt.
- "Portabel" = alles was über den Standard hinausgeht: PyInstaller, Electron,
  embedded Server, ODBC-Treiber-Abhängigkeit, Offline-Fähigkeit.

### Ergebnis

| Summe | Grösse | Prompt-Strategie |
|---|---|---|
| 0-3 | **S** (Small) | Einzelprompt — alles in einem |
| 4-6 | **M** (Medium) | 2-3 Prompts in Sequenz |
| 7-8 | **L** (Large) | 4-5 Prompts in Sequenz |
| 9-10 | **XL** (Extra Large) | 5-6 Prompts in Sequenz |

**Grenzfälle:** Wenn die Summe genau auf einer Grenze liegt (3, 6, 8),
den Kontext betrachten: Ist das Projekt eher komplex oder einfach für seine
Kategorie? Im Zweifel eine Stufe höher wählen — ein Prompt zu viel ist
weniger schlimm als ein Mega-Prompt der halbfertige Ergebnisse liefert.

### Beispiel: Artikel-Manager (Desktop-App mit Legacy-DB)

- Schichten: UI + Service + DB (kein separates Backend, kein Auth-Provider) = **1**
- Endpoints/Pages: Suchmaske, Detailansicht, Erfassungsdialog = **1**
- Datenmodell: Legacy Access DB, 88 Spalten, 18 Tabellen, keine FK-Constraints = **2**
- Integrationen: ODBC zu lokaler Access-DB = **1**
- Deployment: PyInstaller portabel mit ODBC-Abhängigkeit = **2**
- **Summe: 7 → L → 4-5 Prompts**

Alternativ bei bewusster Vereinfachung (weniger Views, schlankes Deployment)
kann M (3 Prompts) passend sein — der User entscheidet nach Präsentation.

### Beispiel: Rezept-Manager (Web-App)

- Schichten: Backend + Frontend + Auth = **2**
- Endpoints/Pages: 5 Endpoints, 4 Pages = **1**
- Datenmodell: 5 Entitäten, neues Schema = **1**
- Integrationen: Keine = **0**
- Deployment: Docker = **1**
- **Summe: 5 → M → 2-3 Prompts**

### Beispiel: Backup-Monitor (Script)

- Schichten: Nur Backend (Script) = **0**
- Endpoints: Kein API = **0**
- Datenmodell: YAML Config = **0**
- Integrationen: Notification-API (extern) = **2**
- Deployment: Cron-Job = **0**
- **Summe: 2 → S → Einzelprompt**

### Beispiel: Task-API (Backend mit Auth)

- Schichten: Nur Backend + Auth = **1**
- Endpoints: 10+ Endpoints = **2**
- Datenmodell: 3 Entitäten (User, Project, Task) = **1**
- Integrationen: Keine = **0**
- Deployment: Docker = **1**
- **Summe: 5 → M → 2-3 Prompts**

## Sequenz-Prinzipien

1. **Jeder Prompt ist eigenständig lauffähig** — nach jedem Schritt muss der
   Code starten (auch wenn noch nicht alles implementiert ist)
2. **Abhängigkeiten zuerst** — Config, DB-Verbindung, Auth vor Features
3. **Backend vor Frontend** — API muss stehen bevor die UI gebaut wird
4. **Ein Prompt = ein logisches Modul** — nicht nach Dateien aufteilen,
   sondern nach Funktionsbereichen
5. **Jeder Prompt referenziert CLAUDE.md** — im PREPARATION-Block
6. **Prüfpunkt nach jedem Prompt** — User testet und committet bevor weiter

## Einzelprompt-Grössenlimit

Das Komplexitäts-Gate verhindert zu grosse Gesamtprojekte in einem Prompt.
Aber auch innerhalb einer Sequenz kann ein einzelner Prompt zu gross werden.

**Selbstcheck für jeden Prompt in der Sequenz:**

Zähle die Aufgabenblöcke im TASK-Abschnitt. Ein Aufgabenblock ist eine
eigenständige Einheit wie "Edit-Dialog bauen", "Query-Funktionen schreiben",
"Build-Script erstellen".

| Aufgabenblöcke | Bewertung | Aktion |
|---|---|---|
| 1-3 | ✅ Gut | Prompt passt |
| 4-5 | ⚠️ Grenzwertig | Prüfen ob Blöcke eng zusammenhängen |
| 6+ | ❌ Zu gross | Prompt aufteilen |

**Wenn ein Prompt zu gross ist:**
Die logische Trennlinie liegt meistens zwischen "erstellen" und "verkabeln":
- Prompt N: Neue Komponenten bauen (Dialog, Service, Queries)
- Prompt N+1: Bestehende UI anpassen + Integration + Build/Doku

**Beispiel — zu grosser CRUD-Prompt:**
```
❌ Ein Prompt mit 7 Blöcken:
1. Edit-Dialog (16 Felder, Validation, Duplicate-Check)
2. Write-Queries (INSERT, UPDATE, Transaction-Handling)
3. Article-Service (5 Methoden)
4. Auth-Service
5. UI-Verkabelung (3 Views anpassen)
6. PyInstaller Build
7. README Update

✅ Aufgeteilt in 2 Prompts:
Prompt 3: Edit-Dialog + Write-Queries + Services
  → Neue Dateien erstellen, eigenständig testbar
  → Prüfpunkt: Dialog öffnet, Speichern schreibt in DB

Prompt 4: UI-Integration + Build + Doku
  → Buttons verkabeln, Modi durchsetzen, Build-Script, README
  → Prüfpunkt: App komplett nutzbar, Build erstellt
```

## Kontext-Brücken

Ab Prompt 2 muss der PREPARATION-Block beschreiben was die vorherigen
Prompts bereits erstellt haben. Der AI-Agent hat keinen Kontext aus dem
vorherigen Chat.

### Regeln für Kontext-Brücken

1. **Konkrete Dateipfade auflisten** — nicht nur beschreiben was existiert,
   sondern welche Dateien. Der Agent muss wissen was er lesen kann.
2. **Wichtige Klassen und Funktionen benennen** — wenn der neue Prompt
   bestehende Funktionen aufruft, diese explizit nennen.
3. **Explizit sagen was NICHT angefasst werden soll** — "Do NOT recreate
   or restructure what already exists — extend it."
4. **Kurz halten** — 10-20 Zeilen maximal. Kein Nachbau der CLAUDE.md.

### Vorlage

```markdown
## PREPARATION

Read `CLAUDE.md` for full project context.

The previous prompt(s) have created:

**Dateien:**
- `src/config/settings.py` — Config loader (db_path, app_mode)
- `src/db/connection.py` — pyodbc connection manager
- `src/db/models.py` — Dataclasses: Article, Manufacturer, ArticleLexicon, ArticleAttribute
- `src/db/queries.py` — Query functions (see below)
- `src/main.py` — QApplication entry point

**Verfügbare Funktionen in `queries.py`:**
- `get_articles_by_filter(conn, ...) -> list[Article]`
- `get_article_by_number(conn, art_number) -> Article | None`
- `get_all_manufacturers(conn) -> list[Manufacturer]`
- `get_lexicon_for_article(conn, art_number) -> list[ArticleLexicon]`
- `article_number_exists(conn, art_number) -> bool`

**Verfügbare UI-Komponenten (aus Prompt 2):**
- `src/ui/main_window.py` — MainWindow mit QStackedWidget, Toolbar, Status-Bar
- `src/ui/search_view.py` — SearchView mit Filter-Form und QTableView
- `src/ui/detail_view.py` — DetailView mit GroupBox-Sections
- `src/ui/widgets/article_table_model.py` — QAbstractTableModel
- `src/ui/widgets/labeled_field.py` — Label+Value Widget

Your task builds on this existing code. Do NOT recreate or restructure
what already exists — extend it.
```

### Schlecht vs. Gut

**❌ Zu vage (Agent weiss nicht was existiert):**
```
The previous prompt has created the project foundation:
- Project structure with all directories
- Config loader and database connection
- Data models and query functions
- Minimal app entry point
```

**❌ Zu lang (wiederholt die CLAUDE.md):**
```
[40 Zeilen die den kompletten Tech-Stack, alle Dataclass-Felder,
alle SQL-Queries und die Config-Optionen wiederholen]
```

**✅ Richtig (konkret, knapp, mit Dateipfaden und Funktionsnamen):**
```
The previous prompt has created:

Files:
- `src/db/connection.py` — get_connection() context manager
- `src/db/models.py` — Article, Manufacturer dataclasses
- `src/db/queries.py` — get_articles_by_filter(), get_all_manufacturers(),
  get_lexicon_for_article(), article_number_exists()
- `src/config/settings.py` — loads config.ini (db_path, app_mode)
- `src/main.py` — QApplication with MainWindow

Your task builds on this existing code.
```

## Sequenz-Vorlagen nach Projekttyp

Diese Vorlagen sind Ausgangspunkte. Je nach Projekt anpassen.
Detaillierte Vorlagen mit Beispiel-Prompts liegen in `projekttypen/`.

### Web-App (Frontend + Backend + Auth)

**Grösse M (4-6 Endpoints, einfaches Datenmodell):**
```
Prompt 1: Backend-Gerüst + Auth
  → Config, DB-Verbindung, Auth-Routen, JWT-Middleware
  → Prüfpunkt: Login funktioniert, Token wird zurückgegeben

Prompt 2: API-Endpoints
  → CRUD für Haupt-Entitäten, Pagination, Filter
  → Prüfpunkt: Endpoints via curl/Postman testbar

Prompt 3: Frontend komplett
  → Vite-Setup, Routing, Auth-Context, alle Pages
  → Prüfpunkt: App startet, Login funktioniert, Daten werden angezeigt
```

**Grösse L (viele Endpoints, komplexes Datenmodell, Spezialitäten):**
```
Prompt 1: Backend-Gerüst + Config + DB-Verbindung
  → Projektstruktur, Settings, DB-Connection-Factory, Health-Check
  → Prüfpunkt: Server startet, DB-Verbindung steht

Prompt 2: Auth-System
  → User-Modell, Login/Refresh/Logout, JWT-Middleware, Rollen
  → Prüfpunkt: Auth-Flow komplett testbar

Prompt 3: Kern-API (Haupt-Entitäten)
  → CRUD, Suche, Filter, Pagination für die wichtigsten Entitäten
  → Prüfpunkt: Alle Kern-Endpoints funktionieren

Prompt 4: Frontend-Grundstruktur
  → Vite-Setup, Routing, Auth-Context, API-Client, Layout, Login-Page
  → Prüfpunkt: App startet, Login funktioniert

Prompt 5: Frontend-Pages
  → Listen-, Detail-, Formular-Seiten, Admin-Bereich
  → Prüfpunkt: Alle MVP-Features nutzbar
```

### Desktop-App (PySide6 / PyQt)

**Grösse M (3 Views, einfaches Datenmodell):**
```
Prompt 1: Gerüst + DB-Layer + Datenmodell
  → Projektstruktur, Config, DB-Verbindung, Dataclasses, Queries
  → Prüfpunkt: App startet, DB verbunden, Queries testbar

Prompt 2: UI (Read-Only)
  → Hauptfenster, Suche, Ergebnistabelle, Detailansicht
  → Prüfpunkt: Suche und Navigation funktionieren

Prompt 3: Schreiboperationen + Build
  → Erfassungsdialog, Validierung, CRUD-Queries, PyInstaller
  → Prüfpunkt: Erstellen/Bearbeiten funktioniert, Build läuft
```

**Grösse L (3+ Views, Legacy-DB, komplexes Formular):**
```
Prompt 1: Gerüst + DB-Layer + Datenmodell
  → Projektstruktur, Config, DB-Verbindung, Dataclasses, Basis-Queries
  → Prüfpunkt: App startet, DB verbunden

Prompt 2: UI (Read-Only)
  → Hauptfenster, Suche mit Filtern, Ergebnistabelle, Detailansicht
  → Prüfpunkt: Suche und Detail-Navigation funktionieren

Prompt 3: Erfassungsdialog + Write-Queries + Services
  → Formular, Validation, Duplicate-Check, INSERT/UPDATE, Service-Layer
  → Prüfpunkt: Dialog öffnet, Speichern schreibt in DB

Prompt 4: UI-Integration + Modi + Build + Doku
  → Buttons verkabeln, Lese-/Schreibmodus, PyInstaller, README
  → Prüfpunkt: App komplett nutzbar, Build erstellt
```

### API / Backend-Service

**Grösse M:**
```
Prompt 1: Gerüst + Auth + Datenmodell
  → Config, DB, Auth-Routen, Schema-Migration
  → Prüfpunkt: Auth funktioniert, DB-Schema steht

Prompt 2: Alle Endpoints
  → CRUD, Filter, Pagination, Geschäftslogik
  → Prüfpunkt: Alle Endpoints via curl testbar
```

**Grösse L:**
```
Prompt 1: Gerüst + Config + DB
Prompt 2: Auth-System
Prompt 3: Kern-Endpoints (Haupt-Entitäten)
Prompt 4: Sekundäre Endpoints + Geschäftslogik
Prompt 5: Dokumentation + Build
```

### Homelab / Self-Hosted

**Meistens Grösse S oder M:**
```
Prompt 1 (S): Alles in einem
  → Docker Compose, App-Code, Config

Prompt 1 (M): Backend + Config
Prompt 2 (M): Frontend / Web-UI
Prompt 3 (M): Docker + Reverse Proxy
```

### Automation / Script

**Fast immer Grösse S:**
```
Prompt 1: Komplettes Script + Config + README
```

## Prompt-Sequenz generieren

Wenn die Komplexität M oder grösser ergibt, generiert der `architect-prompter`
nicht einen Prompt, sondern eine nummerierte Sequenz.

### Format pro Sequenz-Prompt

```markdown
# Prompt [N] von [Total]: [Titel]

## PREPARATION
Read CLAUDE.md for full project context.
[Wenn N > 1: Kontext-Brücke mit Dateipfaden und Funktionsnamen]

## TASK
[Fokussierte Aufgabe — maximal 3-4 Aufgabenblöcke, siehe Grössenlimit]

## CONSTRAINTS
[Step-spezifische Einschränkungen]
["Do NOT implement X — that comes in Prompt N+1"]

## CHECKPOINT
After completing this step, verify:
- [ ] [Konkreter Test 1]
- [ ] [Konkreter Test 2]
Commit with message: "feat: [beschreibung]"
```

## Dem User präsentieren

Die Sequenz wird als Übersicht präsentiert (nicht als komplette Prompts).
Der User sieht:

1. Komplexitäts-Einschätzung (S/M/L/XL) mit Scoring-Tabelle
2. Sequenz-Übersicht (nummerierte Schritte mit Titel und Prüfpunkt)
3. Erster Prompt als kopierbarer Block

Nachfolgende Prompts werden erst generiert wenn der User danach fragt.
So bleibt der Kontext aktuell — der User kann zwischen den Schritten
Änderungen beschreiben die in den nächsten Prompt einfliessen.

Via `ask_user_input`:
```
Frage: "Projekt-Komplexität ist [L]. Ich empfehle [5] Prompts in Folge."
Optionen:
- Passt — zeig mir Prompt 1
- Weniger Schritte — fasse zusammen
- Mehr Schritte — feiner aufteilen
```
