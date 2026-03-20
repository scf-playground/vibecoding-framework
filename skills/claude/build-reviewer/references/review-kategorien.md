# Review-Kategorien

Bausteine für projektspezifische Review-Prompts. Nicht alle Kategorien sind
für jedes Projekt relevant — der Skill wählt basierend auf dem Tech-Stack
und der CLAUDE.md die passenden aus.

## Pflicht-Kategorien (immer prüfen)

### CLAUDE.md Compliance

Der wichtigste Check: Hält der Code die eigenen Regeln ein?

```
### CLAUDE.md Compliance
- Does the code follow all rules listed in the Rules section?
- Are naming conventions consistent (snake_case, PascalCase, etc.)?
- Are type hints present on all function signatures?
- Is the import order correct (stdlib → third-party → local)?
- Are comments technical and concise (WHAT, not WHY)?
- Are forbidden patterns avoided (as listed in Rules)?
```

Die konkreten Prüfpunkte werden aus den Rules der Projekt-CLAUDE.md extrahiert.
Jede "Do NOT" Regel wird zu einem Prüfpunkt.

### Error Handling

```
### Error Handling
- Are all external calls wrapped in try/except with specific exceptions?
- Is the logging module used (not print())?
- Are user-facing error messages helpful and non-technical?
- What happens when the main dependency is unavailable?
  (DB offline, API unreachable, file missing)
- Are there bare except: clauses? (should be specific exceptions)
- Can the app crash from unhandled exceptions in normal usage?
```

### Code-Qualität

```
### Code Quality
- Dead code: unused imports, commented-out blocks, unreachable code?
- Duplication: same logic in multiple places?
- Naming consistency: same concept, same name across all files?
- Are dataclasses/models used consistently, or are raw dicts mixed in?
- Are magic numbers/strings extracted to constants?
- Is the separation of concerns clean (UI vs. logic vs. data access)?
```

## Stack-spezifische Kategorien

### SQL / Datenbank

Relevant wenn: pyodbc, SQLAlchemy, Prisma, oder direkte SQL-Queries im Stack.

```
### SQL Correctness
- All queries use parameterized placeholders (? or :name), never f-strings?
- SQL dialect matches the database?
  - Access: LIKE with *, TOP n, IIf(), bracket notation for special chars
  - PostgreSQL: LIKE with %, LIMIT/OFFSET, CASE WHEN
  - SQLite: LIKE with %, LIMIT/OFFSET
- AutoNumber/SERIAL columns: NOT provided on INSERT?
- Transactions: autocommit=False + explicit commit/rollback for writes?
- Connection handling: connections closed/returned after use?
- NULL handling: are nullable columns handled in the code?
- What happens if a query returns no results?
```

### Auth / Security

Relevant wenn: JWT, Login, Rollen, Passwörter im Stack.

```
### Auth & Security
- Passwords hashed with bcrypt/argon2, never stored plain text?
- JWT tokens: reasonable expiry, refresh flow implemented?
- Role checks: enforced on every protected endpoint/action?
- Can a user access data belonging to another user?
- Are secrets (JWT_SECRET, DB credentials) in environment variables, not in code?
- CORS: properly configured for the deployment target?
```

### UI-Konsistenz

Relevant wenn: Frontend (React, PySide6, etc.) im Stack.

```
### UI Consistency
- All labels, buttons, and messages in the correct language?
- Read-only vs. write mode enforced everywhere?
- Buttons disabled when they should be (permissions, validation state)?
- Loading states for all async operations?
- Error states shown to user (not just logged)?
- Form validation: inline errors or at least on submit?
- Layout: does it work at minimum window size?
- Navigation: can the user always go back?
```

### API-Konsistenz

Relevant wenn: REST/GraphQL API im Stack.

```
### API Consistency
- All endpoints return consistent format: { data: T } or { error: string, code: string }?
- All list endpoints support pagination?
- HTTP status codes correct (201 for create, 404 for not found, etc.)?
- Input validation on all endpoints (before processing)?
- Are error responses informative but not leaking internals?
- CORS headers set correctly?
```

### Desktop-App

Relevant wenn: PySide6, PyQt, Electron, Tauri im Stack.

```
### Desktop App Specifics
- Does the app start without internet/network? (if applicable)
- File paths: relative or configurable, not hardcoded?
- Config file: read from correct location (next to exe, not cwd)?
- Window management: minimum size set, resize behavior correct?
- Does the app handle the main dependency being unavailable gracefully?
  (e.g., database file locked, network drive disconnected)
- System resources: does the app clean up connections on close?
- Portable: no registry entries, no AppData writes (if portable deployment)?
```

### Docker / Homelab

Relevant wenn: Docker Compose, Self-Hosted im Stack.

```
### Docker & Deployment
- Dockerfile: multi-stage build, minimal final image?
- docker-compose.yml: follows project conventions?
- Volumes: data persisted correctly, not lost on container restart?
- Environment variables: all documented, sensible defaults?
- Health check: endpoint or script defined?
- Reverse proxy: configured for the deployment target?
- Backup: data directory identified and documented?
```

## Integration zwischen Schichten

Immer prüfen wenn das Projekt mehrere Schichten hat:

```
### Cross-Layer Integration
- Do imports between modules resolve correctly?
- Is there a service layer between UI and data access?
  Or do views/routes call the database directly?
- Are caches implemented where specified (e.g., manufacturer list)?
- Is state preserved correctly across navigation (e.g., search results
  after viewing detail)?
- Are all files created by Prompt 1 still used by Prompt 2/3 code?
  (no orphaned modules from earlier prompts)
```

## Output-Format

Der Review-Prompt fordert immer dieses Ausgabeformat:

```
## OUTPUT FORMAT

1. **Summary** — Overall assessment in 3-5 sentences.
   Is this ready for its intended audience?

2. **Critical issues** — Must fix before giving this to users.
   Bugs, crashes, data corruption risks, security holes.

3. **Warnings** — Should fix soon.
   Missing error handling, edge cases, UX problems.

4. **Style issues** — Convention violations, naming inconsistencies,
   dead code. Not blocking but should be cleaned up.

5. **Top 5 action items** — Ordered by impact.
   Each with: file path, function/line, what to change, why.

Do NOT make any code changes. Analysis only.
```
