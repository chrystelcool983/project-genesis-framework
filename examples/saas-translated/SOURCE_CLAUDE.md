# CLAUDE.md — PayFlow SaaS

> This is the AI development guide for PayFlow. Read this file before starting any task.
> Last updated: 2026-03

---

## Section 1: Project Overview

PayFlow is a B2B invoicing and payment automation platform for African SMEs. It lets business owners create invoices, collect payments via mobile money or card, and track receivables — all from a web dashboard and mobile app.

**Core goals:**
- Reduce invoice-to-payment cycle from 14 days to <3 days
- Support M-Pesa, Airtel Money, MTN MoMo, and Visa/Mastercard
- Work reliably on 3G connections with offline invoice creation
- Be compliant with Kenya's data protection regulations (KDPA 2019)

**Target users:** Small business owners in Kenya (20–100 employees), accountants managing client books, and enterprise accounts payable teams.

**Business context:** SaaS subscription ($29/mo Basic, $79/mo Pro, $199/mo Enterprise). Target: 500 paying customers by Month 6. Currently pre-launch.

---

## Section 2: Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│  Web Dashboard (Next.js)    Mobile App (React Native) │
└────────────────┬────────────────────┬────────────────┘
                 │  REST + WebSocket   │
         ┌───────▼────────────────────▼───────┐
         │           API Gateway              │
         └───────┬────────────┬───────────────┘
                 │            │
        ┌────────▼───┐  ┌─────▼──────┐
        │  Core API  │  │ Payment API│
        │ (Fastify)  │  │ (Fastify)  │
        └────────┬───┘  └─────┬──────┘
                 │            │
         ┌───────▼────────────▼───────┐
         │    PostgreSQL + Redis       │
         └────────────────────────────┘
```

**Monorepo structure:**
```
apps/
  web/          # Next.js 14 dashboard (port 3000)
  api/          # Core API service (port 4000)
  payments/     # Payment processing service (port 4001)
  mobile/       # React Native + Expo
packages/
  types/        # Shared TypeScript types
  utils/        # Shared utilities (phone formatting, currency)
  logger/       # Structured JSON logger
  config/       # Shared config schemas
docs/
  PRD.md        # Product requirements
  EDD.md        # Engineering design doc
  schema.sql    # Full database schema
  architecture.md # Living service map (update after changes)
  CHANGELOG.md  # Living changelog
  projectstatus.md # Living sprint tracker
.claude/
  plugins/      # Skill files
  commands/     # Slash commands
  agents/       # Subagents
  mcp/          # MCP config
