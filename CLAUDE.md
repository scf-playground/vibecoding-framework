# VibeCoding Framework

## Goal
This repo is the central knowledge base for a structured AI-assisted development
workflow (VibeCoding). It contains Claude Skills that guide developers through
a 6-phase lifecycle — from idea to production — and shared standards/templates
that ensure consistent, high-quality outputs across all projects.

This is NOT an application. It is a framework repo consumed by AI agents
(Claude Chat, Claude Code, Cursor AI) as reference material and skill definitions.

## Architecture

Three layers work together:

1. **Skills** (`skills/claude/`) — Claude Skills that actively guide the user
   through workflows. Installed in Claude Chat via Settings > Skills.
2. **Standards** (`standards/`) — Cross-cutting conventions (code style, UI,
   git workflow, testing) referenced by skills and CLAUDE.md files.
3. **Project Type Modules** (`projekttypen/`) — Type-specific patterns,
   prompt sequences, and templates (web-app, api-backend, homelab, automation).

A companion Notion Wiki page ("VibeCoding Framework — Übersicht") contains
the human-readable documentation and checklists.

## Repo Structure

```
vibecoding-framework/
├── CLAUDE.md                         # This file — project context for AI agents
├── README.md                         # Public repo description
├── skills/
│   └── claude/                       # Claude Skills (uploaded to claude.ai)
│       ├── vibecoding-lifecycle/     # Master skill — orchestrates all 6 phases
│       │   ├── SKILL.md
│       │   └── references/
│       │       ├── phasen.md         # Phase templates and examples
│       │       ├── tool-guide.md     # When to use which tool + chat session mgmt
│       │       └── anti-patterns.md  # Common mistakes to avoid
│       ├── project-scoper/           # Phase 1-2: Idea → Problem → Requirements
│       │   ├── SKILL.md
│       │   └── references/
│       │       ├── templates.md      # Problem-statement + requirements templates
│       │       └── fragen.md         # Questioning techniques
│       ├── architect-prompter/       # Phase 3-4: Architecture → CLAUDE.md → Prompt
│       │   ├── SKILL.md
│       │   └── references/
│       │       ├── stack-guide.md    # Recommended stacks per project type
│       │       ├── prompt-schema.md  # Prompt structure + examples per task type
│       │       ├── prompt-sequenz.md # Complexity gate + prompt sequence logic
│       │       └── claude-md-template.md
│       ├── agent-md-generator/       # Generates CLAUDE.md + .cursorrules files
│       │   ├── SKILL.md
│       │   └── references/
│       │       └── beispiele.md      # Full CLAUDE.md examples per project type
│       └── build-reviewer/           # Phase 6: Code review, testing, deployment
│           ├── SKILL.md
│           └── references/
│               ├── review-kategorien.md  # Modular review prompt building blocks
│               └── testing-templates.md  # Checklist templates by project type
├── standards/                        # Cross-cutting standards (referenced by skills)
│   ├── code-standards/               # Naming, formatting, patterns per language
│   ├── ui-design/                    # Design tokens, components, typography
│   ├── git-workflow/                 # Branch strategy, commit conventions
│   └── testing/                      # Test patterns, coverage goals
├── projekttypen/                     # Project-type-specific patterns + sequences
│   ├── web-app/                      # Prompt sequences, legacy DB patterns, CLAUDE.md sizing
│   ├── api-backend/                  # Prompt sequences, error/pagination patterns
│   ├── homelab/
│   └── automation/
└── templates/
    └── prompt-templates/             # Reusable prompt templates
```

## The 6-Phase Lifecycle

```
Phase 1: Idea & Problem     → Problem-Statement           (Claude Chat)
Phase 2: Planning & Scope   → Requirements + MVP           (Claude Chat)
Phase 3: Architecture       → Tech-Stack + Folder Structure (Claude Chat)
  ↓ Complexity Gate (S/M/L)
Phase 4: Prompt Engineering  → CLAUDE.md + Prompt(s)        (Claude Chat)
Phase 5: Implementation     → Working Code                  (Cursor / Claude Code)
Phase 6: Review & Testing   → Production-ready Code         (Claude Chat + Claude Code)
```

Phase 1-4 happen in Claude Chat with skills guiding the process.
Phase 5 happens in Cursor AI or Claude Code — the skills prepare the context,
the user takes it into the coding tool.
Phase 6 uses the `build-reviewer` skill in Claude Chat to generate review prompts
and testing checklists, then Claude Code to execute the review.

### Complexity Gate (between Phase 3 and 4)

After architecture is approved, the project complexity is scored on 5 factors
(layers, endpoints, data model, integrations, deployment) with 0-2 points each:
- **S (0-3):** Single prompt — everything in one shot
- **M (4-6):** 2-3 prompts in sequence
- **L (7-8):** 4-5 prompts in sequence
- **XL (9-10):** 5-6 prompts in sequence

This prevents the "everything-at-once" anti-pattern where a single mega-prompt
tries to generate 15+ files and produces inconsistent results.
Details: `skills/claude/architect-prompter/references/prompt-sequenz.md`
Project-type templates: `projekttypen/web-app/` and `projekttypen/api-backend/`

