# Stack-Guide

Bewährte Tech-Stack-Kombinationen nach Projekttyp.
Nicht dogmatisch — wenn der User einen anderen Stack bevorzugt, respektieren.

## Web-App (Frontend + Backend)

### Empfehlung A: Next.js Fullstack
- **Framework:** Next.js (App Router)
- **Sprache:** TypeScript
- **Styling:** Tailwind CSS
- **DB:** SQLite (lokal) oder PostgreSQL (Production)
- **ORM:** Prisma
- **Auth:** NextAuth.js oder Lucia
- **Gut für:** Dashboards, interne Tools, Content-Apps
- **Trade-off:** Opinionated, Server Components Lernkurve

### Empfehlung B: SvelteKit
- **Framework:** SvelteKit
- **Sprache:** TypeScript
- **Styling:** Tailwind CSS
- **DB:** SQLite oder PostgreSQL
- **ORM:** Drizzle
- **Auth:** Lucia
- **Gut für:** Performance-kritische Apps, einfachere Projekte
- **Trade-off:** Kleineres Ökosystem als React

### Empfehlung C: Vite + React SPA + separates Backend
- **Frontend:** Vite + React + Tailwind
- **Backend:** Express.js oder Hono
- **Sprache:** TypeScript
- **DB:** PostgreSQL mit Prisma
- **Auth:** JWT oder Session-basiert
- **Gut für:** Wenn Frontend und Backend unabhängig deployt werden
- **Trade-off:** Mehr Boilerplate, zwei Deployments

## API / Backend-Service

### Empfehlung A: Node.js + Express/Hono
- **Runtime:** Node.js
- **Framework:** Hono (modern, schnell) oder Express (bewährt)
- **Sprache:** TypeScript
- **DB:** PostgreSQL mit Prisma
- **Validation:** Zod
- **Gut für:** REST APIs, Microservices
- **Trade-off:** Express ist alt aber stabil, Hono neuer aber weniger Docs

### Empfehlung B: Python + FastAPI
- **Runtime:** Python 3.11+
- **Framework:** FastAPI
- **DB:** PostgreSQL mit SQLAlchemy
- **Validation:** Pydantic (built-in)
- **Gut für:** Data-heavy APIs, ML-Integration, Scripting-Background
- **Trade-off:** Async kann komplex werden, Deployment braucht mehr Setup

## Homelab / Self-Hosted Service

### Empfehlung: Docker Compose
- **Container:** Docker auf LXC (Proxmox)
- **Reverse Proxy:** Nginx Proxy Manager
- **Storage:** Synology NAS via NFS
- **Monitoring:** Uptime Kuma oder eigenes Dashboard
- **Gut für:** Alles im Homelab
- **Referenz:** `docker-compose` + `homelab-kontext` Skills

### Wenn eigener Code nötig:
- **Runtime:** Node.js oder Python
- **Web UI:** Simple HTML + Tailwind (kein Framework-Overhead)
- **DB:** SQLite (lokal im Container)
- **Config:** Environment Variables via `.env`

## Automation / Scripts

### Empfehlung A: Python
- **Sprache:** Python 3.11+
- **Task Runner:** Standalone Script oder Typer (CLI)
- **Scheduling:** Cron oder systemd Timer
- **Gut für:** Datenverarbeitung, API-Calls, File-Operationen
- **Referenz:** `code-standards` Skill (Python-Standards)

### Empfehlung B: Bash
- **Sprache:** Bash mit `set -euo pipefail`
- **Scheduling:** Cron
- **Gut für:** System-Tasks, einfache Automatisierungen
- **Trade-off:** Begrenzt bei komplexer Logik
- **Referenz:** `code-standards` Skill (Bash-Standards)

## Entscheidungshilfe

```
Braucht es ein UI?
├── Ja → Web-App
│   ├── Fullstack (eine Codebasis) → Next.js oder SvelteKit
│   └── Frontend + Backend getrennt → Vite + Express/Hono
│
└── Nein
    ├── API für andere Konsumenten → Express/Hono oder FastAPI
    ├── Selbst-gehosteter Service → Docker Compose
    └── Wiederkehrende Aufgabe → Python oder Bash Script
```

## Prinzipien bei der Stack-Wahl

1. **Bekanntes bevorzugen** — Ein Stack den der User kennt schlägt den "besten" Stack
2. **Komplexität passend zum Projekt** — Kein Kubernetes für eine Todo-App
3. **Ökosystem beachten** — Gute Docs und Community sparen Stunden
4. **Deployment mitdenken** — Wo soll es am Ende laufen?
5. **Bei Unsicherheit: SQLite** — Reicht für überraschend viele Projekte
