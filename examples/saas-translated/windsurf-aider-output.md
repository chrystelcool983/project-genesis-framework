# Windsurf + Aider Translation — PayFlow SaaS
# Generated from: CLAUDE.md
# Save each file block to its indicated path

---
# WINDSURF OUTPUT
---

## File: `.windsurf/rules/01-project-context.md`

```markdown
# PayFlow — Project Context

PayFlow is a B2B invoicing and payment automation platform for African SMEs. It enables business owners to create invoices, collect payments via mobile money (M-Pesa, Airtel, MTN MoMo) and card (Visa/Mastercard), and track receivables — from a web dashboard and mobile app. Kenya-first, KDPA 2019 compliant.

## Tech Stack
| Layer | Technology | Version |
|-------|-----------|---------|
| Web | Next.js (App Router) | 14.x |
| Mobile | React Native + Expo | 0.74 / SDK 51 |
| API | Node.js + Fastify + TypeScript | 20.x / 4.x / 5.x |
| ORM | Prisma | 5.x |
| Database | PostgreSQL | 16 |
| Cache/Queue | Redis + BullMQ | 7.x / 5.x |
| Validation | Zod | 3.x |
| Cloud | AWS ECS Fargate | ap-southeast-1 |

## Repository Layout
```
apps/
  web/        Next.js 14 dashboard (port 3000)
  api/        Core API: invoices, auth, business logic (port 4000)
  payments/   Payment processing: M-Pesa, card (port 4001)
  mobile/     React Native iOS + Android (Expo SDK 51)
packages/
  types/      Shared TypeScript types
  utils/      Phone formatting, currency helpers
  logger/     Structured JSON logger — use this, never console.log
  config/     Shared Zod config schemas
docs/
  PRD.md      Product requirements and feature priority
  EDD.md      Engineering design — endpoint specs (definitive)
  schema.sql  Full database schema
  architecture.md  Living service map (update after changes)
```

## Key Documents — Read Before Implementing
| Document | Path | Read when... |
|----------|------|-------------|
| EDD | `docs/EDD.md` | Implementing any endpoint or system component |
| PRD | `docs/PRD.md` | Checking feature priority or acceptance criteria |
| Schema | `docs/schema.sql` | Adding tables, columns, or queries |
| Architecture | `docs/architecture.md` | Checking service status or endpoints |
| Env Config | `.env.example` | Adding or referencing environment variables |

Rule: always check the EDD before implementing. Never implement features not in the PRD without a product decision.
```

---

## File: `.windsurf/rules/02-code-conventions.md`

```markdown
# Code Conventions

## TypeScript Rules
- Strict mode: `noImplicitAny`, `strictNullChecks`, `noUncheckedIndexedAccess`
- No `ts-ignore` without an explanatory comment on the same line
- Absolute imports via tsconfig paths — never `../../relative/paths`
- Zod schemas at every external boundary (API input, env vars, external API responses)
- `unknown` over `any` — always narrow before use
- Explicit return types on all exported functions

## Naming Conventions
| Thing | Convention | Example |
|-------|-----------|---------|
| Files | kebab-case | `invoice-service.ts` |
| Components | PascalCase | `InvoiceCard.tsx` |
| Functions/variables | camelCase | `createInvoice` |
| Constants | SCREAMING_SNAKE | `MAX_LINE_ITEMS` |
| DB tables | snake_case | `invoice_line_items` |
| Env vars | SCREAMING_SNAKE | `MPESA_CONSUMER_KEY` |

## Backend Module Structure
Every backend feature follows this layout:
```
feature/
├── feature.router.ts      # Route definitions + middleware registration
├── feature.controller.ts  # Input parsing, response shaping — no business logic
├── feature.service.ts     # Business logic, DB calls, cache operations
├── feature.types.ts       # Zod schemas + derived TypeScript types
└── feature.test.ts        # Unit tests (service) + integration tests (routes)
```

## Forbidden Patterns
- `console.log` → use `packages/logger` (structured JSON logging)
- Raw SQL or string-interpolated queries → Prisma ORM only
- `fetch` in `useEffect` → use TanStack Query
- Throwing errors across service boundaries → return `Result<T, AppError>`
- Unhandled promise rejections → always `await` or `.catch()`

## API Response Envelope
```typescript
// All endpoints return this shape:
type Success<T> = { success: true; data: T; meta?: { page: number; total: number } }
type Failure = { success: false; error: { code: ErrorCode; message: string } }
```

## Error Codes
`VALIDATION_ERROR` (400) · `UNAUTHORISED` (401) · `FORBIDDEN` (403) · `NOT_FOUND` (404) · `CONFLICT` (409) · `PAYMENT_FAILED` (402) · `INTERNAL_ERROR` (500)

## Git Conventions
- Branches: `feature/TICKET-description`, `fix/TICKET-description`, `chore/description`
- Commits: Conventional Commits — `type(scope): subject`
  - Types: `feat` | `fix` | `chore` | `docs` | `test` | `refactor` | `perf`
  - Scope: service or feature (e.g. `invoices`, `payments`, `web`, `mobile`)
- PRs: 1 approval + CI passing. Must reference issue. Update living docs if behaviour changed.

## After Every Task
1. Mark task ✅ in `docs/projectstatus.md`
2. Add entry to `docs/CHANGELOG.md` under [Unreleased]
3. Update `docs/architecture.md` if endpoints or schema changed
4. Commit docs separately: `docs: sync living docs`
```

