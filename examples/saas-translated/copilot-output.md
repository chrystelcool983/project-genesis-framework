# Copilot Translation — PayFlow SaaS
# Generated from: CLAUDE.md
# Save each file block to its indicated path

---

## File: `.github/copilot-instructions.md`

```markdown
# PayFlow — Copilot Instructions

## Project
B2B invoicing and payment platform for Kenyan SMEs. Web dashboard (Next.js 14) + mobile app (React Native/Expo) + two Fastify API services. Supports M-Pesa, Airtel Money, MTN MoMo, Visa/Mastercard.

## Stack
Next.js 14 · Fastify 4 · TypeScript 5 · Prisma/PostgreSQL 16 · Redis 7 · BullMQ 5 · React Native 0.74 · Expo SDK 51 · AWS (ap-southeast-1)

## Repository
- `apps/web/` — dashboard (Next.js, port 3000)
- `apps/api/` — core API: invoices, auth (port 4000)
- `apps/payments/` — M-Pesa + card processing (port 4001)
- `apps/mobile/` — iOS/Android client
- `packages/logger/` — use this, never console.log
- `docs/EDD.md` — endpoint specs · `docs/PRD.md` — feature priority · `docs/schema.sql` · `docs/architecture.md`

## Code rules
- TypeScript strict mode — no implicit any, absolute imports only (no ../)
- Zod schemas at every API boundary
- Prisma ORM only — no raw SQL, no string-interpolated queries
- packages/logger only — never console.log/error/warn
- Return Result<T, AppError> from services — never throw across service boundaries

## Naming
Files: kebab-case | Components: PascalCase | Functions/vars: camelCase | Constants: SCREAMING_SNAKE | DB tables: snake_case

## Backend module order
types.ts (Zod) → service.ts (logic) → controller.ts (parse/respond) → router.ts → test.ts

## API response format
`{ success: true, data: T }` or `{ success: false, error: { code: string, message: string } }`

## Security — never violate
- No secrets in code — environment variables only
- Validate all input with Zod before processing
- Auth check on every protected route
- Derive tenant_id from JWT — never trust client-supplied
- Never log: passwords, PINs, card numbers, tokens, national IDs
- Rate limit: 20/min unauthenticated, 200/min authenticated

## Git
Branches: `feature/TICKET-desc` or `fix/TICKET-desc`. Commits: `type(scope): subject` (feat|fix|chore|docs|test). 1 approval + CI required for PRs.

## After every task
- Mark ✅ in docs/projectstatus.md
- Add entry to docs/CHANGELOG.md under [Unreleased]
- Update docs/architecture.md if any endpoint or schema changed
```

---

## File: `.github/instructions/typescript.instructions.md`

```markdown
---
applyTo: "**/*.ts,**/*.tsx"
---

Strict mode always: noImplicitAny, strictNullChecks, noUncheckedIndexedAccess.
Prefer const over let. Never use var.
Absolute imports via tsconfig paths only — no relative paths with ../
Use unknown over any — always narrow before use.
No ts-ignore without an explanatory comment on the same line.
Use discriminated unions for multi-shape state.
Explicit return types on all exported functions.
Zod for runtime validation at external boundaries; TypeScript for compile-time only.
```

---

## File: `.github/instructions/api.instructions.md`

```markdown
---
applyTo: "apps/api/**,apps/payments/**"
---

Implementation order: types.ts (Zod schema) → service.ts → controller.ts → router.ts → test.ts.
Every endpoint must: validate input (Zod), check auth + tenant, call service, return typed envelope.
Cache pattern: check Redis → DB on miss → write to Redis with TTL → return.
Tenant isolation: filter all DB queries by tenant_id from verified JWT — never client-supplied.
Payment jobs: always async via BullMQ — return job_id immediately, poll for status.
On completion: add endpoint to docs/architecture.md endpoint map table.
```

---

## File: `.github/instructions/frontend.instructions.md`

```markdown
---
applyTo: "apps/web/**/*.tsx"
---

Server components by default. 'use client' only for event handlers, hooks, browser APIs.
Server state via TanStack Query (useQuery/useMutation) — never fetch in useEffect.
Skeleton loaders for loading states — never spinners for content areas.
Empty states: illustration + headline + CTA — never "No data" alone.
Colours: use CSS variables (--color-primary, --color-invoice-paid, etc.) — never hardcode hex.
Amounts: JetBrains Mono, right-aligned, always include currency code (KES/USD).
Spacing: 4px scale only — use 4, 8, 12, 16, 20, 24, 32, 40, 48, 64px.
WCAG 2.1 AA: visible focus rings on all interactive elements, colour never sole indicator.
```

---

## File: `.github/instructions/testing.instructions.md`

```markdown
---
applyTo: "**/*.test.*,**/*.spec.*"
---

Structure: describe(feature) → describe('when condition') → it('should outcome').
Arrange / Act / Assert — blank line between each section.
Required for every endpoint: happy path, each validation failure, 401, 403 (wrong tenant), 404.
Payment endpoints also need: M-Pesa timeout case, gateway unavailable case.
Coverage targets: payment services 90%, API services 80%, utils 90%, components 60%.
Test fixtures: user=testuser@payflow.co.ke/test-tenant-001, invoice=INV-TEST-001 KES 10000.
Mock all external services (M-Pesa, Stripe, email) — never call real APIs in tests.
```

---

## File: `.github/copilot-setup-steps.yml`

```yaml
# .github/copilot-setup-steps.yml
# Codex cloud agent environment bootstrap — runs before every agent task

steps:
  - name: Setup Node.js
    run: |
      export NVM_DIR="$HOME/.nvm"
      [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
      nvm install 20
      nvm use 20
      node --version

  - name: Setup pnpm
    run: |
      npm install -g pnpm@9
      pnpm --version

  - name: Install dependencies
    run: pnpm install

  - name: Set up environment
    run: |
      cp .env.example .env.test
      echo "NODE_ENV=test" >> .env.test

  - name: Start infrastructure
    run: |
      docker compose up -d postgres redis
      sleep 5  # Wait for services to be ready

  - name: Run migrations
    run: pnpm db:migrate

  - name: Seed test data
    run: pnpm db:seed:test

  - name: Verify setup
    run: |
      curl -f http://localhost:4000/health || exit 1
      curl -f http://localhost:4001/health || exit 1
      echo "All services healthy"
```
