# Projekttyp: Web-App

Patterns, Prompt-Sequenzen und Besonderheiten für Web-Apps
(Frontend + Backend, optional mit Auth).

## Typische Architektur

```
web-app/
├── backend/          # API-Server
│   ├── routes/       # Endpoint-Definitionen
│   ├── services/     # Business-Logik
│   ├── models/       # Datenmodelle / Schemas
│   ├── middleware/   # Auth, Validation
│   └── database/     # DB-Verbindung, Migrations
├── frontend/         # SPA oder SSR
│   ├── pages/        # Seiten / Views
│   ├── components/   # Wiederverwendbare UI-Teile
│   ├── services/     # API-Client
│   ├── hooks/        # Custom Hooks (React)
│   ├── context/      # State Management
│   └── types/        # TypeScript Interfaces
├── CLAUDE.md
├── .cursorrules
└── README.md
```

## Prompt-Sequenzen

### Grösse M — Einfache Web-App (4-6 Endpoints, 3-4 Pages)

Beispiel: Rezept-Manager, Notiz-App, Bookmark-Verwaltung

```
Prompt 1: Backend-Gerüst + Auth + Datenmodell
  Scope: Projektstruktur, Config, DB-Schema, Auth-Routen, JWT-Middleware
  Prüfpunkt: Server startet, Login gibt Token zurück
  Commit: "feat: backend scaffolding with auth"

Prompt 2: API-Endpoints
  Scope: CRUD für alle Entitäten, Suche, Filter, Pagination
  Prüfpunkt: Alle Endpoints via curl testbar
  Commit: "feat: article/recipe/note CRUD endpoints"

Prompt 3: Frontend komplett
  Scope: Vite-Setup, Routing, Auth-Context, API-Client, alle Pages
  Prüfpunkt: App startet, Login funktioniert, Daten werden angezeigt
  Commit: "feat: frontend with all MVP pages"
```

### Grösse L — Komplexe Web-App (8+ Endpoints, 5+ Pages, Auth mit Rollen)

Beispiel: Artikel-Manager mit Legacy-DB, Dashboard mit Admin-Bereich

```
Prompt 1: Backend-Gerüst + Config + DB-Verbindung
  Scope: Projektstruktur, Settings/Config, DB-Connection-Factory, Health-Check
  Besonders bei Legacy-DB: Connection-String, Treiber, Encoding testen
  Prüfpunkt: Server startet, DB-Verbindung bestätigt
  Commit: "feat: backend scaffolding with DB connection"

Prompt 2: Auth-System
  Scope: User-Modell (separate DB wenn nötig), Registration/Login/Refresh,
         JWT-Middleware, Rollen-basierte Zugriffskontrolle
  Prüfpunkt: Login/Refresh/Logout funktioniert, Rollen werden geprüft
  Commit: "feat: auth system with role-based access"

Prompt 3: Kern-API
  Scope: CRUD für Haupt-Entitäten, Suche, Filter, Pagination,
         verwandte Daten (JOINs, Attribute, Übersetzungen)
  Prüfpunkt: Alle Kern-Endpoints funktionieren mit Auth
  Commit: "feat: core API endpoints"

Prompt 4: Frontend-Grundstruktur
  Scope: Vite-Setup, Tailwind, Routing, Auth-Context, API-Client,
         Layout-Komponente, Login-Page
  Prüfpunkt: App startet, Login funktioniert, geschützte Routen aktiv
  Commit: "feat: frontend foundation with auth"

Prompt 5: Frontend-Pages
  Scope: Listen-Seite (Tabelle, Suche, Filter), Detail-Seite,
         Formular (Create/Edit), Admin-Bereich (User-Verwaltung)
  Prüfpunkt: Alle MVP-Features über die UI nutzbar
  Commit: "feat: all MVP pages"

Prompt 6 (optional): Build + Deployment
  Scope: Build-Script, Docker/PyInstaller/.exe, README, .env.example
  Prüfpunkt: Deploybares Artefakt erstellt
  Commit: "chore: build configuration and docs"
```

## Besonderheiten

### Legacy-DB-Integration

Wenn eine bestehende Datenbank angebunden wird die nicht geändert werden darf:

- **CLAUDE.md:** Nur MVP-relevante Tabellen im Hauptteil dokumentieren.
  Sekundäre Tabellen als kurze Liste ohne Spaltendetails.
- **Prompt 1 isoliert die DB-Verbindung** — das ist die riskanteste Stelle.
  Erst wenn die Verbindung steht, mit Auth und Features weitermachen.
- **Kein ORM für Legacy-DBs** — Raw SQL mit parametrisierten Queries.
  ORM-Mappings auf bestehende Schemas sind fragil und fehleranfällig.
- **SQL-Dialekt dokumentieren** — Access-SQL, T-SQL, PL/SQL haben
  unterschiedliche Syntax für Pagination, Wildcards, Funktionen.
  Explizit in CLAUDE.md Rules aufnehmen.

### Dual-DB-Pattern

Wenn die App zwei Datenbanken braucht (z.B. Legacy-DB für Daten +
SQLite für User/Sessions):

- Separate Connection-Factories in `database/`
- Klar dokumentieren welche DB für was zuständig ist
- In Prompt 1 beide Verbindungen aufsetzen und testen

### Portable Deployment (.exe, kein Server)

Wenn die App als standalone-Executable ohne Installation laufen soll:

- Frontend wird als statische Dateien ins Backend eingebettet
- Build-Script als eigener Prompt (nicht im Haupt-Prompt)
- Alle Pfade relativ oder via Environment Variables
- Keine externen CDN-Ressourcen im Frontend

## CLAUDE.md Längen-Guidance

| Abschnitt | Einfache App | Komplexe App mit Legacy-DB |
|---|---|---|
| Goal | 2-3 Sätze | 3-4 Sätze |
| Tech Stack | 5-8 Zeilen | 8-12 Zeilen |
| Project Structure | 15-25 Zeilen | 25-40 Zeilen |
| Data Model | 10-20 Zeilen | 20-40 Zeilen (nur MVP-Tabellen!) |
| MVP Scope | 5-8 Items | 5-8 Items (gleich, nicht mehr) |
| Conventions | 15-25 Zeilen | 15-25 Zeilen |
| Rules | 8-12 Regeln | 12-18 Regeln (inkl. DB-spezifische) |
| **Total** | **~80-120 Zeilen** | **~120-180 Zeilen** |

**Harte Grenze:** Wenn die CLAUDE.md über 200 Zeilen geht, sekundäre
DB-Tabellen und optionale Abschnitte kürzen oder in ein separates
Referenz-Dokument auslagern.