---

## File: `.windsurf/rules/03-security.md`

```markdown
# Security Rules — Always Enforced

Cascade must never generate code that violates these constraints.

## Authentication & Authorisation
- Auth required on ALL routes except `/health`, `/auth/login`, `/auth/register`
- Use the auth middleware — never inline token checks in controllers
- Derive `tenant_id` from the verified JWT — never trust client-supplied `tenant_id`
- Verify both authentication (who are you?) and authorisation (are you allowed?) separately

## Input Validation
- Validate ALL external input with Zod at the API boundary before any processing
- This includes: request body, URL params, query strings, and headers
- Reject and return `VALIDATION_ERROR` before the input ever reaches the service layer

## Database
- Prisma ORM only — no raw SQL, no `$queryRawUnsafe`, no string interpolation in queries
- Tenant isolation: every query must filter by `tenant_id`

## Secrets & Credentials
- No API keys, M-Pesa credentials, Stripe keys, or tokens in source code
- All secrets via environment variables (see `.env.example` for the full list)
- Never commit `.env` files — `.env.example` with placeholder values only

## Logging — Never Log These Fields
- Passwords and PINs (including M-Pesa transaction PINs)
- Card numbers, CVVs, expiry dates
- Access tokens, refresh tokens, API keys
- National IDs, phone numbers in payment context

## Transport & Cookies
- HTTPS only in staging and production
- Session cookies: `HttpOnly` + `Secure` + `SameSite=Strict`
- CORS: explicit origins only — never `*` in non-local environments
- Rate limiting: 20 req/min unauthenticated, 200 req/min authenticated

## KDPA 2019 Compliance
- User data must not leave Kenya without explicit user consent
- Honour data deletion requests within 30 days
- All support staff data access must create an audit log entry
```

---

## File: `.windsurf/rules/04-frontend-patterns.md`

```markdown
# Frontend Patterns

## Next.js App Router
- Server components by default
- `'use client'` only for: event handlers, browser APIs, third-party hooks, real-time
- Keep client components as leaf nodes — push data fetching up to server components

## State Management
- Server state: TanStack Query (`useQuery`, `useMutation`) — never `fetch` in `useEffect`
- Local UI state: `useState` / `useReducer`
- Global UI state (modals, toasts, sidebars): Zustand (for cross-component UI state only)
- Never put server data in global Zustand state

## PayFlow Design System

### Colours — always use CSS variables
```css
--color-primary: #1B4FD8;        /* CTAs, active links */
--color-primary-dark: #1339A8;   /* Hover state */
--color-secondary: #F59E0B;      /* Highlights, status badges */
--color-text-primary: #111827;
--color-text-muted: #6B7280;
--color-surface-muted: #F9FAFB;
--color-border: #E5E7EB;
--color-success: #059669;
--color-error: #DC2626;
--color-warning: #D97706;
/* Invoice status colours */
--color-invoice-draft: #6B7280;
--color-invoice-sent: #1B4FD8;
--color-invoice-overdue: #DC2626;
--color-invoice-paid: #059669;
```

### Typography
- Headings/display: Inter 700 or 600
- Body text: Inter 400 or 500
- Financial amounts + invoice IDs: JetBrains Mono 400 — always right-aligned
- Always show currency code with amounts: "KES 10,000" not "10,000"

### Spacing
4px base unit. Only use: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64px.
Never arbitrary pixel values.

### Component Patterns
- **Invoice card:** white bg, 1px `--color-border`, 8px border-radius, status badge top-right corner
- **Loading:** skeleton loaders — not spinners for any content area
- **Empty states:** illustration + headline + CTA button — never just "No data" or "Nothing here"
- **Error states:** red border on field, error icon on left, inline message below — not toast

### Accessibility
- WCAG 2.1 AA minimum
- Visible focus rings on all interactive elements
- Colour alone is never the sole status indicator — always pair with icon or text
```

