# Cursor Translation — PayFlow SaaS
# Generated from: CLAUDE.md
# Save each file block to its indicated path

---

## File: `.cursor/rules/01-project-context.mdc`

```mdc
---
description: Project identity, tech stack, repo layout, and key reference docs
alwaysApply: true
---

# PayFlow — B2B invoicing and payment automation for African SMEs

Web dashboard (Next.js) + Mobile app (React Native) + Two API services.
Supports M-Pesa, Airtel Money, MTN MoMo, Visa/Mastercard. Kenya-first.

## Stack
- Web: Next.js 14 (App Router) · React 18 · TanStack Query 5 · Tailwind 3
- API: Node.js 20 · Fastify 4 · TypeScript 5 · Prisma 5 · Zod 3
- Payments: Node.js 20 · Fastify 4 · BullMQ 5 (job queue)
- Mobile: React Native 0.74 · Expo SDK 51 · MMKV 2
- DB: PostgreSQL 16 (Prisma ORM) · Redis 7 (cache + sessions + queues)
- Cloud: AWS (ap-southeast-1) · ECS Fargate · CloudFront

## Repository layout
- `apps/web/` — Next.js dashboard (port 3000)
- `apps/api/` — Core API: invoices, auth, business logic (port 4000)
- `apps/payments/` — Payment processing: M-Pesa, card (port 4001)
- `apps/mobile/` — React Native iOS + Android client
- `packages/types/` — Shared TypeScript types
- `packages/utils/` — Phone formatting, currency helpers
- `packages/logger/` — Structured JSON logger (use this, not console.log)
- `docs/` — PRD.md, EDD.md, schema.sql, architecture.md

## Key documents — read before implementing
- `docs/EDD.md` — endpoint specs, definitive source
- `docs/PRD.md` — feature priority and acceptance criteria
- `docs/schema.sql` — database schema
- `docs/architecture.md` — live endpoint map (update after changes)
```

---

## File: `.cursor/rules/02-code-conventions.mdc`

```mdc
---
description: Naming, imports, module structure, TypeScript rules, git conventions
alwaysApply: true
---

# Code Conventions

## TypeScript rules
- Strict mode: noImplicitAny, strictNullChecks, noUncheckedIndexedAccess
- Absolute imports via tsconfig paths only — never ../../relative/paths
- Zod schemas at every external boundary (API input, env vars, external APIs)
- unknown over any — narrow the type before use
- No ts-ignore without an explanatory comment on the same line

## Naming
| Thing | Convention | Example |
|-------|-----------|---------|
| Files | kebab-case | `invoice-service.ts` |
| Components | PascalCase | `InvoiceCard.tsx` |
| Functions/variables | camelCase | `createInvoice` |
| Constants | SCREAMING_SNAKE | `MAX_LINE_ITEMS` |
| DB tables | snake_case | `invoice_line_items` |
| Env vars | SCREAMING_SNAKE | `MPESA_CONSUMER_KEY` |

## Backend module structure
```
feature/
├── feature.router.ts      # Routes + middleware
├── feature.controller.ts  # Input parsing, response shaping
├── feature.service.ts     # Business logic, DB/cache
├── feature.types.ts       # Zod schemas + TypeScript types
└── feature.test.ts        # Tests
```

## Forbidden patterns
- console.log → use packages/logger
- Raw SQL → Prisma ORM only
- Unhandled promises → always await or .catch()
- Throwing across service boundaries → return Result<T, AppError>
- fetch in useEffect → use TanStack Query

## API response format
```typescript
{ success: true, data: T, meta?: { page: number, total: number } }
{ success: false, error: { code: ErrorCode, message: string } }
```

## Git conventions
- Branches: feature/TICKET-desc, fix/TICKET-desc, chore/desc
- Commits: type(scope): subject — feat|fix|chore|docs|test|refactor|perf
- PRs: 1 approval + CI passing, reference issue, update living docs if behaviour changed

## After every task
1. Mark task ✅ in docs/projectstatus.md
2. Add entry to docs/CHANGELOG.md under [Unreleased]
3. Update docs/architecture.md if endpoints or schema changed
4. Commit docs: `docs: sync living docs`
```

