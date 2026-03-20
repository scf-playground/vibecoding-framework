# Projekttyp: API / Backend-Service

Patterns, Prompt-Sequenzen und Besonderheiten für reine Backend-APIs
die von einem separaten Frontend oder externen Clients konsumiert werden.

## Typische Architektur

```
api-service/
├── src/
│   ├── routes/          # Endpoint-Definitionen (thin handlers)
│   ├── services/        # Business-Logik
│   ├── models/          # Pydantic Schemas / TypeScript Interfaces
│   ├── middleware/       # Auth, Validation, Error Handler
│   ├── database/        # DB-Verbindung, Migrations
│   └── types/           # Shared Types (wenn TypeScript)
├── tests/               # Unit + Integration Tests
├── CLAUDE.md
├── .cursorrules
├── .env.example
└── README.md
```

### Schichten-Regel

APIs folgen einer strikten Dreischicht-Architektur:

```
Routes (thin) → Services (Logik) → Database (Zugriff)
```

- **Routes:** Validierung, Deserialisierung, Response-Formatting. Keine Logik.
- **Services:** Geschäftsregeln, Datenverarbeitung, Fehlerbehandlung.
- **Database:** Queries, Connection-Management, Transaktionen.

Route-Handler sollten nie direkt auf die DB zugreifen.

## Prompt-Sequenzen

### Grösse S — Einfache API (1-4 Endpoints, kein Auth)

Beispiel: Webhook-Empfänger, Status-API, interner Microservice

```
Prompt 1: Komplettes Projekt
  Scope: Setup, alle Endpoints, Datenmodell, README
  Prüfpunkt: Alle Endpoints via curl testbar
  Commit: "feat: complete API implementation"
```

### Grösse M — Standard-API (4-8 Endpoints, Auth)

Beispiel: Task-API, Notiz-Service, CRUD-Backend für eine App

```
Prompt 1: Gerüst + Auth + Datenmodell
  Scope: Projektstruktur, Config, DB-Setup, Auth (Login/Refresh/Logout),
         JWT-Middleware, Rollen-Check, DB-Schema/Migration
  Prüfpunkt: Auth-Flow komplett, DB-Schema steht
  Commit: "feat: project scaffolding with auth and data model"

Prompt 2: Alle Business-Endpoints
  Scope: CRUD, Suche/Filter, Pagination, Geschäftslogik,
         Error Handling, Response-Format konsistent
  Prüfpunkt: Alle Endpoints mit Auth getestet
  Commit: "feat: all business endpoints"
```

### Grösse L — Komplexe API (8+ Endpoints, mehrere Entitäten, komplexe Logik)

Beispiel: E-Commerce-Backend, Multi-Entity-API mit Beziehungen

```
Prompt 1: Gerüst + Config + DB
  Scope: Projektstruktur, Settings, DB-Verbindung, Health-Check,
         Error-Handler, Logging
  Prüfpunkt: Server startet, DB verbunden, Health-Check antwortet
  Commit: "feat: project scaffolding"

Prompt 2: Auth-System
  Scope: User-Modell, Login/Register/Refresh, JWT, Rollen, Middleware
  Prüfpunkt: Auth-Flow komplett testbar
  Commit: "feat: authentication system"

Prompt 3: Kern-Endpoints (Haupt-Entitäten)
  Scope: CRUD + Filter + Pagination für die wichtigsten 2-3 Entitäten
  Prüfpunkt: Kern-Endpoints mit Auth getestet
  Commit: "feat: core entity endpoints"

Prompt 4: Sekundäre Endpoints + Geschäftslogik
  Scope: Verbleibende Endpoints, komplexe Queries, Aggregationen,
         Beziehungen zwischen Entitäten
  Prüfpunkt: Alle Endpoints funktionieren
  Commit: "feat: secondary endpoints and business logic"

Prompt 5: Docs + Tests + Build
  Scope: OpenAPI/Swagger-Doku, README, .env.example, Docker, Tests
  Prüfpunkt: Doku generiert, Build läuft
  Commit: "chore: documentation, tests, and build config"
```

## Besonderheiten

### Konsistente Error Responses

Jede API braucht ein einheitliches Fehlerformat. In der CLAUDE.md und
im ersten Prompt festlegen:

```json
// Erfolg
{ "data": T }

// Fehler
{ "detail": "Human-readable message", "code": "MACHINE_READABLE_CODE" }
```

Das Format im Error-Handler des Gerüst-Prompts implementieren —
nicht in jedem Endpoint einzeln.

### Pagination-Pattern

Im Gerüst-Prompt ein wiederverwendbares Pagination-Utility erstellen:
- Query-Parameter: `page`, `page_size` (mit Defaults und Max-Werten)
- Response-Envelope: `{ data: T[], total: number, page: number, page_size: number }`
- Access-SQL: `TOP n` mit Offset-Berechnung (nicht `LIMIT/OFFSET`)
- Standard-SQL: `LIMIT n OFFSET m`

### Multi-Consumer APIs

Wenn mehrere Clients die API nutzen (Web, Mobile, Dritte):
- Versionierung von Anfang an: `/api/v1/...`
- CORS-Config im Gerüst-Prompt sauber aufsetzen
- Rate-Limiting einplanen (mindestens als Stub)

### Legacy-DB als Datenquelle

Gleiche Regeln wie bei Web-Apps (siehe `projekttypen/web-app/`):
- Kein ORM, Raw SQL
- SQL-Dialekt in Rules dokumentieren
- DB-Verbindung als isolierter erster Prompt

## CLAUDE.md Längen-Guidance

| Abschnitt | Einfache API | Komplexe API |
|---|---|---|
| Goal | 2-3 Sätze | 2-3 Sätze |
| Tech Stack | 5-8 Zeilen | 8-10 Zeilen |
| Project Structure | 10-15 Zeilen | 15-25 Zeilen |
| Data Model | 10-15 Zeilen | 15-30 Zeilen |
| API Endpoints | 10-15 Zeilen | 20-30 Zeilen |
| Conventions | 10-15 Zeilen | 15-20 Zeilen |
| Rules | 5-8 Regeln | 8-12 Regeln |
| **Total** | **~60-90 Zeilen** | **~100-150 Zeilen** |