---

## File: `.windsurf/rules/05-api-patterns.md`

```markdown
# API Implementation Patterns

## Endpoint Implementation Order
Always follow this sequence — don't skip steps:
1. Define Zod schema in `feature.types.ts`
2. Implement service function in `feature.service.ts`
3. Write controller in `feature.controller.ts`
4. Register route in `feature.router.ts`
5. Write tests (see testing-patterns.md for required scenarios)
6. Update `docs/architecture.md` endpoint map table

## Cache Pattern
```typescript
// Always check cache first
const cacheKey = `invoice:${tenantId}:${invoiceId}`;
const cached = await redis.get(cacheKey);
if (cached) return JSON.parse(cached);

const result = await prisma.invoice.findUnique({
  where: { id: invoiceId, tenantId },
});
if (result) {
  await redis.setex(cacheKey, 300, JSON.stringify(result)); // 5 min TTL
}
return result;
```

## Tenant Isolation Pattern
```typescript
// Every DB query must include tenantId from the verified JWT
const invoice = await prisma.invoice.findUnique({
  where: { id: invoiceId, tenantId: ctx.user.tenantId }, // tenantId from JWT
});
```

## Payment Job Pattern
Payment initiations are always async:
```typescript
const job = await paymentQueue.add('initiate-mpesa', { invoiceId, phone, amount });
return { success: true, data: { jobId: job.id, status: 'QUEUED' } };
// Client polls GET /payments/:jobId/status
```

## Performance Targets
| Endpoint | Target p95 | Hard limit |
|----------|-----------|-----------|
| GET /invoices | <100ms | 500ms |
| POST /invoices | <200ms | 1s |
| POST /payments/initiate | <300ms | 2s |
| Dashboard initial load | <2s LCP | 4s |
```

---

## File: `.windsurf/rules/06-testing-patterns.md`

```markdown
# Testing Patterns

## Coverage Targets
- Payment services: 90% minimum (financial logic — highest bar)
- API services: 80% minimum
- Utility functions: 90% minimum
- React components: 60% minimum (focus on logic, skip pure-render snapshots)

## Test Structure
```typescript
describe('invoiceService', () => {
  describe('createInvoice', () => {
    describe('when called with valid input', () => {
      it('should create invoice with DRAFT status', async () => {
        // Arrange
        const input = { tenantId: TEST_TENANT, ...validInvoiceInput };
        // Act
        const result = await invoiceService.createInvoice(ctx, input);
        // Assert
        expect(result.success).toBe(true);
        expect(result.data.status).toBe('DRAFT');
      });
    });
    describe('when line items exceed the limit', () => {
      it('should return VALIDATION_ERROR', async () => { ... });
    });
  });
});
```

## Required Scenarios Per Endpoint
1. ✅ Happy path — valid input, expected output
2. ❌ Each validation failure case (test each Zod rule)
3. 🔒 Unauthenticated request → 401
4. 🚫 Authenticated but wrong tenant → 403
5. 🔍 Not found → 404
6. 💳 Payment-specific: M-Pesa timeout, gateway unavailable → graceful failure

## Test Fixtures — Always Use These
```typescript
export const TEST_TENANT = 'test-tenant-001';
export const TEST_USER = { email: 'testuser@payflow.co.ke', tenantId: TEST_TENANT };
export const TEST_INVOICE_ID = 'INV-TEST-001';
export const TEST_MPESA_NUMBER = '254712345678'; // M-Pesa sandbox
export const TEST_CARD = '4111111111111111'; // Stripe test card
```

## Rules
- Mock all external services (M-Pesa API, Stripe, SendGrid) — never call real APIs
- Integration tests use the test database with seeded data — never mock the DB
- Reset test DB state between suites — never share mutable state
```

---

## File: `.windsurf/rules/07-living-docs.md`

