# Beispiele

Vollständige CLAUDE.md Beispiele für verschiedene Projekttypen.
Als Vorlage nutzen und an das konkrete Projekt anpassen.

## Web-App: Rezept-Manager

```markdown
# Recipe Manager

## Goal
My recipes are scattered across Notion, screenshots, and bookmarks. I need
a single place to store, search, and scale recipes with automatic portion
adjustment. Used by me and my partner.

## Tech Stack
- **Framework:** Next.js 14 (App Router)
- **Language:** TypeScript (strict mode)
- **Styling:** Tailwind CSS
- **Database:** SQLite via Prisma ORM
- **Auth:** None (local network only)
- **Deployment:** Docker container, self-hosted

## Project Structure
src/
├── app/
│   ├── layout.tsx            # Root layout with nav
│   ├── page.tsx              # Recipe list (home)
│   ├── recipes/
│   │   ├── [id]/page.tsx     # Recipe detail + scaling
│   │   └── new/page.tsx      # Add recipe form
│   └── api/
│       └── recipes/
│           └── route.ts      # REST endpoints
├── components/
│   ├── RecipeCard.tsx
│   ├── IngredientList.tsx
│   └── ScalingSlider.tsx
├── lib/
│   ├── db.ts                 # Prisma client
│   └── types.ts              # Shared interfaces
prisma/
└── schema.prisma
Dockerfile
docker-compose.yml

## Data Model
- Recipe: id, title, description, servings, prep_time, image_url, created_at
- Ingredient: id, recipe_id, name, amount, unit, order
- Step: id, recipe_id, instruction, order
- Tag: id, name (unique)
- RecipeTag: recipe_id, tag_id (many-to-many)

## MVP Scope
- [ ] List all recipes with search and tag filter
- [ ] View recipe detail with ingredient scaling slider
- [ ] Add new recipe with title, ingredients, steps, tags
- [ ] Edit existing recipe
- [ ] Delete recipe (soft delete)

## Conventions
- Components: PascalCase, one component per file
- Functions/variables: camelCase
- Files: kebab-case for non-component files
- Imports: React first, then libs, then local — separated by blank line
- Comments: Technical and concise, describe WHAT not WHY
- Error handling: Try/catch at API boundary, typed error responses

## Rules
- Always use TypeScript strict mode, never use `any`
- All components must be responsive (mobile-first with Tailwind)
- Use server components by default, client components only when needed
- Do NOT install additional npm packages without asking
- Do NOT use inline styles — Tailwind utility classes only
- Do NOT leave console.log in production code
- All API responses follow: { data: T } or { error: string, code: number }
- Soft delete via deleted_at timestamp, never hard delete

## Environment Variables
- DATABASE_URL: SQLite file path (default: file:./dev.db)
- PORT: Server port (default: 3000)
```

## API / Backend: Task-API

```markdown
# Task API

## Goal
Backend API for a task management app. Supports user authentication,
task CRUD with priorities and due dates, and project-based organization.
Consumed by a separate React frontend.

## Tech Stack
- **Runtime:** Node.js 20+
- **Framework:** Hono
- **Language:** TypeScript
- **Database:** PostgreSQL via Prisma
- **Auth:** JWT (access + refresh tokens)
- **Validation:** Zod
- **Deployment:** Docker on VPS

## Project Structure
src/
├── routes/
│   ├── auth.ts               # register, login, refresh, logout
│   ├── tasks.ts              # CRUD + filters
│   └── projects.ts           # project management
├── middleware/
│   ├── auth.ts               # JWT verification
│   └── validate.ts           # Zod schema middleware
├── services/
│   ├── auth.service.ts       # password hashing, token generation
│   ├── task.service.ts       # task business logic
│   └── project.service.ts
├── types/
│   └── index.ts              # shared interfaces
└── index.ts                  # Hono app setup + route registration
prisma/
└── schema.prisma

## Data Model
- User: id, email, password_hash, created_at
- Project: id, user_id, name, color, created_at
- Task: id, user_id, project_id?, title, description?, priority (1-4),
  status (open|in_progress|done), due_date?, deleted_at?, created_at

## API Endpoints
### Auth
- POST /auth/register (email, password → tokens)
- POST /auth/login (email, password → tokens)
- POST /auth/refresh (refresh_token → new access_token)
- POST /auth/logout (invalidate refresh_token)

### Tasks (all require auth)
- GET /tasks (?status=X&priority=X&project_id=X&due_before=X)
- POST /tasks (title, description?, priority, project_id?, due_date?)
- PATCH /tasks/:id (partial update)
- DELETE /tasks/:id (soft delete)

### Projects (all require auth)
- GET /projects (list user's projects with task counts)
- POST /projects (name, color)
- PATCH /projects/:id
- DELETE /projects/:id (fails if tasks exist)

## Conventions
- Functions/variables: camelCase
- Files: kebab-case
- Route handlers: thin — delegate to service layer
- Services: contain business logic, return typed results
- Validation: Zod schemas co-located with routes
- Comments: Technical, concise — WHAT not WHY

## Rules
- Passwords hashed with bcrypt, never stored plain text
- Each user can only access their own data (enforce in every query)
- All endpoints return: { data: T } or { error: string, code: number }
- All list endpoints support pagination: ?page=1&limit=20
- Soft delete via deleted_at, never hard delete
- Do NOT use `any` type
- Do NOT put business logic in route handlers
- Do NOT return password_hash in any response
- Always validate request body with Zod before processing
```