---

## File: `.cursor/rules/03-frontend-patterns.mdc`

```mdc
---
description: Web UI component patterns, design system, state management
globs: apps/web/**/*.tsx
alwaysApply: false
---

# Frontend Patterns (PayFlow Design System)

## Colours — use CSS variables, never hardcode hex
```css
--color-primary: #1B4FD8        /* CTAs, links */
--color-secondary: #F59E0B      /* Highlights, badges */
--color-success: #059669
--color-error: #DC2626
--color-warning: #D97706
--color-invoice-draft: #6B7280
--color-invoice-sent: #1B4FD8
--color-invoice-overdue: #DC2626
--color-invoice-paid: #059669
```

## Typography
- Headings/display: Inter 700/600
- Body: Inter 400/500
- Amounts and IDs: JetBrains Mono 400 — always right-aligned, always show currency code (KES/USD)

## Spacing
- 4px base unit — only use: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64px
- Never arbitrary pixel values

## Component patterns
- Invoice card: white bg, 1px --color-border, 8px radius, status badge top-right
- Loading: skeleton loaders only — never spinners for content areas
- Empty state: illustration + headline + CTA — never just "No data"
- Error state: red border, error icon left, inline message below field — not toast

## State management
- Server state: TanStack Query (useQuery/useMutation) — never fetch in useEffect
- Local UI state: useState/useReducer
- Global UI state (modals, toasts): Zustand only

## Next.js App Router rules
- Server components by default — 'use client' only for event handlers, hooks, browser APIs
- Keep client components as leaf nodes

## Accessibility
- WCAG 2.1 AA minimum
- All interactive elements need visible focus rings
- Colour is never the only status indicator
```

---

## File: `.cursor/rules/04-api-patterns.mdc`

```mdc
---
description: Backend API implementation patterns, endpoint scaffold, performance constraints
globs: apps/api/**/*.ts,apps/payments/**/*.ts
alwaysApply: false
---

# API Implementation Patterns

## Endpoint implementation order (always follow this)
1. Zod schema in feature.types.ts
2. Service in feature.service.ts (cache → DB → cache)
3. Controller in feature.controller.ts
4. Route registration in feature.router.ts
5. Tests covering all required scenarios
6. Update docs/architecture.md endpoint map

## Cache pattern
```typescript
const cached = await redis.get(cacheKey);
if (cached) return JSON.parse(cached);
const result = await prisma.invoice.findUnique({ where: { id } });
await redis.setex(cacheKey, 300, JSON.stringify(result));
return result;
```

## Error codes
VALIDATION_ERROR (400) · UNAUTHORISED (401) · FORBIDDEN (403) · NOT_FOUND (404)
CONFLICT (409) · PAYMENT_FAILED (402) · INTERNAL_ERROR (500)

## Performance constraints
| Endpoint | Target p95 | Hard limit |
|----------|-----------|-----------|
| GET /invoices | <100ms | 500ms |
| POST /invoices | <200ms | 1s |
| POST /payments/initiate | <300ms | 2s |

## Tenant isolation
All queries must filter by tenant_id. Never return data across tenant boundaries.
Derive tenant_id from the verified JWT — never trust client-supplied tenant_id.

## Payment job pattern
Payment initiations go through BullMQ — never process synchronously.
Return job_id immediately. Poll status via GET /payments/:job_id/status.
```

---

## File: `.cursor/rules/05-security.mdc`

