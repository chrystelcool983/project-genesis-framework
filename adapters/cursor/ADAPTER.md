# Cursor Adapter — Project Genesis Framework

## How Cursor Works

Cursor reads context from two places:
- **`.cursor/rules/`** — Markdown rule files (Cursor 0.45+). Each file is a named rule with a scope.
- **`.cursorrules`** — Legacy single-file format (still supported, lower priority than rules/).

Cursor injects rules into the system prompt for every AI interaction in the editor. Rules can be scoped to file globs, always-on, or agent-only.

---

## Setup Instructions

### Step 1: Create the rules directory

```bash
mkdir -p .cursor/rules
```

### Step 2: Generate your rules files

Run this Genesis prompt in Claude (or your preferred LLM):

```
Read core/UNIVERSAL_SPEC.md from this project.
Generate Cursor rule files for this project following the adapters/cursor/ADAPTER.md format.
Output each file separately with its exact filename.
```

### Step 3: File placement

```
.cursor/
└── rules/
    ├── project-context.mdc      # Always-on: project identity, stack, layout
    ├── code-conventions.mdc     # Always-on: naming, imports, error handling
    ├── git-conventions.mdc      # Always-on: branching, commits, PR rules
    ├── api-patterns.mdc         # Agent: backend endpoint patterns (scoped to apps/api/**)
    ├── frontend-patterns.mdc    # Agent: UI component patterns (scoped to apps/web/**)
    ├── mobile-patterns.mdc      # Agent: mobile patterns (scoped to apps/mobile/**)
    ├── testing-patterns.mdc     # Agent: test writing patterns (scoped to **/*.test.*)
    ├── database-patterns.mdc    # Agent: migration and query patterns (scoped to **/db/**)
    └── security.mdc             # Always-on: hard security constraints
```

---

## Rule File Format

Each `.mdc` file follows this structure:

```markdown
---
description: [What this rule covers — shown in Cursor UI]
globs: [File glob pattern, e.g. apps/api/**/*.ts — omit for always-on]
alwaysApply: [true for always-on, false for glob-scoped or agent-requested]
---

[Rule content in plain markdown]
```

---

## Generated Rule Templates

Copy these, fill in your project values from `core/UNIVERSAL_SPEC.md`.

### `project-context.mdc`

```markdown
---
description: Project identity, tech stack, and repository layout
alwaysApply: true
---

# Project: [PROJECT_NAME]

[Elevator pitch from UNIVERSAL_SPEC Section 1]

## Stack
- Frontend: [framework + version]
- Backend: [language + framework + version]
- Database: [database + version]
- Cache: [cache layer]
- Auth: [auth provider]
- Cloud: [cloud provider]

## Repository layout
- `apps/web/` — [web app]
- `apps/api/` — [API service]
- `apps/mobile/` — [mobile app, if applicable]
- `packages/types/` — shared TypeScript types
- `packages/utils/` — shared utilities
- `docs/` — PRD, EDD, schema, living docs

## Key docs
- PRD: `docs/PRD.md` — feature priority and scope
- EDD: `docs/EDD.md` — technical specification
- Schema: `docs/schema.sql` — database structure
- Architecture: `docs/architecture.md` — service overview
```

### `code-conventions.mdc`

```markdown
---
description: Code style, naming, and structural conventions
alwaysApply: true
---

# Code Conventions

## TypeScript
- Strict mode always on — no implicit any, no ts-ignore without explanation
- Absolute imports via tsconfig paths — never use ../../../
- Zod schemas at all external boundaries (API input, env vars, external APIs)
- No console.log — use the structured logger at `packages/logger`

## Naming
- Files: kebab-case (`user-profile.ts`, `auth-middleware.ts`)
- React components: PascalCase (`UserProfile.tsx`)
- Variables/functions: camelCase
- Constants: SCREAMING_SNAKE_CASE
- Database tables: snake_case
- Environment variables: SCREAMING_SNAKE_CASE

## Module structure (backend)
```
feature/
├── feature.router.ts    # Route definitions only
├── feature.controller.ts # Input parsing, response shaping
├── feature.service.ts   # Business logic
├── feature.types.ts     # Types and Zod schemas
└── feature.test.ts      # Tests
```

## Error handling
- Always use typed errors (never `throw new Error('string')` alone)
- All async functions must handle rejections — no unhandled promises
- Return typed Result<T, E> from service functions, never throw across service boundaries
```

### `security.mdc`

```markdown
---
description: Security hard constraints — applied to every code generation
alwaysApply: true
---

# Security Constraints

These rules are non-negotiable. Never generate code that violates them.

- NEVER include secrets, API keys, or credentials in code
- NEVER build raw SQL strings — always use parameterised queries or ORM
- NEVER log sensitive fields: passwords, tokens, PII, card numbers
- NEVER expose internal error details in API responses — log internally, return generic message
- ALWAYS validate and sanitise all input at the API boundary before use
- ALWAYS check authentication and authorisation before serving data
- ALWAYS use HTTPS-only cookies with SameSite=Strict for session tokens
- ALWAYS rate-limit authentication endpoints
- NEVER store passwords in plaintext — use bcrypt with cost factor ≥ 12
```