## Tool Landscape

| Tool | Role | Phases |
|---|---|---|
| Claude Chat (claude.ai) | Think, plan, decide. Skills guide the workflow. | 1, 2, 3, 4, 6 |
| Cursor AI | Code generation, multi-file editing, UI work | 5 |
| Claude Code (terminal) | Quick fixes, debugging, git, reviews, deployment | 5, 6 |
| GitHub | Code hosting, standards repo, version control | All |
| Notion | Documentation, project planning, wiki | 1, 2, 3 |

## How Skills Work

Each skill follows this structure:

```
skill-name/
├── SKILL.md              # Main file — YAML frontmatter + instructions
└── references/           # Supporting docs loaded on demand
    ├── file1.md
    └── file2.md
```

### SKILL.md Anatomy

- **YAML frontmatter** (`name` + `description`): Controls when the skill triggers.
  Written in ASCII (`ae/oe/ue` instead of `ä/ö/ü`) because it is a technical identifier.
- **Markdown body**: The actual instructions. Written with real umlauts (`ä/ö/ü`).
  Must stay under ~500 lines. Heavy detail goes into `references/` files.

### Skill Hierarchy

```
vibecoding-lifecycle (master)
├── delegates Phase 1-2 to → project-scoper
├── delegates Phase 3-4 to → architect-prompter
│   └── delegates CLAUDE.md generation to → agent-md-generator
└── delegates Phase 6 to → build-reviewer
```

Skills are designed to work independently too — if only `architect-prompter`
is installed, it works standalone without the master skill.

## Conventions

### Language
- Skill body text and references: German with real umlauts (ä/ö/ü)
- YAML frontmatter `description`: ASCII only (ae/oe/ue) — technical identifier
- CLAUDE.md files generated FOR projects: English — AI tools work better with it
- Prompts generated FOR Cursor/CC: English
- Use "ss" instead of "ß" everywhere (Swiss German convention)
- "Wenn du unsicher bist, behandle es als normalen Text und nutze ä/ö/ü."

### Skill Writing
- Keep SKILL.md under 500 lines; delegate detail to references/
- Use `ask_user_input` for closed questions, never text-based option lists
- Include freigabe-gates (approval gates) between phases
- Skills must gracefully degrade — work without sub-skills if they are not installed
- Description should be "pushy" to ensure reliable triggering
- Include concrete examples, not just abstract rules

### File Naming
- Skill folders: kebab-case (`project-scoper`, `agent-md-generator`)
- Reference files: kebab-case (`stack-guide.md`, `anti-patterns.md`)
- No spaces, no special characters in filenames
- No "claude" in skill names — use tool-agnostic names

### Comments in Markdown
- Technical and concise — describe WHAT, not WHY
- No conversational tone in skill instructions

## External Dependencies

### Notion
The framework documentation lives in the Notion Wiki DB:
- Page: "VibeCoding Framework — Übersicht"
- DB: Wiki (data_source_id: 94dcd698-9f50-4080-af8b-f4a45f378193)
- Tags: Coding, AI, Projekte
- Status: Entwurf

Skills can optionally create Notion projects and tasks via the
`notion-life-os` skill (separate, not part of this repo).

### Existing Claude Skills (not in this repo)
These skills exist separately and are referenced by framework skills:
- `code-standards` — Language-specific naming, formatting, patterns
- `docker-compose` — Homelab Docker stack conventions
- `homelab-kontext` — Infrastructure reference (Proxmox, NAS, network)
- `notion-life-os` — Notion workspace operations

## Rules
- Do NOT rename skill folders without updating all cross-references in other skills
- Do NOT put "claude" in skill names (use tool-agnostic names)
- Do NOT mix languages within a single file (either full German or full English)
- Do NOT hardcode Notion page IDs in skills — use search to find pages
- Always test skill changes by triggering them with realistic prompts
- When adding a new skill, update the Notion Wiki page to reflect it
- When modifying skill descriptions, verify triggering still works reliably
- Standards in `standards/` are referenced by skills — changing them affects
  all projects that use those skills
- Keep `projekttypen/` and `standards/` folders even if empty (.gitkeep) —
  they are part of the planned structure
- When updating prompt-sequenz.md or projekttypen, verify the scoring heuristic
  still produces sensible results for known test projects

## Planned Work
- [x] ~~`build-reviewer` skill (Phase 6: code review + testing + deployment readiness)~~
- [ ] Fill `standards/` with actual content (code-standards, ui-design, git-workflow, testing)
- [x] ~~Fill `projekttypen/` with project-type-specific patterns and templates~~ (web-app, api-backend done)
- [ ] Fill `projekttypen/homelab/` and `projekttypen/automation/` with patterns
- [ ] Add prompt templates to `templates/prompt-templates/`
- [ ] Update README.md with proper framework overview
- [x] ~~First real-world test: run a complete project through all 6 phases~~ (Article Manager test)
- [ ] CLAUDE.md length guidance in agent-md-generator for DB-heavy projects
- [ ] Second test: run a project through the updated workflow with prompt sequences
- [ ] Update Notion Wiki page with current skill inventory
