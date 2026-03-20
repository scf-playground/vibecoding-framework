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

### Chat-Sessions: Wann neuen Chat starten?

Claude's Kontext-Qualität sinkt in langen Gesprächen. Ab ~30 Nachrichten
verliert der Agent Details aus dem Gesprächsanfang. Deshalb: Phasen auf
mehrere Chat-Sessions verteilen.

**Empfohlene Aufteilung:**

```
Chat 1: Phase 1 + 2 (Idee → Problem → Requirements)
  → Output: Problem-Statement + Requirements-Dokument
  → Diesen Output kopieren/speichern

Chat 2: Phase 3 (Architektur + Tech-Stack)
  → Input: Requirements aus Chat 1 einfügen
  → Output: Tech-Stack + Ordnerstruktur + Datenmodell
  → Diesen Output kopieren/speichern

Chat 3: Phase 4 (CLAUDE.md + Prompt-Sequenz)
  → Input: Requirements + Architektur-Entscheidung einfügen
  → Output: CLAUDE.md + Prompts als Dateien
```

**Wann zusammenlegen?** Phase 1+2 können in einem Chat bleiben weil sie
ein natürlicher Gesprächsfluss sind (Problem → Requirements). Phase 3 und 4
sollten getrennt sein — die Architektur-Entscheidung ist ein harter Schnitt,
und die Prompt-Generierung profitiert von frischem Kontext.

**Bei kleinen Projekten (Grösse S):** Ein Chat für alles reicht — das
Gespräch ist kurz genug dass kein Kontext verloren geht.

**Bei grossen Projekten (Grösse L/XL):** Eher mehr Chats, nicht weniger.
Phase 3 könnte sogar zwei Chats brauchen wenn das Datenmodell komplex ist
(z.B. Legacy-DB analysieren in einem Chat, Stack-Entscheidung im nächsten).

**Kontext-Übergabe zwischen Chats:**
- Den Output der vorherigen Phase als Text oder Datei in den neuen Chat
  einfügen. Nicht nur beschreiben — den tatsächlichen Text mitgeben.
- Die Projektanweisung (System-Prompt) enthält den Framework-Kontext
  und muss nicht wiederholt werden.
- Kein ganzes Gesprächsprotokoll mitgeben — nur die Ergebnisse.

**Phase 5 (Umsetzung) — Prompt-Sequenz über mehrere Chats:**
Wenn die Prompt-Sequenz 4+ Prompts hat, zwischen den Prompts in Claude Chat
zurückkommen für den nächsten Prompt. Auch hier: neuer Chat pro Prompt ist
besser als ein Endlos-Chat. Der User beschreibt kurz was seit dem letzten
Prompt passiert ist und was angepasst werden muss.

**Phase 6 (Review):** Immer eigener Chat — mit frischem Kontext und dem
fertigen Code als Input. Nie im selben Chat wie die Implementierung reviewen.

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
- Pro Prompt aus der Sequenz: Neuer Composer-Chat (gleiche Logik wie bei Claude Chat)

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
│   ├── Idee besprechen → Phase 1+2 (Chat 1)
│   ├── Stack entscheiden → Phase 3 (Chat 2)
│   └── Prompt/CLAUDE.md → Phase 4 (Chat 3)
│
└── Bauen → Cursor oder Claude Code
    ├── Neues Projekt aufsetzen → Cursor (Composer, neuer Chat pro Prompt)
    ├── Feature implementieren → Cursor
    ├── Quick Fix / Debugging → Claude Code
    ├── Git Operations → Claude Code
    ├── Tests ausführen → Claude Code
    └── Code Review → Claude Code oder Claude Chat (frischer Chat)
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
