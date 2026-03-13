# Project Genesis — Universal Project Spec

> This file is the **agent-agnostic source of truth** for a Genesis project.
> Every AI coding agent adapter (Claude Code, Cursor, Copilot, Windsurf, Aider, OpenHands, Continue)
> reads this file and maps its sections to the agent's native format.
>
> **If you are a human:** Fill in the `[PLACEHOLDER]` values for your project.
> **If you are an AI agent:** Read this file first, then read your adapter file in `adapters/YOUR_AGENT/`.

---

## 1. Project Identity

```
Project name:        [PROJECT_NAME]
Elevator pitch:      [One sentence: what it does and who it's for]
Problem solved:      [The pain point in one sentence]
Business model:      [How it makes money]
Target market:       [Geography, industry, user type]
MVP deadline:        [Date]
Current phase:       [Phase name + sprint number]
```

---

## 2. Platforms

```
Web:        [Yes / No] — [Framework if yes]
Mobile iOS: [Yes / No] — [React Native / Flutter / Swift if yes]
Mobile Android: [Yes / No] — [React Native / Flutter / Kotlin if yes]
API/Backend: [Yes / No] — [Language + framework]
CLI:        [Yes / No]
```

---

## 3. Tech Stack

| Layer | Technology | Version |
|-------|------------|---------|
| Frontend | [e.g. Next.js] | [e.g. 14.x] |
| Backend | [e.g. Node.js + Fastify] | [e.g. 20.x] |
| Database | [e.g. PostgreSQL] | [e.g. 16] |
| Cache | [e.g. Redis] | [e.g. 7.x] |
| Auth | [e.g. Clerk] | latest |
| Payments | [e.g. Stripe] | latest |
| Mobile | [e.g. React Native + Expo] | [e.g. SDK 51] |
| Cloud | [e.g. AWS / GCP / Vercel] | — |
| CI/CD | [e.g. GitHub Actions] | — |
| Monitoring | [e.g. Sentry] | latest |

---

## 4. Repository Layout

```
/
├── apps/
│   ├── web/          # [Web app description]
│   ├── api/          # [API service description]
│   └── mobile/       # [Mobile app description — if applicable]
├── packages/
│   ├── types/        # Shared TypeScript types
│   ├── utils/        # Shared utilities
│   └── config/       # Shared configuration
├── infrastructure/   # Terraform, Docker, scripts
├── docs/             # PRD, EDD, schema, living docs
├── .claude/          # Claude Code tooling (plugins, commands, agents)
├── adapters/         # This directory — agent-specific config files
└── core/             # This directory — universal spec
```

---

## 5. Key Reference Documents

| Document | Location | Read it when... |
|----------|----------|-----------------|
| PRD | `docs/PRD.md` | Checking feature priority or scope |
| EDD | `docs/EDD.md` | Implementing any endpoint or system component |
| Database Schema | `docs/schema.sql` | Adding tables, queries, or migrations |
| Architecture | `docs/architecture.md` | Understanding service boundaries |
| Project Status | `docs/projectstatus.md` | Checking what's done and what's next |
| Environment Config | `.env.example` | Adding or referencing any env variable |

---

## 6. Code Conventions

```
Language:         TypeScript (strict mode, no implicit any)
File naming:      kebab-case for files, PascalCase for components/classes
Variable naming:  camelCase for variables, SCREAMING_SNAKE for constants
Imports:          Absolute paths via tsconfig aliases (never ../../../)
Error handling:   Always typed, never swallowed
Logging:          Structured JSON (no console.log in production)
Validation:       Zod schemas at all API boundaries
```

---

## 7. Git Conventions

```
Branching:     feature/ticket-short-description
               fix/ticket-short-description
               chore/short-description
Commits:       Conventional Commits — type(scope): subject
               Types: feat | fix | chore | docs | test | refactor | perf
PRs:           Require 1 approval + CI passing
               Must reference issue number
               Must update living docs if behaviour changed
```

---

## 8. Design System

```
Primary colour:   [hex]
Secondary colour: [hex]
Error colour:     #EF4444
Success colour:   #22C55E
Warning colour:   #F59E0B

Font (display):   [Font name]
Font (body):      [Font name]
Font (mono):      [Font name]

Spacing scale:    4px base unit (4, 8, 12, 16, 24, 32, 48, 64, 96)
Border radius:    4px (sm), 8px (md), 12px (lg), 9999px (pill)

Accessibility:    WCAG 2.1 AA minimum
Dark mode:        [Required / Optional / Not required]
```

---

## 9. Current Sprint Tasks

> Update this section at the start of each sprint.

| Task | Priority | Status | Owner |
|------|----------|--------|-------|
| [Task description] | P0 | 🔄 In Progress | [Agent/Person] |
| [Task description] | P0 | ⬜ Not Started | — |
| [Task description] | P1 | ⬜ Not Started | — |

Status icons: ✅ Done · 🔄 In Progress · ⬜ Not Started · 🚫 Blocked

---

## 10. Hard Constraints

```
□ Never commit secrets or credentials
□ Never use console.log — use the structured logger
□ Never bypass TypeScript strict mode
□ Never write SQL without parameterised queries
□ Never modify the database schema without a migration file
□ Always validate all external input at the boundary
□ Always update living docs after completing a feature
□ Always write tests for new endpoints (coverage target: 80%)
```

---

## 11. Agent Adapter Index

Each AI coding agent has its own adapter file that maps this spec to the agent's native format:

| Agent | Adapter | Config file(s) |
|-------|---------|----------------|
| Claude Code | `adapters/claude/` | `.claude/` directory + `CLAUDE.md` |
| Cursor | `adapters/cursor/` | `.cursor/rules/` + `.cursorrules` |
| GitHub Copilot | `adapters/copilot/` | `.github/copilot-instructions.md` |
| Windsurf | `adapters/windsurf/` | `.windsurf/rules/` |
| Aider | `adapters/aider/` | `.aider.conf.yml` + `CONVENTIONS.md` |
| OpenHands | `adapters/openhands/` | `.openhands/microagents/` |
| Continue | `adapters/continue/` | `.continue/config.json` |