```markdown
# Living Documentation Protocol

These three documents must stay in sync with the code. Updating them is part of every task.

## Update Triggers

| Event | Document(s) to update |
|-------|----------------------|
| New endpoint added | `docs/architecture.md` — add row to endpoint map |
| Endpoint behaviour changed | `docs/architecture.md` + `docs/EDD.md` |
| New DB table or column added | `docs/architecture.md` — update schema section |
| Migration file added | `docs/architecture.md` — update migration log |
| Feature shipped to production | `docs/CHANGELOG.md` → Added |
| Bug fixed | `docs/CHANGELOG.md` → Fixed |
| Security fix | `docs/CHANGELOG.md` → Security |
| Sprint task completed | `docs/projectstatus.md` → mark ✅ |
| Sprint started | `docs/projectstatus.md` → update sprint header + dates |

## Post-Task Protocol
Run this after every completed task before committing:

```
1. Open docs/projectstatus.md
   → Find the current sprint section
   → Mark the completed task ✅
   → Update progress bar if milestone reached

2. Open docs/CHANGELOG.md
   → Add entry under [Unreleased] with correct category:
     - Added / Changed / Fixed / Security / Infrastructure / Deprecated
   → Format: `- [scope]: description`

3. Open docs/architecture.md
   → If new endpoint: add row to endpoint map table
   → If schema change: update the schema section
   → If new external service: add to integrations table

4. Commit docs separately from code:
   git add docs/
   git commit -m "docs: sync living docs after [feature/fix name]"
```

## CHANGELOG Entry Format
```
## [Unreleased]
### Added
- [invoices]: POST /invoices/bulk endpoint for batch invoice creation
- [web]: Bulk invoice creation UI with CSV upload

### Fixed
- [payments]: M-Pesa callback now correctly handles partial payment amounts
```
```

---

## File: `.windsurfrules`

```
Project: PayFlow — B2B invoicing + M-Pesa/card payments for Kenyan SMEs
Stack: Next.js 14 · Fastify 4 · TypeScript 5 · Prisma/Postgres 16 · Redis 7 · React Native/Expo · AWS

Repo: apps/web (:3000) · apps/api (:4000) · apps/payments (:4001) · apps/mobile
Key docs: docs/EDD.md (spec) · docs/PRD.md (priority) · docs/schema.sql · docs/architecture.md

Hard rules — never violate:
- TypeScript strict, absolute imports, Zod at boundaries
- Prisma only — no raw SQL
- packages/logger only — no console.log
- No secrets in code
- Auth + tenant check before every data operation
- Never trust client-supplied tenant_id — derive from JWT
- Never log: passwords, PINs, card numbers, tokens, national IDs
- KDPA 2019: user data stays in Kenya, 30-day deletion SLA

Naming: files=kebab | components=PascalCase | vars=camelCase | constants=SCREAMING_SNAKE | db=snake_case
Module: router.ts → controller.ts → service.ts + types.ts + test.ts
Response: { success: true, data: T } | { success: false, error: { code, message } }

After every task: ✅ projectstatus.md → CHANGELOG.md [Unreleased] → architecture.md if changed
```

---
# AIDER OUTPUT
---

## File: `.aider.conf.yml`

```yaml
# .aider.conf.yml — PayFlow
# Aider configuration generated from CLAUDE.md

##########
# Model
##########
model: claude-sonnet-4-5
# Upgrade to claude-opus-4-5 for complex multi-service features or hard debugging sessions

##########
# Always-loaded context
##########
read:
  - CONVENTIONS.md               # Project conventions — loaded every session

# Load these for specific task types (use /read in session):
# /read docs/EDD.md              → before implementing any endpoint
# /read docs/schema.sql          → before adding tables or queries
# /read docs/architecture.md    → before any service-level changes

##########
# Git behaviour
##########
auto-commits: false
dirty-commits: true
commit-prompt: |
  Generate a conventional commit message for these changes.
  
  Format: type(scope): subject
  
  Types: feat | fix | chore | docs | test | refactor | perf
  Scopes: invoices | payments | auth | web | mobile | api | db | infra
  Subject: imperative mood, max 72 chars, no trailing period
  
  If this is a non-trivial change, add a body explaining WHY (not what — the diff shows what).
  
  Examples:
    feat(invoices): add bulk invoice creation endpoint
    fix(payments): handle M-Pesa partial payment callbacks correctly
    chore(deps): upgrade Prisma to 5.10.2
    docs: sync living docs after bulk invoice feature

##########
# Code quality
##########
lint-cmd: pnpm lint
test-cmd: pnpm test
auto-test: false  # Set true for TDD sessions

##########
# Editor integration
##########
pretty: true
stream: true
show-diffs: true
yes-always: false

##########
# Context budget
##########
map-tokens: 2048   # Increase to 4096 for large-feature sessions
```

