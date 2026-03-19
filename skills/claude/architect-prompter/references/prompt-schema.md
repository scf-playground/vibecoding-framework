# Prompt-Schema

Struktur für Prompts an AI-Coding-Tools (Cursor, Claude Code, Antigravity).
Alle Prompts werden auf Englisch generiert.

## Grundstruktur

Nicht jeder Abschnitt ist für jeden Prompt nötig — nur verwenden was relevant ist.

```
## PREPARATION
Anweisungen welche Dateien und Skills der Agent ZUERST lesen soll.

## ROLE (optional)
Rolle die dem Ergebnis hilft.

## CONTEXT (optional)
Informationen die NICHT in Dateien stehen — Business-Kontext, Beobachtungen.

## TASK
Klare Beschreibung: WAS, WELCHE Dateien, WIE das Ergebnis aussieht.

## CONSTRAINTS
Was NICHT getan werden soll. Bevorzugte Patterns. Kompatibilität.

## OUTPUT FORMAT
Erwartete Dateien, Code-Stil, Dokumentation.

## STEPS (optional)
Nummerierte Schritte für komplexe Aufgaben.
```

## Regeln für gute Prompts

1. **Spezifisch** — Keine vagen Wörter wie "improve", "make better", "clean up"
2. **Aktionsorientiert** — Beginnt mit klarem Verb: Implement, Create, Refactor, Fix
3. **Abgegrenzt** — Klar definiert was IN scope und OUT of scope ist
4. **Messbar** — Das Ergebnis ist prüfbar
5. **Kontexteffizient** — Dateien per Verweis referenzieren, nicht inline kopieren
6. **Englisch** — Immer auf Englisch, technische Begriffe korrekt

## PREPARATION-Block

Der wichtigste Abschnitt für saubere Ergebnisse. Der Agent soll verstehen
bevor er handelt.

```
## PREPARATION

Before writing any code, read these files to understand the project:
1. `CLAUDE.md` — Project overview, tech stack, conventions
2. `src/routes/auth.ts` — Current auth implementation
3. `prisma/schema.prisma` — Database schema
```

**Skill-Referenzen einbauen** wenn sie dem Ergebnis helfen:
```
Read the `code-standards` skill at `/path/to/code-standards/SKILL.md`
for naming and formatting conventions.
```

## Beispiele nach Aufgabentyp

### new-project

```
## PREPARATION

Read the `code-standards` skill at `/path/to/code-standards/SKILL.md`
for project structure and naming conventions.

## TASK

Create a new REST API project for a recipe management app.

## TECH STACK
- Runtime: Node.js with TypeScript
- Framework: Hono
- Database: SQLite with Prisma ORM
- Validation: Zod

## PROJECT STRUCTURE
/recipe-api
├── prisma/
│   └── schema.prisma
├── src/
│   ├── routes/
│   │   ├── recipes.ts      (CRUD operations)
│   │   └── tags.ts         (tag management)
│   ├── middleware/
│   │   └── validate.ts     (Zod schema validation)
│   ├── services/
│   │   ├── recipe.service.ts
│   │   └── tag.service.ts
│   ├── types/
│   │   └── index.ts
│   └── index.ts            (Hono app setup)
├── package.json
├── tsconfig.json
└── .env.example

## MVP SCOPE
### Endpoints
- GET /recipes (list, support ?tag=X filter)
- GET /recipes/:id (detail with ingredients)
- POST /recipes (create with title, ingredients, steps)
- PATCH /recipes/:id (update)
- DELETE /recipes/:id (soft delete)
- GET /tags (list all tags)
- POST /tags (create tag)

### Data Model
- Recipe: id, title, description, servings, prep_time, created_at
- Ingredient: id, recipe_id, name, amount, unit
- Step: id, recipe_id, order, instruction
- Tag: id, name
- RecipeTag: recipe_id, tag_id (many-to-many)

## CONSTRAINTS
- Consistent error responses: { error: string, code: number }
- All list endpoints support pagination (?page=1&limit=20)
- Soft delete via deleted_at timestamp, never hard delete
- Environment variables for port and DB path via .env

## OUTPUT FORMAT
- All files with complete, runnable code
- README.md with setup instructions
- .env.example with all required variables
```