### `api-patterns.mdc`

```markdown
---
description: Backend API implementation patterns
globs: apps/api/**/*.ts
alwaysApply: false
---

# API Implementation Patterns

## Endpoint implementation order
1. Define Zod schema for request body/params/query
2. Write controller (parse + validate input, call service, shape response)
3. Write service (business logic, database calls, cache checks)
4. Register route in the router
5. Write unit test for service, integration test for endpoint
6. Update `docs/architecture.md` endpoint map

## Standard response envelope
```typescript
// Success
{ success: true, data: T, meta?: { page, total, ... } }

// Error
{ success: false, error: { code: string, message: string, details?: unknown } }
```

## Error codes
- `VALIDATION_ERROR` — bad input (400)
- `UNAUTHORISED` — not authenticated (401)
- `FORBIDDEN` — authenticated but not allowed (403)
- `NOT_FOUND` — resource doesn't exist (404)
- `CONFLICT` — duplicate or state conflict (409)
- `INTERNAL_ERROR` — unexpected server error (500)

## Cache pattern
Check cache → return if hit → query DB → write to cache → return
Always set a TTL. Never cache user-specific data in a shared key.
```

### `frontend-patterns.mdc`

```markdown
---
description: Frontend UI component patterns
globs: apps/web/**/*.tsx
alwaysApply: false
---

# Frontend Patterns

## Component structure
- One component per file
- Co-locate styles, tests, and stories in the same directory
- Use server components by default; add 'use client' only when needed

## State management
- Server state: React Query / TanStack Query (never fetch in useEffect)
- UI state: useState / useReducer (local to component)
- Global UI state: Zustand (only for cross-cutting UI state like modals, toasts)
- Never put server data in global state

## Loading states
- Use skeleton loaders, never spinners for content areas
- Streaming with Suspense for server components
- Optimistic updates for mutations that are unlikely to fail

## Design system
- [Colour system from UNIVERSAL_SPEC Section 8]
- [Typography from UNIVERSAL_SPEC Section 8]
- Spacing: always use the 4px scale — never arbitrary pixel values
- All interactive elements must have focus styles
```

### `testing-patterns.mdc`

```markdown
---
description: Test writing patterns and coverage requirements
globs: **/*.test.*
alwaysApply: false
---

# Testing Patterns

## Coverage targets
- API services: 80% minimum
- Utility functions: 90% minimum
- React components: 60% minimum (focus on logic, not renders)

## Unit test structure (Arrange / Act / Assert)
```typescript
describe('featureName', () => {
  describe('when [condition]', () => {
    it('should [expected outcome]', async () => {
      // Arrange
      const input = { ... };
      // Act
      const result = await featureService.doThing(input);
      // Assert
      expect(result).toEqual({ ... });
    });
  });
});
```

## What to always test
- Happy path
- Invalid input / validation rejection
- Not found case
- Unauthorised access
- Any edge case documented in `docs/risks.pdf`

## Mocking
- Mock external services (Stripe, email, SMS) — never hit real APIs in tests
- Use the test fixtures defined in `.claude/plugins/testing-qa.md`
- Database: use a test database with known seed data, not mocks
```

---

## `.cursorrules` (Legacy Fallback)

If you're on an older Cursor version, use this single-file format:

```
# [PROJECT_NAME] — Cursor Rules

## Project
[Elevator pitch]. Stack: [frontend] + [backend] + [database].

## Hard rules
- TypeScript strict mode always
- Absolute imports only (no ../)
- Zod schemas at all API boundaries
- Never console.log — use structured logger
- Never raw SQL — use parameterised queries or ORM
- Never commit secrets
- Validate all external input before use

## Module structure
Routes → Controller (parse/validate) → Service (business logic) → Database
Response format: { success: boolean, data?: T, error?: { code, message } }

## Naming
Files: kebab-case | Components: PascalCase | Variables: camelCase | Constants: SCREAMING_SNAKE

## Before finishing any task
- Write or update tests
- Update docs/architecture.md if endpoints changed
- Update docs/projectstatus.md task status
```

---

## Cursor-Specific Tips for Genesis Projects

**Use `@docs` references in chat:**
```
@docs/EDD.md implement the /users/:id endpoint per the spec in Section 4.2
```

**Use `@codebase` for cross-file understanding:**
```
@codebase add a new endpoint following the same pattern as the existing /auth/login route
```

**Scope agent tasks to services:**
Open only the relevant `apps/api/` or `apps/web/` folder in a Cursor workspace tab to keep context focused.

**Composer for multi-file features:**
Use Cursor Composer when implementing a full feature that touches routes, services, tests, and docs together.
