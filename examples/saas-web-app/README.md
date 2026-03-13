# Example: SaaS Web App

**Stack:** Next.js 14 (App Router) · TypeScript · PostgreSQL · Redis · Vercel · Stripe

**Project type:** B2B SaaS with subscription billing and team workspaces

---

## Stack Decisions

| Layer | Choice | Reason |
|-------|--------|--------|
| Frontend | Next.js 14 App Router | SSR for SEO, RSC for performance, built-in API routes |
| Database | PostgreSQL (Supabase) | Relational data, row-level security, hosted |
| Cache | Redis (Upstash) | Session store, rate limiting, job queue |
| Auth | Clerk | Built-in org/team support, webhook-friendly |
| Payments | Stripe | Best-in-class SaaS billing, subscription management |
| Deployment | Vercel | Zero-config Next.js, preview deployments per PR |
| Monitoring | Sentry + Vercel Analytics | Error tracking + Core Web Vitals |

---

## Agents Used

**Standard:** `changelog`, `frontend`, `retro`, `docs`, `security`, `api`

**Web:** `web-performance`, `web-seo`, `web-accessibility`

**Custom:** `billing` — handles Stripe webhook processing, subscription state transitions, and invoice edge cases

---

## MCP Configuration

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/project"],
      "description": "Monorepo file access"
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "description": "Persistent session context"
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"],
      "description": "Structured reasoning for complex tasks"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "YOUR_GITHUB_TOKEN_HERE" },
      "description": "Issue creation and PR management"
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/your_db_dev"],
      "description": "Local dev database — never point at production"
    },
    "redis": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-redis", "redis://localhost:6379"],
      "description": "Local Redis for session and cache inspection"
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"],
      "description": "E2E tests and visual regression"
    },
    "fetch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-fetch"],
      "description": "API endpoint testing and webhook simulation"
    },
    "vercel": {
      "command": "npx",
      "args": ["-y", "@vercel/mcp"],
      "env": { "VERCEL_TOKEN": "YOUR_VERCEL_TOKEN_HERE" },
      "description": "Deployment management and preview URL access"
    },
    "stripe": {
      "command": "npx",
      "args": ["-y", "stripe-mcp"],
      "env": { "STRIPE_SECRET_KEY": "sk_test_YOUR_KEY_HERE" },
      "description": "Payment intent inspection and webhook testing (test mode only)"
    }
  }
}
```

---

## Key Decisions & Rationale

**Why Supabase over raw PostgreSQL?**
Row-level security lets us enforce tenant isolation at the database layer, not just the application layer. The hosted option removes ops burden for a small team.

**Why Clerk over NextAuth?**
Clerk's organisation management maps directly to our "team workspace" concept — no custom org table needed. The webhook events are reliable for keeping our DB in sync.

**Why Upstash Redis over ElastiCache?**
Serverless pricing model fits Vercel's edge function usage pattern. No always-on instance costs.

**Web agents used, not mobile:**
This is a web-first product. `web-seo` matters because the marketing pages need organic traffic. `web-accessibility` is required for enterprise customers with WCAG procurement requirements.
