# Windsurf Adapter — Project Genesis Framework

## How Windsurf Works

Windsurf (by Codeium) reads context from:
- **`.windsurf/rules/`** — Directory of named rule files (Windsurf 1.9+, the primary system)
- **`.windsurfrules`** — Legacy single-file format (still supported)

Windsurf's **Cascade** AI engine has a large context window and supports multi-file edits. Rules files are injected into Cascade's system context. Unlike Cursor, Windsurf also has a **Flows** system for agentic multi-step tasks.

---

## Setup Instructions

### Step 1: Create the rules directory

```bash
mkdir -p .windsurf/rules
```

### Step 2: Generate your rules files

```
Read core/UNIVERSAL_SPEC.md from this project.
Generate Windsurf rule files following adapters/windsurf/ADAPTER.md.
Output each file with its exact filename and path.
```

### Step 3: File placement

```
.windsurf/
└── rules/
    ├── 01-project-context.md     # Project identity, stack, layout
    ├── 02-code-conventions.md    # Naming, imports, error handling
    ├── 03-security.md            # Hard security constraints
    ├── 04-api-patterns.md        # Backend patterns
    ├── 05-frontend-patterns.md   # Web UI patterns
    ├── 06-mobile-patterns.md     # Mobile patterns (if applicable)
    ├── 07-testing.md             # Test writing standards
    └── 08-living-docs.md         # When and how to update docs
```

> **Numbering matters** — Windsurf applies rules in filename order. Put universal context first, specialised patterns later.

---

## Rule File Templates

### `01-project-context.md`

```markdown
# Project Context

**Project:** [PROJECT_NAME]
**What it does:** [Elevator pitch]
**Stack:** [Frontend] + [Backend] + [Database] + [Cache] + [Auth]
**Platforms:** [Web / Mobile iOS / Mobile Android / API]

## Repository structure
```
apps/web/      # [Web app — framework]
apps/api/      # [API service — language + framework]
apps/mobile/   # [Mobile app — if applicable]
packages/      # Shared types, utils, config
docs/          # PRD.md, EDD.md, schema.sql, architecture.md
```

## Key documents (read before implementing)
- **PRD** (`docs/PRD.md`) — what to build and why, feature priority
- **EDD** (`docs/EDD.md`) — how to build it, endpoint specs
- **Schema** (`docs/schema.sql`) — database structure
- **Architecture** (`docs/architecture.md`) — living service map, update after changes
```

### `02-code-conventions.md`

```markdown
# Code Conventions

## TypeScript
- Strict mode always — `noImplicitAny`, `strictNullChecks`, `noUncheckedIndexedAccess`
- No `ts-ignore` without an explanatory comment on the same line
- Absolute imports via tsconfig path aliases — never `../../..` relative paths
- `unknown` over `any` — narrow the type before use

## Naming
| Thing | Convention | Example |
|-------|-----------|---------|
| Files | kebab-case | `user-profile.ts` |
| Components | PascalCase | `UserProfile.tsx` |
| Variables | camelCase | `userProfile` |
| Constants | SCREAMING_SNAKE | `MAX_RETRY_COUNT` |
| DB tables | snake_case | `user_profiles` |
| Env vars | SCREAMING_SNAKE | `DATABASE_URL` |

## Module anatomy (backend feature)
```
feature/
├── feature.router.ts      # Route registration only
├── feature.controller.ts  # Input validation + response shaping
├── feature.service.ts     # Business logic + DB/cache calls
├── feature.types.ts       # Zod schemas + TypeScript types
└── feature.test.ts        # Unit + integration tests
```

## Forbidden patterns
- `console.log` → use `packages/logger`
- Raw string SQL → use parameterised queries or ORM
- `fetch` in `useEffect` → use TanStack Query
- Throwing across service boundaries → return `Result<T, E>`
```

### `03-security.md`

```markdown
# Security Rules — Non-Negotiable

Cascade must never generate code that violates these constraints.

## Authentication & authorisation
- Every protected route must verify the auth token before any data access
- Use middleware for auth — never inline token checks in controllers
- Never trust client-supplied user IDs — always derive from the verified token

## Data handling
- Validate and sanitise ALL external input (body, params, query, headers)
- Use Zod schemas at every API boundary
- Parameterised queries only — no string interpolation in SQL ever

## Secrets & credentials  
- No API keys, passwords, or tokens in source code
- All secrets via environment variables, loaded at runtime
- Never log: passwords, tokens, card numbers, SSNs, or any PII field

## Transport & cookies
- HTTPS-only in production
- Session cookies: HttpOnly, Secure, SameSite=Strict
- Set CORS origins explicitly — never `*` in production

## Passwords
- bcrypt with cost factor ≥ 12
- Never store or log plaintext passwords, even temporarily
```

### `04-api-patterns.md`

```markdown
# API Implementation Patterns

## Implementation order (always follow this sequence)
1. Define Zod schema in `feature.types.ts`
2. Write service function in `feature.service.ts`
3. Write controller in `feature.controller.ts`  
4. Register route in `feature.router.ts`
5. Write tests
6. Update `docs/architecture.md` endpoint map

## Response envelope
```typescript
// All API responses follow this shape
type ApiResponse<T> = 
  | { success: true; data: T; meta?: PaginationMeta }
  | { success: false; error: { code: ErrorCode; message: string } }