---

## File: `CONVENTIONS.md`

```markdown
# PayFlow — Development Conventions

> This file is loaded into every Aider session. It is the complete reference for how 
> this codebase is built. Read it before starting any task.

---

## 1. Project Identity

**PayFlow** is a B2B invoicing and payment automation platform for African SMEs.
Web dashboard (Next.js 14) + mobile app (React Native/Expo) + two Fastify API services.
Supports M-Pesa, Airtel Money, MTN MoMo, Visa/Mastercard. Kenya-first, KDPA 2019 compliant.

**Stack:** TypeScript 5 · Next.js 14 · Fastify 4 · Prisma 5 · PostgreSQL 16 · Redis 7 · BullMQ 5 · React Native 0.74 · Expo SDK 51 · AWS (ap-southeast-1)

---

## 2. Repository Layout

```
apps/
  web/        Next.js dashboard (port 3000)
  api/        Core API: invoices, auth, tenants (port 4000)
  payments/   Payment processing: M-Pesa, card, webhooks (port 4001)
  mobile/     React Native iOS + Android
packages/
  types/      Shared TypeScript types
  utils/      Phone formatting, currency helpers, validators
  logger/     Structured JSON logger — USE THIS, never console.log
  config/     Zod config schemas
docs/
  EDD.md      Engineering spec with endpoint definitions — read before implementing
  PRD.md      Product requirements and feature priority — check before scoping
  schema.sql  Full database schema
  architecture.md  Living service map — update after changes
  CHANGELOG.md     Living changelog
  projectstatus.md Sprint tracker