```

**Service responsibilities:**
| Service | Runtime | Port | Responsibility |
|---------|---------|------|----------------|
| web | Next.js 14 | 3000 | Dashboard UI, SSR pages |
| api | Node.js 20 + Fastify | 4000 | Business logic, auth, invoices |
| payments | Node.js 20 + Fastify | 4001 | Payment gateway integrations |
| mobile | React Native (Expo SDK 51) | — | iOS + Android client |

---

## Section 3: Reference Documents

| Document | Path | Read it when... |
|----------|------|-----------------|
| PRD | `docs/PRD.md` | Checking feature priority, scope, or acceptance criteria |
| EDD | `docs/EDD.md` | Implementing any endpoint — spec is definitive |
| Schema | `docs/schema.sql` | Adding tables, columns, indexes, or queries |
| Architecture | `docs/architecture.md` | Checking service status or endpoint map |
| Project Status | `docs/projectstatus.md` | Checking what's done and what's next |
| Env Config | `.env.example` | Adding or referencing environment variables |

**Rule:** Check the EDD before implementing. Check the PRD for priority. Never implement features not in the PRD without a product decision.

---

## Section 4: Technology Stack

**Backend:**
| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js | 20.x LTS | Runtime |
| Fastify | 4.x | API framework |
| TypeScript | 5.x | Language |
| Prisma | 5.x | ORM |
| PostgreSQL | 16 | Primary database |
| Redis | 7.x | Cache + sessions + job queue |
| BullMQ | 5.x | Job queue (payment processing) |
| Zod | 3.x | Schema validation |

**Frontend:**
| Technology | Version | Purpose |
|------------|---------|---------|
| Next.js | 14.x | Web framework (App Router) |
| React | 18.x | UI library |
| TanStack Query | 5.x | Server state management |
| Zustand | 4.x | Client UI state |
| Tailwind CSS | 3.x | Styling |

**Mobile:**
| Technology | Version | Purpose |
|------------|---------|---------|
| React Native | 0.74 | Mobile framework |
| Expo | SDK 51 | Managed workflow + OTA |
| MMKV | 2.x | Fast local storage |

**Infrastructure:**
| Technology | Version | Purpose |
|------------|---------|---------|
| AWS | — | Cloud provider (ap-southeast-1) |
| ECS Fargate | — | Container orchestration |
| RDS Postgres | 16 | Managed database |
| ElastiCache | Redis 7 | Managed cache |
| CloudFront | — | CDN |
| GitHub Actions | — | CI/CD |

---

## Section 5: Design System & Style Guide

**Brand identity:** Professional, trustworthy, optimistic. "Banking for the rest of us."

**Colours:**
```css
--color-primary: #1B4FD8;      /* Electric blue — CTAs, links */
--color-primary-dark: #1339A8; /* Hover state */
--color-secondary: #F59E0B;    /* Amber — highlights, badges */
--color-surface: #FFFFFF;
--color-surface-muted: #F9FAFB;
--color-border: #E5E7EB;
--color-text-primary: #111827;
--color-text-muted: #6B7280;
--color-success: #059669;
--color-error: #DC2626;
--color-warning: #D97706;
/* Status colours for invoices */
--color-invoice-draft: #6B7280;
--color-invoice-sent: #1B4FD8;
--color-invoice-overdue: #DC2626;
--color-invoice-paid: #059669;
```

**Typography:**
- Display/heading: Inter (700, 600)
- Body: Inter (400, 500)
- Mono (amounts, IDs): JetBrains Mono (400)
- Scale: 12/14/16/18/20/24/30/36/48px

**Spacing:** 4px base unit — never arbitrary values. Use: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64px.

**Component patterns:**
- Invoice card: white background, 1px border `--color-border`, 8px radius, status badge top-right
- Amount display: JetBrains Mono, right-aligned, always show currency code (KES/USD)
- Loading state: skeleton loader — never spinner for content areas
- Empty state: illustration + headline + CTA button (never just "No data")
- Error state: red border, error icon left, message below field (inline, not toast)

**Accessibility:** WCAG 2.1 AA minimum. All interactive elements need focus rings. Colour is never the only indicator.

---

## Section 6: Code Conventions

**TypeScript:**
- Strict mode always — `noImplicitAny`, `strictNullChecks`, `noUncheckedIndexedAccess`
- Absolute imports via tsconfig paths — never `../../relative` paths
- Zod schemas at every external boundary (API input, env vars, external API responses)
- `unknown` over `any` — always narrow before use
- No `ts-ignore` without an explanatory comment

**Naming:**
| Thing | Convention | Example |
|-------|-----------|---------|
| Files | kebab-case | `invoice-service.ts` |
| Components | PascalCase | `InvoiceCard.tsx` |
| Functions | camelCase | `createInvoice` |
| Constants | SCREAMING_SNAKE | `MAX_LINE_ITEMS` |
| DB tables | snake_case | `invoice_line_items` |
| Env vars | SCREAMING_SNAKE | `MPESA_CONSUMER_KEY` |

**Module structure (backend feature):**
```
feature/
├── feature.router.ts      # Route definitions + middleware
├── feature.controller.ts  # Input parsing, response shaping
├── feature.service.ts     # Business logic, DB/cache calls
├── feature.types.ts       # Zod schemas + TypeScript types
└── feature.test.ts        # Unit + integration tests
```

**Hard rules:**
- `console.log` is forbidden — use `packages/logger`
- Raw SQL is forbidden — use Prisma ORM always
- Unhandled promise rejections are forbidden — always `await` or `.catch()`
- Throwing across service boundaries is forbidden — return `Result<T, AppError>`

---

## Section 7: Repository Etiquette

**Branches:** `feature/TICKET-description`, `fix/TICKET-description`, `chore/description`

**Commits:** Conventional Commits — `type(scope): subject`
- Types: `feat` | `fix` | `chore` | `docs` | `test` | `refactor` | `perf`
- Scope: service or feature name (e.g. `invoices`, `payments`, `web`, `mobile`)
- Subject: imperative, max 72 chars, no period

**PRs:** Require 1 approval + CI passing. Must reference issue. Must update living docs if behaviour changed.

---

## Section 8: Constraints & Policies

**Performance targets:**
| Endpoint | Target | Hard limit |
|----------|--------|-----------|
| GET /invoices | <100ms p95 | 500ms |
| POST /invoices | <200ms p95 | 1s |
| POST /payments/initiate | <300ms p95 | 2s |
| Dashboard load | <2s LCP | 4s |

**Security policies:**
- Auth required on all routes except `/health`, `/auth/login`, `/auth/register`
- Validate all input with Zod at the API boundary before any processing
- Never log: passwords, M-Pesa PINs, card numbers, tokens, national IDs
- Parameterised queries only — Prisma handles this, never drop to raw SQL
- HTTPS-only cookies: HttpOnly, Secure, SameSite=Strict
- Rate limit: 20 req/min unauthenticated, 200 req/min authenticated
- Secrets via environment variables only — never in source code

**Data/privacy (KDPA 2019):**
- User data must not leave Kenya unless user explicitly consents
- Data deletion requests must be honoured within 30 days
- Audit log required for all data access by support staff

---

## Section 9: Testing Instructions

**Commands:**
```bash
pnpm test              # All services
pnpm test apps/api     # API service only
pnpm test apps/payments # Payments service only
pnpm test --coverage   # With coverage report
```

**Coverage targets:**
- API services: 80% minimum
- Payment services: 90% minimum (higher — financial logic)
- Utility functions: 90% minimum
- React components: 60% minimum

**Required scenarios per endpoint:**
1. Happy path with valid input
2. Each validation failure
3. Unauthenticated request (401)
4. Authorised but wrong tenant (403)
5. Not found (404)
6. Payment-specific: M-Pesa timeout, gateway error

**Test fixtures (use these, don't invent data):**
- Test user: `testuser@payflow.co.ke` / tenant_id: `test-tenant-001`
- Test invoice: invoice_id `INV-TEST-001`, amount KES 10,000
- Test M-Pesa number: `254712345678` (sandbox)
- Test card: `4111111111111111` (Stripe test, any future date, any CVV)

---

## Section 10: Local Development Setup

**Prerequisites:**
- Node.js 20.x (use `.nvmrc`)
- pnpm 9.x
- Docker Desktop
- AWS CLI (configured with dev credentials)

**First-time setup:**
```bash
nvm use                    # Use correct Node version
pnpm install               # Install all dependencies
cp .env.example .env.local # Copy env template
docker compose up -d       # Start Postgres + Redis
pnpm db:migrate            # Run migrations
pnpm db:seed               # Seed dev data
pnpm dev                   # Start all services
```

**Verify it's working:**
```bash
curl http://localhost:4000/health   # → {"status":"ok"}
curl http://localhost:4001/health   # → {"status":"ok"}
open http://localhost:3000          # Dashboard
```

---

## Section 11: Deployment

| Environment | URL | Trigger | Data |
|-------------|-----|---------|------|
| Local | localhost | Manual | Seeded dev data |
| Staging | staging.payflow.co.ke | Merge to main | Anonymised prod data |
| Production | app.payflow.co.ke | Manual approval | Real data |

---

## Section 12: Common Tasks

**Adding a new endpoint:**
1. Define Zod schema in `feature.types.ts`
2. Implement service in `feature.service.ts` (check cache → DB → cache)
3. Write controller in `feature.controller.ts`
4. Register route in `feature.router.ts`
5. Write tests covering all required scenarios
6. Update `docs/architecture.md` endpoint map

**Adding a new DB table:**
1. Write migration in `docs/migrations/[timestamp]_[name].sql`
2. Update Prisma schema in `packages/config/prisma/schema.prisma`
3. Run `pnpm db:migrate`
4. Update `docs/architecture.md` schema section

**API response format:**
```typescript
// Success
{ success: true, data: T, meta?: { page: number, total: number } }
// Error
{ success: false, error: { code: ErrorCode, message: string } }
```

**Error codes:** `VALIDATION_ERROR` · `UNAUTHORISED` · `FORBIDDEN` · `NOT_FOUND` · `CONFLICT` · `PAYMENT_FAILED` · `INTERNAL_ERROR`

---

## Section 13: Living Documentation Rules

| Event | Update these docs |
|-------|------------------|
| New endpoint added | `docs/architecture.md` endpoint map |
| Endpoint behaviour changed | `docs/architecture.md` + `docs/EDD.md` |
| New DB table/column | `docs/architecture.md` schema section |
| Feature shipped | `docs/CHANGELOG.md` → Added |
| Bug fixed | `docs/CHANGELOG.md` → Fixed |
| Sprint task completed | `docs/projectstatus.md` → mark ✅ |

**Post-task protocol (run after every completed task):**
1. Mark task ✅ in `docs/projectstatus.md`
2. Add CHANGELOG entry under [Unreleased]
3. Update `docs/architecture.md` if endpoints/schema changed
4. Commit docs separately: `docs: sync living docs`

---

## Section 14: Plugins

| Plugin | Path | Read when... |
|--------|------|--------------|
| Frontend Design | `.claude/plugins/frontend-design.md` | Any UI task |
| Dev Streamline | `.claude/plugins/dev-streamline.md` | Any backend task |
| Domain Context | `.claude/plugins/domain-context.md` | Any M-Pesa / Kenya-specific task |
| Testing QA | `.claude/plugins/testing-qa.md` | Writing tests |
| Compound Engineering | `.claude/plugins/compound-engineering.md` | Multi-service features |

---

## Section 15: Slash Commands & Subagents

**Commands:** `/commit` · `/update-docs` · `/new-endpoint` · `/create-issue` · `/sprint-status` · `/deploy-check`

**Agents:** `api` (backend endpoints) · `frontend` (UI) · `security` (security review) · `changelog` (CHANGELOG.md) · `docs` (living docs sync) · `retro` (sprint retrospective) · `payment` (M-Pesa + card integration)

---

## Section 16: MCP Servers

| Server | Purpose |
|--------|---------|
| postgres | Direct DB access (local dev) |
| redis | Cache inspection |
| github | Issue + PR management |
| filesystem | File access |
| memory | Persistent context |
| fetch | API testing |
| playwright | E2E testing |