## Homelab: Docker Service

```markdown
# Uptime Dashboard

## Goal
Simple dashboard showing the status of all Docker containers running
in my homelab. Replaces manual SSH checks with a single web view.

## Tech Stack
- **Runtime:** Node.js 20
- **Framework:** Express + EJS templates
- **Styling:** Tailwind CSS (CDN)
- **Database:** None (reads Docker API live)
- **Deployment:** Docker container on LXC (Proxmox)

## Project Structure
src/
├── index.ts                  # Express app + routes
├── docker.ts                 # Docker API client
├── types.ts
└── views/
    ├── layout.ejs
    └── dashboard.ejs
Dockerfile
docker-compose.yml
.env.example

## Conventions
- Follow homelab Docker Compose standards
- Container name: uptime-dashboard
- Network: proxy_network (for Nginx Proxy Manager)
- Volumes: /var/run/docker.sock read-only mount

## Rules
- Docker socket mounted read-only — never write operations
- No authentication (internal network only)
- No database — all data fetched live from Docker API
- Page must load in under 2 seconds
- Must work on mobile screens
- Do NOT install heavy frameworks — keep the container small
- Do NOT expose any port except 3000

## Environment Variables
- PORT: 3000
- DOCKER_SOCKET: /var/run/docker.sock
- REFRESH_INTERVAL: 30 (seconds, for auto-refresh)
```

## Automation: Backup-Monitor

```markdown
# Backup Monitor

## Goal
Monitors backup jobs across the homelab and sends notifications when
a backup fails or hasn't run in over 24 hours. Runs as a cron job.

## Tech Stack
- **Language:** Python 3.11+
- **Notifications:** Apprise (multi-channel)
- **Scheduling:** Cron (external, not in script)
- **Config:** YAML config file

## Project Structure
backup-monitor/
├── monitor.py                # Main script
├── config.yaml               # Backup job definitions
├── lib/
│   ├── checker.py            # Check backup status
│   └── notifier.py           # Send notifications
├── requirements.txt
└── README.md

## Conventions
- Python: snake_case everywhere
- Docstrings: Google style, only for public functions
- Logging: Python logging module, not print()
- Config: YAML, not hardcoded values

## Rules
- Script must exit 0 on success, 1 on failure
- All errors must be caught and logged — never crash silently
- Notification failures must not prevent other checks from running
- Do NOT use relative paths — resolve everything from script directory
- Do NOT store credentials in config.yaml — use environment variables
- Script must complete in under 60 seconds

## Environment Variables
- NTFY_URL: Notification server URL
- NTFY_TOPIC: Topic for alerts
```

## .cursorrules Beispiele

### Kurz (Web-App)
```
You are working on Recipe Manager, a self-hosted recipe app with
ingredient scaling built with Next.js 14, TypeScript, Tailwind, and SQLite.

Conventions:
- TypeScript strict, never use `any`
- Components: PascalCase, one per file
- Server components by default
- Tailwind only, no inline styles or CSS files

Rules:
- Do NOT install packages without asking
- Do NOT use client components unless state/effects needed
- All API responses: { data: T } or { error: string, code: number }
- Mobile-first responsive design

Read CLAUDE.md for full project context.
```

### Kurz (API)
```
You are working on Task API, a Hono REST API with JWT auth,
PostgreSQL/Prisma, and Zod validation.

Conventions:
- Thin route handlers, logic in services
- Zod schemas co-located with routes
- camelCase for code, kebab-case for files

Rules:
- Do NOT put business logic in routes
- Do NOT return password_hash in responses
- Do NOT use `any` — proper interfaces always
- All responses: { data: T } or { error: string, code: number }
- Validate all input with Zod before processing

Read CLAUDE.md for full context, data model, and endpoints.
```