```mdc
---
description: Security hard constraints — always applied
alwaysApply: true
---

# Security Rules — Non-Negotiable

## Authentication
- Auth required on ALL routes except /health, /auth/login, /auth/register
- Use the auth middleware — never inline token checks
- Derive tenant_id from verified JWT — never trust client-supplied values

## Data handling
- Validate ALL external input with Zod at the API boundary before any processing
- Prisma ORM only — never raw SQL or string-interpolated queries
- Rate limit: 20 req/min unauthenticated, 200 req/min authenticated

## Secrets & credentials
- No API keys, M-Pesa credentials, or tokens in source code
- All secrets via environment variables (see .env.example)
- Never commit .env files — .env.example only

## Logging — never log these fields
- passwords, PINs (M-Pesa PIN), card numbers, CVVs
- tokens (access, refresh, API keys)
- national IDs, phone numbers in payment context

## Transport & cookies
- HTTPS only in staging/production
- Session cookies: HttpOnly + Secure + SameSite=Strict
- CORS: explicit origins only — never * in non-local environments

## KDPA 2019 compliance
- User data must not leave Kenya without explicit user consent
- Data deletion requests: honour within 30 days
- Support staff data access requires audit log entry
```

---

## File: `.cursor/rules/06-testing-patterns.mdc`

```mdc
---
description: Test writing standards, required scenarios, coverage targets
globs: **/*.test.*,**/*.spec.*
alwaysApply: false
---

# Testing Patterns

## Coverage targets
- API services: 80% minimum
- Payment services: 90% minimum (financial logic)
- Utility functions: 90% minimum
- React components: 60% minimum

## Test structure
```typescript
describe('invoiceService', () => {
  describe('createInvoice', () => {
    describe('when input is valid', () => {
      it('should create invoice and return it', async () => {
        // Arrange
        const input = buildCreateInvoiceInput();
        // Act
        const result = await invoiceService.createInvoice(input);
        // Assert
        expect(result.success).toBe(true);
        expect(result.data.status).toBe('DRAFT');
      });
    });
  });
});
```

## Required scenarios per endpoint
1. Happy path — valid input, expected output
2. Each validation failure case
3. Unauthenticated (401)
4. Wrong tenant access (403)
5. Not found (404)
6. Payment-specific: M-Pesa timeout, gateway unavailable

## Test fixtures — always use these
- Test user: testuser@payflow.co.ke / tenant_id: test-tenant-001
- Test invoice: INV-TEST-001, KES 10,000
- M-Pesa sandbox number: 254712345678
- Stripe test card: 4111111111111111

## Rules
- Mock all external services (M-Pesa, Stripe, email) — never hit real APIs
- Use test DB with seed data — never mock the database in integration tests
- Never share state between test suites
```

---

## File: `.cursorrules`

```
# PayFlow — Cursor Rules

## Project
B2B invoicing + payments for African SMEs. Next.js 14 web + React Native mobile + Fastify API.
Stack: TypeScript · Prisma/Postgres · Redis · BullMQ · Expo · AWS. Kenya-first (KDPA compliant).

## Repo
apps/web (Next.js, :3000) · apps/api (core API, :4000) · apps/payments (M-Pesa/card, :4001) · apps/mobile
packages/logger (use this, not console.log) · packages/utils · packages/types
docs/EDD.md (spec) · docs/PRD.md (priority) · docs/schema.sql · docs/architecture.md (update after changes)

## Hard rules — never violate
- TypeScript strict mode, absolute imports only (no ../)
- Zod schemas at all API boundaries
- Prisma ORM only — no raw SQL
- packages/logger only — no console.log
- No secrets in code
- Auth check before every data operation
- Validate all input before use
- Derive tenant_id from JWT — never trust client-supplied value
- Never log: passwords, PINs, card numbers, tokens, national IDs

## Naming
Files: kebab-case | Components: PascalCase | Functions: camelCase | Constants: SCREAMING_SNAKE | DB: snake_case

## Backend module structure
router.ts → controller.ts (parse/validate) → service.ts (logic/cache/db) + types.ts + test.ts

## API response
{ success: true, data: T } or { success: false, error: { code, message } }

## After every task
1. Mark ✅ in docs/projectstatus.md  2. Add to docs/CHANGELOG.md  3. Update docs/architecture.md if endpoints/schema changed
```
