# Tool-Guide

Wann welches Tool im VibeCoding-Workflow einsetzen.

## Claude Chat (claude.ai)

**Primäres Tool für Phase 1-4 und 6.**

Stärken:
- Denken, planen, entscheiden
- Komplexe Fragen diskutieren
- Skills und MCP-Server nutzen
- Notion-Integration
- Dokumente und Prompts generieren

Einsetzen für:
- Ideenfindung und Problemdefinition
- Requirements und Scope festlegen
- Architektur-Entscheidungen treffen
- CLAUDE.md und Prompts generieren
- Code-Reviews (Code einfügen oder Repo verlinken)
- Projekt-Dokumentation

Nicht einsetzen für:
- Direkte Code-Generierung in bestehenden Projekten
- Dateien im Projekt lesen/schreiben

## Cursor AI

**Primäres Tool für Phase 5 — Code-Generierung.**

Stärken:
- Multi-File-Editing
- Projektkontext (liest automatisch CLAUDE.md / .cursorrules)
- Composer für grössere Änderungen
- Tab-Completion für kleine Änderungen
- Inline-Chat für punktuelle Fragen

Einsetzen für:
- Neues Projekt aufsetzen (Composer)
- Features implementieren (Composer)
- Multi-File Refactoring (Composer)
- Kleine Änderungen (Tab / Inline Chat)
- UI-Arbeit mit Live-Preview

Nicht einsetzen für:
- Planung und Architektur-Entscheidungen
- Shell-Befehle und Deployment
- Aufgaben die viel Terminal-Interaktion brauchen

### Cursor-spezifische Tipps:
- `.cursorrules` im Projekt-Root = wird bei jedem Prompt geladen
- `CLAUDE.md` im Root = wird von Claude-Modellen gelesen
- Composer: Für grössere Aufgaben, kann mehrere Dateien auf einmal ändern
- Bei Problemen: Neuen Chat starten statt langen Kontext mitschleppen

## Claude Code (Terminal)

**Sekundäres Tool für Phase 5 + primär für Phase 6.**

Stärken:
- Direkter Dateizugriff im Terminal
- Shell-Befehle ausführen
- Git-Operationen
- Schnelle Iterationen
- Gut für Debugging (Logs lesen, Tests ausführen)

Einsetzen für:
- Schnelle Änderungen an einzelnen Dateien
- Debugging und Fehlerbehebung
- Git-Workflow (Commits, Branches, PRs)
- Tests ausführen und prüfen
- Build-Scripts und Deployment
- Code-Reviews mit Dateizugriff

Nicht einsetzen für:
- Grosse Multi-File-Refactorings (Cursor ist besser)
- UI-Arbeit ohne Preview
- Initiales Projekt-Setup bei komplexen Projekten

### Claude Code Tipps:
- `CLAUDE.md` im Root wird automatisch gelesen
- Arbeitet gut mit bestehenden Projekten
- Für Reviews: "Review the codebase, focus on [X]"

## Entscheidungsbaum: Welches Tool?

```
Planst du oder baust du?
├── Planen → Claude Chat
│   ├── Idee besprechen → Phase 1
│   ├── Requirements sammeln → Phase 2
│   ├── Stack entscheiden → Phase 3
│   └── Prompt/CLAUDE.md → Phase 4
│
└── Bauen → Cursor oder Claude Code
    ├── Neues Projekt aufsetzen → Cursor (Composer)
    ├── Feature implementieren → Cursor
    ├── Quick Fix / Debugging → Claude Code
    ├── Git Operations → Claude Code
    ├── Tests ausführen → Claude Code
    └── Code Review → Claude Code oder Claude Chat
```

## GitHub

**Überall präsent — Versionierung und Standards.**

- Projekt-Repos: Code + CLAUDE.md + .cursorrules
- Framework-Repo: Standards, Templates, Referenzen
- Issues: Feature-Tracking und Bug-Reports
- PRs: Code-Reviews (besonders im Team)

## Notion

**Phase 1-3 und Projektmanagement.**

- Projekt-Dokumentation (Framework-Doku, Wiki)
- Aufgaben-Tracking (Tasks DB)
- Meeting Notes (bei Teamwork)
- Langzeit-Wissen (Wiki DB)