```

**Key `/read` commands for Aider sessions:**
```bash
/read docs/EDD.md        # Before implementing any endpoint
/read docs/schema.sql    # Before adding tables or running queries
/read docs/PRD.md        # Before scoping any feature work
```

---

## 3. Code Rules

### TypeScript
- Strict mode: `noImplicitAny`, `strictNullChecks`, `noUncheckedIndexedAccess`
- Absolute imports via tsconfig paths — never `../../relative/paths`
- Zod schemas at every external boundary (API input, env vars, external API responses)
- `unknown` over `any` — always narrow the type before use
- No `ts-ignore` without an explanatory comment on the same line

### Naming
| Thing | Convention | Example |
|-------|-----------|---------|
| Files | kebab-case | `invoice-service.ts` |
| Components | PascalCase | `InvoiceCard.tsx` |
| Functions + variables | camelCase | `createInvoice` |
| Constants | SCREAMING_SNAKE | `MAX_LINE_ITEMS` |
| DB tables + columns | snake_case | `invoice_line_items` |
| Environment variables | SCREAMING_SNAKE | `MPESA_CONSUMER_KEY` |

### Module structure (every backend feature)
```
feature/
├── feature.router.ts      # Route definitions + middleware registration
├── feature.controller.ts  # Input parsing, Zod validation, response shaping
├── feature.service.ts     # Business logic, DB calls, cache operations
├── feature.types.ts       # Zod schemas + derived TypeScript types
└── feature.test.ts        # Unit tests (service) + integration tests (routes)
```

### Forbidden patterns — Aider must never generate these
- `console.log/error/warn` → use `packages/logger`
- Raw SQL or `prisma.$queryRaw` with string interpolation → Prisma ORM methods only
- `fetch` inside `useEffect` → use TanStack Query
- `throw new Error(string)` across service boundaries → return `Result<T, AppError>`
- Unhandled promise rejections → always `await` or `.catch()`

### API response format
```typescript
// All endpoints return one of these shapes:
type ApiSuccess<T> = { success: true; data: T; meta?: { page: number; total: number } }
type ApiError = { success: false; error: { code: ErrorCode; message: string } }
```

### Error codes
`VALIDATION_ERROR` (400) · `UNAUTHORISED` (401) · `FORBIDDEN` (403) · `NOT_FOUND` (404) · `CONFLICT` (409) · `PAYMENT_FAILED` (402) · `INTERNAL_ERROR` (500)

---

## 4. Git Conventions

**Branches:** `feature/TICKET-description` · `fix/TICKET-description` · `chore/description`

**Commit format:** `type(scope): subject`
- Types: `feat` | `fix` | `chore` | `docs` | `test` | `refactor` | `perf`
- Scopes: `invoices` | `payments` | `auth` | `web` | `mobile` | `api` | `db`
- Subject: imperative mood, max 72 chars, no period

**PRs:** 1 approval + CI passing. Reference issue. Update living docs if behaviour changed.

The `.aider.conf.yml` `commit-prompt` handles commit message generation automatically.

---

## 5. Security Rules

These rules are non-negotiable — never violate them.

- **Auth first:** Auth required on all routes except `/health`, `/auth/login`, `/auth/register`
- **Tenant isolation:** All DB queries must filter by `tenant_id` from the verified JWT — never trust client-supplied `tenant_id`
- **Input validation:** Validate ALL external input with Zod at the API boundary before any processing
- **No raw SQL:** Prisma ORM only — no `$queryRaw` with string interpolation
- **No secrets in code:** All credentials via environment variables (`.env.example` is the reference)
- **Never log:** passwords, M-Pesa PINs, card numbers, CVVs, tokens, national IDs
- **Cookies:** `HttpOnly` + `Secure` + `SameSite=Strict`
- **Rate limits:** 20 req/min unauthenticated, 200 req/min authenticated
- **KDPA:** User data stays in Kenya; deletion requests honoured in 30 days; staff access audited

---

## 6. Testing

### Commands
```bash
pnpm test                    # All services
pnpm test apps/api           # API only
pnpm test apps/payments      # Payments only
pnpm test --coverage         # With coverage report
```

### Coverage targets
- Payment services: **90%** (financial logic — highest bar)
- API services: **80%**
- Utility functions: **90%**
- React components: **60%**

### Required test scenarios per endpoint
1. Happy path with valid input
2. Each Zod validation failure
3. Unauthenticated request (401)
4. Authenticated but wrong tenant (403)
5. Not found (404)
6. Payment services also: M-Pesa timeout + gateway unavailable

### Test fixtures — always use these
```typescript
const TEST_TENANT = 'test-tenant-001';
const TEST_USER_EMAIL = 'testuser@payflow.co.ke';
const TEST_INVOICE_ID = 'INV-TEST-001'; // KES 10,000
const MPESA_SANDBOX_NUMBER = '254712345678';
const STRIPE_TEST_CARD = '4111111111111111';
```

### Rules
- Mock all external services (M-Pesa, Stripe, email) — never call real APIs in tests
- Integration tests use the test DB with seed data — never mock Prisma in integration tests
- Never share mutable state between test suites

---

## 7. Local Development

```bash
# Initial setup
nvm use                    # Node 20 (see .nvmrc)
pnpm install
cp .env.example .env.local
docker compose up -d       # PostgreSQL + Redis
pnpm db:migrate
pnpm db:seed
pnpm dev                   # All services

# Verify
curl http://localhost:4000/health  # → {"status":"ok"}
curl http://localhost:4001/health  # → {"status":"ok"}
```

---

## 8. Common Task Patterns

### New endpoint
```bash
# 1. Add files to Aider
/add apps/api/src/[feature]/[feature].types.ts
/add apps/api/src/[feature]/[feature].service.ts
/add apps/api/src/[feature]/[feature].controller.ts
/add apps/api/src/[feature]/[feature].router.ts
/add apps/api/src/[feature]/[feature].test.ts

# 2. Load the spec
/read docs/EDD.md

# 3. Give the task
Implement the [endpoint name] endpoint per Section [X.Y] of the EDD.
Follow CONVENTIONS.md module structure. Write all required test scenarios.
```

### New DB table
```bash
/add docs/migrations/
/read docs/schema.sql
# Create migration file, update Prisma schema, run migrate, update architecture.md
```

### Fix a bug
```bash
/add [affected service files]
/read docs/EDD.md  # Check what the correct behaviour should be
# Reproduce → root cause → fix → add regression test
```

---

## 9. Living Documentation Protocol

**After every completed task:**

1. **`docs/projectstatus.md`** — mark the task ✅, update progress bar if milestone
2. **`docs/CHANGELOG.md`** — add entry under [Unreleased]:
   - `Added` for new features
   - `Fixed` for bug fixes
   - `Security` for security fixes
   - Format: `- [scope]: description`
3. **`docs/architecture.md`** — if endpoints or schema changed, update the relevant table

**Commit docs separately:**
```bash
/add docs/projectstatus.md docs/CHANGELOG.md docs/architecture.md
/commit
# Aider will generate: docs: sync living docs after [feature]
```
```