```

## Error codes
`VALIDATION_ERROR` (400) · `UNAUTHORISED` (401) · `FORBIDDEN` (403) · 
`NOT_FOUND` (404) · `CONFLICT` (409) · `INTERNAL_ERROR` (500)

## Cache pattern
```typescript
// Always check cache first
const cached = await redis.get(cacheKey);
if (cached) return JSON.parse(cached);

const result = await db.query(...);
await redis.setex(cacheKey, TTL_SECONDS, JSON.stringify(result));
return result;
```

## Pagination
All list endpoints use cursor-based pagination — never offset pagination for large tables.
```

### `05-frontend-patterns.md`

```markdown
# Frontend Patterns

## Component architecture
- Server components by default (Next.js App Router)
- `'use client'` only for: event handlers, hooks, browser APIs, real-time updates
- Keep client components as leaf nodes — push data fetching up to server components

## Data fetching
- Server components: `async/await` directly
- Client components: TanStack Query (`useQuery`, `useMutation`)
- Never `fetch` inside `useEffect`

## State
- Server state → TanStack Query
- Local UI state → `useState` / `useReducer`
- Cross-component UI state (modals, toasts, sidebars) → Zustand

## Loading patterns
- Content areas: skeleton loaders
- Button actions: inline spinner on the button
- Page transitions: Suspense boundaries with skeleton fallback

## Design system application
- Colours: use CSS variables from the design system — never hardcode hex values
- Spacing: 4px scale units — never arbitrary pixel values
- Typography: always use the defined text roles — never ad-hoc font sizes
- Focus styles: every interactive element must have a visible focus ring
```

### `07-testing.md`

```markdown
# Testing Standards

## Coverage targets
- Service functions: 80% minimum
- Utility functions: 90% minimum
- API endpoints (integration): every route needs at least one integration test
- React components: focus on logic-heavy components; skip pure-render snapshots

## Test structure
```typescript
describe('[feature name]', () => {
  describe('when [condition]', () => {
    it('should [outcome]', async () => {
      // Arrange — set up inputs and mocks
      // Act — call the function/endpoint
      // Assert — verify the outcome
    });
  });
});
```

## Required scenarios per endpoint
1. ✅ Happy path with valid input
2. ❌ Invalid input (each validation rule)
3. 🔒 Unauthenticated request (401)
4. 🚫 Authorised but forbidden (403 — if roles exist)
5. 🔍 Not found case (404)

## Rules
- Mock all external services — never call real APIs or send real emails in tests
- Use the shared test fixtures in `.claude/plugins/testing-qa.md`
- Test database: seeded fresh before each test suite, never shared state between tests
```

### `08-living-docs.md`

```markdown
# Living Documentation Rules

These documents must stay in sync with the code. Update them as part of every task.

## When to update each document

| Event | Update |
|-------|--------|
| New endpoint added | `docs/architecture.md` endpoint map |
| Endpoint behaviour changed | `docs/architecture.md` + `docs/EDD.md` |
| New DB table or column | `docs/architecture.md` schema section |
| Feature shipped | `docs/CHANGELOG.md` (Added section) |
| Bug fixed | `docs/CHANGELOG.md` (Fixed section) |
| Sprint task completed | `docs/projectstatus.md` task status |

## Update protocol
After every completed task:
1. Mark the task ✅ in `docs/projectstatus.md`
2. Add a CHANGELOG entry under `[Unreleased]`
3. Update `docs/architecture.md` if any service, endpoint, or schema changed
```

---

## `.windsurfrules` (Legacy Fallback)

```
Project: [PROJECT_NAME] — [elevator pitch]
Stack: [Frontend] + [Backend] + [Database]
Platforms: [Web / Mobile / API]

Code rules:
- TypeScript strict mode, absolute imports, Zod at boundaries
- Never console.log — use packages/logger
- Never raw SQL — parameterised queries or ORM only
- Never commit secrets
- Validate all input before use, check auth before data access

Module structure: router → controller → service → types + tests
API response: { success: boolean, data?: T, error?: { code, message } }

Naming: files=kebab-case, components=PascalCase, vars=camelCase, constants=SCREAMING_SNAKE

After each task: update docs/architecture.md, docs/CHANGELOG.md, docs/projectstatus.md
```

---

## Windsurf Cascade Tips for Genesis Projects

**Reference EDD sections in prompts:**
```
Following docs/EDD.md Section 6.3, implement the notifications service. 
Read the spec carefully before writing any code.
```

**Use Cascade's multi-file strength:**
Windsurf is excellent at implementing full features across multiple files in one go. Give it the full module scope:
```
Implement the complete user-profile feature: types, service, controller, router, and tests.
Follow the patterns in .windsurf/rules/04-api-patterns.md.
```

**Flows for Genesis setup tasks:**
Use Windsurf Flows to automate repetitive genesis tasks like:
- Running migrations after schema changes
- Generating boilerplate for new endpoints
- Updating living docs after feature completion
