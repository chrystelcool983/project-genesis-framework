# GitHub Copilot / Codex Adapter — Project Genesis Framework

## How Copilot Works

GitHub Copilot reads project context from:
- **`.github/copilot-instructions.md`** — The primary custom instructions file (Copilot in VS Code, JetBrains, Visual Studio). Applied to every Copilot Chat interaction in the repo.
- **`.github/instructions/*.instructions.md`** — Scoped instruction files (VS Code only, Copilot 1.x+). Can target specific file types or paths.
- **`copilot-setup-steps.yml`** — Codex cloud agent environment setup (GitHub.com agent tasks).

Copilot does **not** have a plugin or command system like Claude Code. Context is injected through the instructions file and whatever files are open in the editor.

---

## Setup Instructions

### Step 1: Generate your instructions file

Run this prompt:

```
Read core/UNIVERSAL_SPEC.md from this project.
Generate a .github/copilot-instructions.md file following adapters/copilot/ADAPTER.md.
Be specific and concise — Copilot performs best with dense, direct instructions.
```

### Step 2: File placement

```
.github/
├── copilot-instructions.md          # Primary — applies to all Copilot Chat
└── instructions/
    ├── typescript.instructions.md   # Scoped to *.ts, *.tsx files
    ├── api.instructions.md          # Scoped to apps/api/**
    ├── frontend.instructions.md     # Scoped to apps/web/**
    ├── mobile.instructions.md       # Scoped to apps/mobile/** (if applicable)
    └── testing.instructions.md      # Scoped to **/*.test.*
```

### Step 3: Enable in VS Code settings

```json
{
  "github.copilot.chat.codeGeneration.useInstructionFiles": true
}
```

---

## `copilot-instructions.md` Template

This is the primary file. Keep it focused — Copilot has a shorter effective context window than Claude Code.

```markdown
# [PROJECT_NAME] — Copilot Instructions

## What this project is
[Elevator pitch from UNIVERSAL_SPEC]. [Platform summary — web, mobile, API].

## Stack
[Frontend framework] · [Backend language + framework] · [Database] · [Cache] · [Auth] · [Cloud]

## Repository layout
- `apps/web/` — [web app]
- `apps/api/` — [API]  
- `apps/mobile/` — [mobile, if applicable]
- `packages/types/` — shared types
- `docs/` — PRD.md, EDD.md, schema.sql, architecture.md

## Code rules (follow strictly)
- TypeScript strict mode — no implicit any, no ts-ignore without comment
- Absolute imports via tsconfig paths — never relative paths with ../../
- Zod schemas at every API boundary
- Structured logger at `packages/logger` — never console.log
- Parameterised queries or ORM only — never string-concatenated SQL
- Typed errors — never throw raw strings

## Naming
- Files: kebab-case | Components: PascalCase | Variables: camelCase | Constants: SCREAMING_SNAKE
- Database: snake_case tables and columns

## API response format
```json
// Success: { "success": true, "data": { ... } }
// Error:   { "success": false, "error": { "code": "ERROR_CODE", "message": "..." } }
```

## Backend module structure
`feature.router.ts` → `feature.controller.ts` → `feature.service.ts` + `feature.types.ts` + `feature.test.ts`

## Security — never violate
- No secrets in code
- Validate all input before use
- Auth check before serving any protected data
- No sensitive fields in logs (passwords, tokens, PII)
- HTTPS-only cookies, SameSite=Strict

## Before finishing a task
- Write or update tests (80% coverage target for services)
- Note any changes needed to docs/architecture.md
```

---

## Scoped Instruction Files (VS Code)

These files apply only when Copilot is working on matched files. More specific than the main file.

### `typescript.instructions.md`
```markdown
---
applyTo: "**/*.ts,**/*.tsx"
---

Use TypeScript strict mode. Prefer `const` over `let`. Never use `var`.
Always type function parameters and return values explicitly.
Use discriminated unions for state that has multiple valid shapes.
Prefer `unknown` over `any` — always narrow before use.
Use `satisfies` operator when you want type inference + constraint checking.
```

### `api.instructions.md`
```markdown
---
applyTo: "apps/api/**"
---

Implementation order: schema → controller → service → route registration → tests.
Every endpoint must: validate input (Zod), check auth, call service, return typed response.
Service functions return `Result<T, AppError>` — never throw across service boundaries.
Check cache before hitting database. Set TTL on every cache write.
Add the new endpoint to `docs/architecture.md` endpoint table when done.
```

### `frontend.instructions.md`
```markdown
---
applyTo: "apps/web/**"
---

Server components by default. Add 'use client' only for interactivity or browser APIs.
Server state via TanStack Query — never fetch in useEffect.
Skeleton loaders for loading states — not spinners for content areas.
Follow the design system in core/UNIVERSAL_SPEC.md Section 8.
All interactive elements need visible focus styles. WCAG 2.1 AA minimum.
```

### `testing.instructions.md`
```markdown
---
applyTo: "**/*.test.*,**/*.spec.*"
---

Structure: describe('feature') → describe('when condition') → it('should outcome').
Every test: Arrange / Act / Assert with a blank line between sections.
Always test: happy path, invalid input, not found, unauthorised access.
Mock external services (never hit real APIs in tests).
Use test fixtures from .claude/plugins/testing-qa.md for consistent test data.
```

---

## `copilot-setup-steps.yml` (Codex Cloud Agent)

For GitHub Codex agent tasks run on GitHub.com, define the environment setup:

```yaml
# .github/copilot-setup-steps.yml
# Runs before every Codex agent task to set up the dev environment

steps:
  - name: Install Node.js
    run: |
      nvm install 20
      nvm use 20

  - name: Install dependencies
    run: pnpm install

  - name: Set up environment
    run: cp .env.example .env.test

  - name: Start database
    run: docker compose up -d postgres redis

  - name: Run migrations
    run: pnpm db:migrate

  - name: Seed test data
    run: pnpm db:seed:test
```

---

## Copilot-Specific Tips for Genesis Projects

**Reference docs in chat:**
```
#file:docs/EDD.md implement the user profile endpoint from Section 5.3
```

**Use `#file` for schema context:**
```
#file:docs/schema.sql add a migration to add the `last_login_at` column to the users table
```

**Ask Copilot to follow the pattern:**
```
Follow the same structure as #file:apps/api/src/auth/auth.service.ts and implement the notifications service
```

**Workspace context:**
Install the GitHub Copilot extension and use "Copilot Chat: Explain This" on complex sections of the EDD to get a fast orientation before implementing.

**Agent mode (VS Code):**
Use `@workspace` in Copilot Chat to search across the full monorepo:
```
@workspace where is the rate limiting middleware and how does it work?
```