### feature

```
## PREPARATION

Read CLAUDE.md for project context.
Read `src/routes/recipes.ts` to understand existing endpoint patterns.
Read `prisma/schema.prisma` for the current data model.

## TASK

Add image upload support to recipes.

### Requirements
1. Users can upload one image per recipe (JPEG or PNG, max 5MB)
2. Images are stored in /uploads directory
3. Image URL is returned in recipe detail response
4. Uploading a new image replaces the old one

### Implementation
- Add `image_url` field to Recipe model (nullable string)
- New endpoint: POST /recipes/:id/image (multipart form upload)
- Serve static files from /uploads
- Validate file type and size before saving

## CONSTRAINTS
- Do NOT install sharp or any image processing library
- Do NOT change existing endpoint response shapes
- Store original file, no resizing
- Use multer for file upload handling
```

### bugfix

```
## PREPARATION

Read `src/routes/recipes.ts` — the broken endpoint.
Read `src/services/recipe.service.ts` — the service layer.

## CONTEXT

The PATCH /recipes/:id endpoint returns 500 when updating only the title
without sending ingredients. It works fine when the full recipe is sent.

Error: "Cannot read properties of undefined (reading 'map')"

## TASK

Fix the PATCH endpoint to handle partial updates correctly.

### Expected behavior
- Sending { title: "New Title" } updates only the title
- Omitted fields remain unchanged
- Ingredients array is optional in PATCH requests

### Acceptance criteria
- [ ] PATCH with only title works
- [ ] PATCH with only ingredients works
- [ ] PATCH with both works
- [ ] Empty PATCH body returns 400
```

### refactor

```
## PREPARATION

Read the `code-standards` skill for target conventions.
Read all files in `src/routes/` — the code to refactor.

## TASK

Refactor the monolithic routes file (~800 lines) into a modular structure.

### Steps
1. Identify logical groupings in the current file
2. Create separate route files per domain
3. Extract shared validation schemas to middleware/
4. Update index.ts to register all routes
5. Verify no endpoints are lost or changed

## CONSTRAINTS
- Zero behavior change — all endpoints must work identically
- Preserve all HTTP methods, paths, status codes, response shapes
- Do NOT refactor business logic, only file organization
- Do NOT rename any endpoints
```

### review

```
## PREPARATION

Read CLAUDE.md for project context and conventions.
Read all files in `src/` recursively.

## TASK

Perform a comprehensive code review of the entire codebase.

### Evaluate
1. Code quality (duplication, dead code, naming consistency)
2. Error handling (uncaught exceptions, missing validation)
3. Security (input validation, SQL injection, exposed secrets)
4. Performance (unnecessary queries, missing indexes)
5. Maintainability (types, documentation, test coverage)

## OUTPUT FORMAT

Single markdown document:
1. **Summary** — Overall assessment (3-5 sentences)
2. **Critical issues** — Must fix before production
3. **Warnings** — Should fix soon
4. **Suggestions** — Nice to have improvements
5. **Top 5 action items** — Ordered by impact

Each issue: location, description, suggested fix.

Do NOT make any code changes. Analysis only.
```

## Skalierung bei grossen Projekten

Wenn das Projekt gross ist (viele Dateien, viele Features):

```
## SCALE CONSTRAINT

This is a large project. Follow these rules:
- Do NOT attempt to read every file
- Start with CLAUDE.md and top-level structure
- Focus on the specific area described in TASK
- Use top-down analysis: directory structure first, then drill into
  relevant files only
- Skip: node_modules, build output, .git, lock files
```
