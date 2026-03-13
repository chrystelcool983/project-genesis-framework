# Project Genesis Framework v1.1

## The Complete AI-Assisted Project Setup System

> **What this is:** A step-by-step meta-framework you give to your AI assistant (Claude) at the start of any new software project. It will generate every document, configuration file, and development tool needed to build the project with AI-assisted development (Claude Code).
>
> **How to use it:** Copy this entire file into a conversation with Claude. Provide your project idea/brief. Claude will walk through each phase, ask clarifying questions, and generate all artifacts in the correct dependency order.
>
> **What it produces:** By the end, you'll have a production-ready monorepo with full documentation, AI development tooling, CI/CD, and infrastructure — ready to start building with Claude Code.

---

## How to Start

Paste this prompt to Claude along with this entire document:

```
I'm starting a new software project. I've attached my Project Genesis Framework 
which outlines every artifact I need you to help me create. 

Here's my project idea:
[Describe your project — what it does, who it's for, what problem it solves]

Walk me through the framework phase by phase. Ask me the intake questions first, 
then generate each artifact in order. Don't skip any phase.
```

---

## Phase 0: Project Intake (Discovery Questions)

> **Purpose:** Gather enough context to make every downstream decision correctly. Claude should ask ALL of these before generating anything.

### 0.1 Core Product Questions

```
□ What does the product do? (1-2 sentence elevator pitch)
□ What problem does it solve? (Pain point)
□ Who are the target users? (2-4 personas with names and contexts)
□ What's the business model? (How does it make money?)
□ What market/geography is it targeting? (Country-specific regulations, languages, currencies)
□ What's the MVP scope? (What ships first vs what comes later?)
□ Are there competitors? (What exists today that's similar?)
□ What's the timeline? (When does the MVP need to be live?)
```

### 0.2 Technical Questions

```
□ What platforms? (Web, mobile, desktop, API, CLI, hardware)
□ If mobile: iOS, Android, or both? Native or cross-platform?
□ What's the team size? (1 person, 2-4, 5-10, 10+)
□ What's the team's tech expertise? (Languages/frameworks they know)
□ Any tech stack preferences or constraints? (e.g., "must use Python", "company uses AWS")
□ What third-party services are needed? (Auth, payments, email, SMS, maps, AI/ML)
□ What data does the system store? (User data, transactions, content, files)
□ What are the performance requirements? (Users, requests/sec, latency targets)
□ Any regulatory requirements? (GDPR, HIPAA, PCI-DSS, local data protection laws)
□ Offline requirements? (Does it need to work without internet?)
```

### 0.3 Development Process Questions

```
□ Git branching strategy? (GitFlow / Trunk-based / GitHub Flow / Recommend)
□ Commit convention? (Conventional Commits / Simple / Ticket-prefixed / Recommend)
□ PR review policy? (1 approval / 2 approvals / CTO reviews all / Recommend)
□ Monorepo or multi-repo? (Or recommend based on team size)
□ CI/CD preferences? (GitHub Actions / GitLab CI / CircleCI / Recommend)
□ Cloud provider preference? (AWS / GCP / Azure / Recommend)
□ Existing infrastructure? (Starting from scratch or migrating?)
```

### 0.4 Design Questions

```
□ Design system? (Material Design / Custom / Existing brand / Recommend)
□ Brand colours? (Existing palette or recommend based on product type)
□ Typography preference? (Modern / Classic / Technical / Recommend)
□ Dark mode required? (Yes / No / System-preference toggle)
□ Target devices? (Desktop-first, mobile-first, specific device constraints)
□ Accessibility requirements? (WCAG level)
```

---

## Phase 1: Strategic Documents

> **Purpose:** Define WHAT we're building and WHY before touching any code. These documents become the source of truth that all technical decisions reference.
>
> **Dependency:** Phase 0 (intake answers) must be complete.

### 1.1 Product Requirements Document (PRD)

**Generate a comprehensive PRD covering:**

```
□ Product overview and vision statement
□ Problem statement with market context
□ Solution description (how it works at a high level)
□ Target user personas (from intake, expanded with scenarios)
□ Access channels / platforms (web, mobile, API, etc.)
□ Core features list with priority levels:
    - P0 = Must have for MVP launch
    - P1 = Required within first 6 months
    - P2 = Important for growth (Month 7-12)
    - P3 = Future roadmap (Year 2+)
□ Feature details for each P0 and P1 item (description, user flow, acceptance criteria)
□ Revenue model and pricing tiers (if applicable)
□ Go-to-market strategy (acquisition channels, launch plan)
□ Success metrics and KPIs (with targets per milestone)
□ Risk analysis and mitigations
□ Build phases and milestones (sprint-level breakdown)
□ Open questions requiring team decisions (with owners and deadlines)
```

**Output format:** Markdown (.md) for Claude Code reference + PDF for team sharing

### 1.2 Engineering Design Document (EDD)

**Generate a comprehensive EDD covering:**

```
□ System overview and design principles (API-first, stateless, cache-first, fail-safe, etc.)
□ System boundaries (in scope vs out of scope)
□ High-level architecture diagram (ASCII art for markdown, visual for PDF)
□ Service decomposition (each service with):
    - Endpoint specification (method, path, input, output, auth, rate limits, errors)
    - Cache strategy
    - Error cases with codes
    - SLA targets
□ Data architecture:
    - Database schema (every table with columns, types, constraints, indexes)
    - Cache data structures (key patterns, types, TTLs)
    - Message queues / async job definitions
    - Data flow diagrams
□ API contract design:
    - Full endpoint list
    - Standard response envelope format
    - Error code taxonomy
    - Authentication methods
    - Rate limiting tiers
    - Webhook contracts (if applicable)
□ Authentication and authorisation design
□ Core algorithm specifications (scoring, ranking, matching — whatever the product's "brain" is)
□ Infrastructure and deployment:
    - Cloud resource specifications
    - Container strategy
    - CI/CD pipeline design
    - Environment strategy (local, staging, production, sandbox)
□ Monitoring, logging, and alerting plan
□ Security design (transport, input validation, auth, data at rest, secrets, dependencies)
□ Performance and scalability targets
□ Disaster recovery and backup strategy
□ Testing strategy (unit, integration, E2E, load, security, per-service coverage targets)
□ Technical debt register (what shortcuts are acceptable for MVP)
□ Future architecture decisions (triggers and options)
□ Glossary of domain-specific terms
```

**Output format:** Markdown (.md) for Claude Code reference + PDF for team sharing

### 1.3 Constraints & Edge Cases Register

**Generate a risk/edge case document covering:**

```
□ Identify all constraint categories relevant to this project:
    - Platform constraints (device, OS, network, offline)
    - Third-party service constraints (API limits, reliability, pricing)
    - Data quality and cold-start problems
    - User input edge cases
    - Payment/billing edge cases
    - Scalability and performance risks
    - Privacy and regulatory constraints
    - Security threat model
    - Business and operational risks
    - Community/user-generated content abuse scenarios (if applicable)
□ For each constraint/risk:
    - Description of the scenario
    - Severity (Critical / High / Medium / Low)
    - Likelihood (Certain / High / Medium / Low)
    - Concrete mitigation strategy
□ Organise by category with tables for scanability
```

**Output format:** PDF for team sharing + key items referenced in EDD and CLAUDE.md

---

## Phase 2: Technical Foundation

> **Purpose:** Define the tech stack, database schema, and project structure.
>
> **Dependency:** Phase 1 (PRD + EDD) must be complete.

### 2.1 Tech Stack Decision

**For each layer of the system, recommend and justify:**

```
□ Frontend framework (and why, with alternatives considered)
□ Backend framework/language (and why)
□ Database (and why — relational vs document vs graph)
□ Cache layer (and why)
□ Authentication provider (and why)
□ Payment provider (if applicable — and why)
□ Cloud provider and region (and why)
□ Container/deployment strategy (and why)
□ CI/CD tooling (and why)
□ Monitoring and error tracking (and why)
□ Analytics (and why)
□ Job queue / async processing (and why)
□ API documentation tooling (and why)
□ Communication services — email, SMS, push (and why each)
□ State management (frontend — and why)
□ ORM / database access layer (and why)
□ Package manager and build tool (and why)
□ Any domain-specific tools (ML frameworks, mapping, search, etc.)
```

**Output format:** Interactive artifact or markdown document with rationale for each choice

### 2.2 Database Schema

**Generate the complete database schema:**

```
□ Full SQL migration file (production-ready, wrapped in transaction)
□ All tables with columns, types, constraints, defaults
□ Custom enum types for all categorical data
□ Foreign key relationships with ON DELETE behaviour
□ Indexes for every column used in WHERE, JOIN, or ORDER BY
□ Composite indexes for common query patterns
□ Table partitioning for high-growth tables (if applicable)
□ Helper functions (normalisation, hashing, detection — domain-specific)
□ Database views for common complex queries
□ Seed data (test/sandbox data for development)
□ Lookup/config tables for configurable business rules
□ Trigger functions for auto-updating timestamps
□ Post-migration notes (partitioning cron, archival strategy)
```

**Output format:** Raw SQL file + interactive ERD artifact + Prisma schema (if using Prisma)

### 2.3 Environment Configuration

**Generate the .env.example file:**

```
□ Every environment variable grouped by service/concern
□ Descriptions and default values for each variable
□ Feature flags for every Phase 2+ feature (defaulting to false)
□ Scoring/algorithm configuration as tuneable env vars
□ Rate limiting configuration per user tier
□ Cache TTL configuration
□ Third-party service credentials (with placeholder values)
□ Environment-specific override documentation (staging vs production)
□ Security: log redaction fields, CORS origins, body size limits
□ Business rules as configurable values (thresholds, limits, weights)
```

**Output format:** .env.example file

---

## Phase 3: Project Structure & Scaffold

> **Purpose:** Create the actual codebase structure with boilerplate files.
>
> **Dependency:** Phase 2 (tech stack + schema + env) must be complete.

### 3.1 Repository Structure

**Decide and generate:**

```
□ Monorepo vs multi-repo (based on team size from intake)
□ If monorepo: workspace tool (Turborepo, Nx, or Lerna)
□ Directory structure with clear separation:
    - apps/ (each deployable service)
    - packages/ (shared code — types, utils, config, database)
    - infrastructure/ (Terraform, Docker, scripts)
    - docs/ (all documentation)
    - .claude/ (AI development tooling)
    - .github/ (CI/CD workflows)
□ Root configuration files:
    - package.json (workspace scripts)
    - turbo.json / nx.json (build orchestration)
    - tsconfig.base.json (shared TypeScript config)
    - pnpm-workspace.yaml / workspace config
    - .gitignore (comprehensive for the stack)
    - .prettierrc / .eslintrc (code formatting)
    - .nvmrc (Node version pin)
    - docker-compose.yml (local development)
    - README.md (project overview + quick start)
```

### 3.2 Service Scaffolds

**For each service, generate:**

```
□ package.json with correct dependencies and scripts
□ tsconfig.json extending the root config
□ Entry point file (server.ts, main.py, main.dart, etc.)
□ Module/feature directory structure
□ Middleware (auth, error handling, logging, rate limiting)
□ Config files (Redis client, queue setup, external service clients)
□ Route stubs for every endpoint from the EDD
□ Shared package exports (types, utils, constants, validation)
□ Dockerfile (multi-stage, optimised for size)
□ .eslintrc / linting config per service
```

### 3.3 CI/CD Workflows

**Generate GitHub Actions (or equivalent) workflows:**

```
□ CI workflow (on every PR):
    - Lint + format check
    - Type checking
    - Unit tests with service containers (DB, cache)
    - Integration tests
    - Docker image build verification
    - Coverage reporting
□ Deploy workflow:
    - Auto-deploy to staging on merge to main
    - Manual approval for production
    - Container registry push
    - Cloud service deployment (with canary/rolling strategy)
    - Smoke tests post-deploy
```

---

## Phase 4: CLAUDE.md (AI Development Guide)

> **Purpose:** Create the single file Claude Code reads to understand everything about the project.
>
> **Dependency:** Phases 1-3 must be complete — CLAUDE.md references everything.

### 4.1 CLAUDE.md Sections

**Generate the CLAUDE.md with all of these sections:**

```
□ Section 1: Project Overview
    - What the product does (1 paragraph)
    - Core product goals (3-5 bullets)
    - Target users (from PRD personas)
    - Business context (market, revenue model, timeline)

□ Section 2: Architecture Overview
    - System architecture diagram (ASCII)
    - Monorepo structure (directory tree)
    - Service responsibilities table (service, runtime, port, description)
    - Critical path data flow (step-by-step with latency targets)
    - Key design principles (API-first, stateless, cache-first, etc.)

□ Section 3: Reference Documents
    - Table linking to PRD, EDD, schema, risk register, .env.example
    - Usage rule: "Check the EDD before implementing, check the PRD for priority"

□ Section 4: Technology Stack
    - Tables for backend, frontend, ML/data, infrastructure
    - Exact versions pinned

□ Section 5: Design System & Style Guide
    - Brand identity and voice
    - Colour palette with exact hex codes (including semantic colours)
    - Typography (font families, weights, sizes for each role)
    - Spacing scale
    - Component patterns (cards, buttons, inputs, navigation)
    - Animation guidelines
    - Accessibility requirements
    - UX principles (3-7 rules specific to this product)
    - Target device testing list

□ Section 6: Code Conventions
    - Per language: file naming, variable naming, type naming, constant naming
    - Module structure pattern
    - Rules (strict types, validation, error handling, logging, no console.log)

□ Section 7: Repository Etiquette
    - Branching strategy (with branch naming convention)
    - Commit message format (with type, scope, examples)
    - PR process (template, approval count, CI requirements)
    - Code review checklist (what reviewers should verify)

□ Section 8: Constraints & Policies
    - Performance constraints table (endpoint targets, timeouts, size limits)
    - Security policies (auth, validation, logging redaction, secrets)
    - Data/privacy policies (regulation compliance)
    - Platform-specific constraints (mobile, USSD, API — whatever applies)

□ Section 9: Testing Instructions
    - How to run tests (commands per service)
    - Test directory structure
    - Coverage requirements per service
    - Test database setup
    - Test fixtures / sandbox data
    - Required test scenarios for every new endpoint/feature

□ Section 10: Local Development Setup
    - Prerequisites (with versions)
    - First-time setup steps (exact commands)
    - Verification commands
    - Useful dev commands reference

□ Section 11: Deployment
    - Environments table (URL, trigger, data)
    - Deploy process (step by step)
    - Environment variable management

□ Section 12: Common Tasks
    - Adding a new API endpoint (step by step)
    - Adding a new database table
    - Adding a new queue/job
    - Adding a new environment variable
    - Adding a new frontend screen/page
    - Adding a new dependency
    - Running individual services
    - API versioning strategy

□ Section 13: Living Documentation Rules
    - List of living docs and their purpose
    - When to update each document (specific triggers)
    - Update protocol (checklist Claude Code follows after every task)
    - Status icons reference
    - Worked example showing how to update all 3 docs for a sample feature

□ Section 14: Plugins
    - Table of all plugins with file paths and when to read them
    - Usage rule (which plugins for which type of task)
    - Interaction model: how plugins + commands + agents work together

□ Section 15: Slash Commands & Subagents
    - Table of all commands with descriptions and when to use
    - Table of all agents with specialisation and when to invoke

□ Section 16: MCP Servers
    - Table of all configured MCP servers with purpose
    - Setup instructions (placeholder values to replace)
```

---

## Phase 5: Living Documentation

> **Purpose:** Create the auto-maintained documents that stay in sync with the codebase.
>
> **Dependency:** Phase 4 (CLAUDE.md) must define the update rules.

### 5.1 architecture.md

**Generate with:**

```
□ System architecture diagram (detailed ASCII)
□ Service registry table (service, location, runtime, port, status emoji)
□ Shared packages table
□ Data architecture:
    - Database schema diagram (ASCII ERD)
    - Table summary with stats (total tables, enums, views, functions)
    - Cache key patterns table (key, type, TTL, purpose)
    - Message queue definitions table (queue, producer, consumer, purpose)
□ External service integrations table (service, purpose, method, status)
□ API endpoint map (method, path, module, auth, status — every endpoint)
□ Infrastructure tables (cloud resources, environments — with status)
□ Architecture Decision Log (ADR) with initial decisions and template
□ Update instructions at the bottom
```

### 5.2 CHANGELOG.md

**Generate with:**

```
□ Keep a Changelog format header
□ [Unreleased] section with all scaffold items logged
□ Category definitions (Added, Changed, Fixed, Security, Infrastructure, etc.)
□ Entry format guide (scope prefix + description)
□ Version plan table (mapping versions to sprints/milestones)
□ Update instructions at the bottom
```

### 5.3 projectstatus.md

**Generate with:**

```
□ Dashboard summary (current sprint, dates, team, blockers)
□ Each sprint/phase as a section with:
    - Goal statement
    - Progress bar
    - Task tables grouped by workstream (task, owner, status, date, notes)
    - Success criteria table (criteria, target, current, status)
□ Accomplishments log (reverse-chronological milestones)
□ Blockers & risks table (active items needing attention)
□ Key metrics table (current vs targets at each milestone)
□ Update instructions with progress bar guide at the bottom
```

---

## Phase 6: Plugins (Skill Files)

> **Purpose:** Give Claude Code domain-specific knowledge for different types of work.
>
> **Dependency:** Phases 1-4 define what knowledge is needed.

### 6.1 Frontend Design Plugin

**Generate `.claude/plugins/frontend-design.md` with:**

```
□ Complete colour system with code (CSS variables, Flutter classes, or framework equivalent)
□ Semantic colour mappings (risk levels, statuses, feedback colours)
□ Typography system with code (every text role: display, heading, body, caption, button, mono)
□ Spacing scale with code
□ Component patterns with implementation notes:
    - Hero/primary component (the main thing users see)
    - Input components
    - Card patterns
    - Navigation patterns
    - Loading states (skeleton, not spinner)
    - Error states
    - Empty states
□ Animation guidelines (durations, easing, what to animate)
□ Accessibility requirements
□ Target device testing list with specs
□ Admin/dashboard design rules (if applicable)
```

### 6.2 Dev Streamline Plugin

**Generate `.claude/plugins/dev-streamline.md` with:**

```
□ Module implementation checklist (step-by-step for new features)
□ Code templates for the project's stack:
    - Route/endpoint template
    - Controller/handler template
    - Service/business logic template with cache pattern
    - Error throwing pattern
    - Queue dispatch pattern
    - API response format
□ Input handling rules (validation, normalisation, sanitisation)
□ Database query patterns (ORM examples for common operations)
□ Testing patterns (unit test template, integration test template)
□ Performance rules (what to cache, what to async, what to index)
□ Security rules (validate, parameterise, hash, mask, never log)
```

### 6.3 Compound Engineering Plugin

**Generate `.claude/plugins/compound-engineering.md` with:**

```
□ Multi-service execution order (bottom-up: DB → packages → services → clients)
□ Cross-service feature patterns (3-5 patterns specific to this product)
□ Database migration protocol
□ Cache invalidation matrix (event → which cache keys to invalidate)
□ Error propagation across services (what happens when each service fails)
□ Feature flag gating patterns
□ Dependency change protocol (how to add new packages safely)
```

### 6.4 Domain Context Plugin

**Generate `.claude/plugins/domain-context.md` with:**

```
□ Industry/domain-specific knowledge Claude Code needs
□ Data format rules (phone numbers, addresses, currencies, dates for the target market)
□ Third-party service integration notes (API quirks, rate limits, gotchas)
□ Regulatory compliance rules (data protection, industry regulations)
□ Localisation rules (language, date format, currency, number format)
□ Common domain terminology and definitions
```

### 6.5 Testing & QA Plugin

**Generate `.claude/plugins/testing-qa.md` with:**

```
□ Test fixture data (sandbox/test data for development)
□ Test user definitions (different roles, tiers, states)
□ Required test scenario checklist per endpoint/feature type
□ Platform-specific test scenarios (mobile, API, webhook, etc.)
□ Test naming conventions
```

---

## Phase 7: Slash Commands

> **Purpose:** Create reusable workflows Claude Code can execute on demand.
>
> **Dependency:** CLAUDE.md and living docs must exist.

### 7.1 Standard Commands (generate all of these)

**`.claude/commands/commit.md` — Smart Commit**
```
□ Analyse staged changes
□ Generate conventional commit message (type + scope + subject)
□ Validate format and check for secrets
□ Remind about doc updates if applicable
□ Execute commit after confirmation
```

**`.claude/commands/update-docs.md` — Update Living Docs**
```
□ Read recent git history
□ Cross-reference against all living docs
□ Identify gaps (code changes not reflected in docs)
□ Propose specific updates to each document
□ Apply changes after confirmation
```

**`.claude/commands/create-issue.md` — Create GitHub Issues**
```
□ Accept spec reference or free-form prompt
□ Break into individual actionable issues
□ Generate structured issues with: requirements, acceptance criteria, technical notes, labels, sizing
□ Support batch creation for entire sprints
□ Cross-reference dependent issues
```

**`.claude/commands/new-endpoint.md` — Scaffold New Endpoint**
```
□ Create full module structure (route, controller, service, types, test)
□ Register in the main server/router
□ Update living docs with scaffolded status
□ Show what to implement next
```

**`.claude/commands/sprint-status.md` — Sprint Dashboard**
```
□ Read projectstatus.md
□ Calculate completion stats
□ Show progress bar, task counts, blockers, next priorities
□ List recent accomplishments
```

**`.claude/commands/deploy-check.md` — Pre-Deploy Validation**
```
□ Run tests, typecheck, lint, format check
□ Verify Docker builds
□ Scan for secrets in code
□ Check .env.example is current
□ Verify living docs are up to date
□ Check for console.log in production code
□ Verify migrations are ready
```

---

## Phase 8: Subagents

> **Purpose:** Create specialist agents for different types of work.
>
> **Dependency:** Plugins must exist (agents reference them).

### 8.1 Standard Agents (generate all of these)

**`.claude/agents/changelog.md` — Changelog Agent**
```
□ Maintains CHANGELOG.md exclusively
□ Knows the Keep a Changelog format
□ Categorises changes correctly
□ Never duplicates entries
□ Uses scope prefixes consistently
```

**`.claude/agents/frontend.md` — Frontend Agent**
```
□ Implements UI following the design plugin strictly
□ Enforces colour system, typography, spacing
□ Follows state management patterns
□ Targets specified devices
□ Ensures accessibility compliance
```

**`.claude/agents/retro.md` — Retro Agent**
```
□ Analyses sprint velocity (planned vs delivered)
□ Identifies scope creep
□ Checks for tech debt introduced
□ Generates structured retro with action items
□ Recommends capacity for next sprint
```

**`.claude/agents/docs.md` — Docs Agent**
```
□ Detects stale documentation
□ Cross-references code against doc state
□ Proposes targeted updates
□ Never deletes — only adds or updates
```

**`.claude/agents/security.md` — Security Agent**
```
□ Reviews auth on every route
□ Checks input validation
□ Scans for data exposure (logging, error responses)
□ Verifies regulatory compliance
□ Outputs pass/warn/fail per check
```

**`.claude/agents/api.md` — API/Backend Agent**
```
□ Implements endpoints following EDD spec exactly
□ Follows correct implementation order
□ Uses code templates from dev-streamline plugin
□ Handles all error cases
```

### 8.2 Web App Agents

These agents are relevant for projects with a web frontend (React, Next.js, Vue, etc.).

**`.claude/agents/web-performance.md` — Web Performance Agent**
```
□ Audits bundle sizes and identifies heavy dependencies
□ Checks for missing code splitting and lazy loading
□ Validates image optimisation (formats, sizes, lazy loading)
□ Reviews Core Web Vitals: LCP, CLS, INP targets
□ Checks caching headers and CDN strategy
□ Identifies render-blocking resources
□ Reviews server-side rendering vs client-side rendering decisions
□ Outputs audit report with specific file-level fixes
```

**`.claude/agents/web-seo.md` — SEO Agent**
```
□ Validates meta tags (title, description, OG, Twitter cards) on every page
□ Checks structured data / JSON-LD markup
□ Reviews heading hierarchy (one H1, logical H2/H3 structure)
□ Verifies canonical URLs and sitemap.xml
□ Checks robots.txt and crawlability
□ Reviews page load performance (affects SEO ranking)
□ Validates mobile-friendliness of all pages
□ Checks internal linking structure
```

**`.claude/agents/web-accessibility.md` — Accessibility Agent**
```
□ Validates WCAG 2.1 AA compliance across all components
□ Checks colour contrast ratios (4.5:1 for text, 3:1 for UI elements)
□ Verifies keyboard navigation and focus management
□ Reviews ARIA labels, roles, and landmarks
□ Tests screen reader compatibility (alt text, form labels, error messages)
□ Checks skip-to-content links
□ Reviews focus trapping in modals and dropdowns
□ Outputs violations by severity with remediation code
```

### 8.3 Mobile App Agents

These agents are relevant for projects with a mobile client (React Native, Flutter, Swift, Kotlin).

**`.claude/agents/mobile-ux.md` — Mobile UX Agent**
```
□ Enforces touch target minimums (44×44pt iOS / 48×48dp Android)
□ Checks gesture conflict handling (swipe, scroll, tap disambiguation)
□ Reviews navigation patterns (tab bar, stack, drawer — platform conventions)
□ Validates safe area handling (notch, home indicator, status bar)
□ Reviews haptic feedback usage (reserved for confirmations, errors)
□ Checks keyboard avoidance and scroll-into-view on input focus
□ Validates loading states for slow network conditions
□ Reviews empty states and onboarding flows
```

**`.claude/agents/mobile-performance.md` — Mobile Performance Agent**
```
□ Analyses list rendering (FlatList/LazyColumn virtualisation)
□ Reviews image caching and memory management
□ Checks unnecessary re-renders in component trees
□ Reviews JS thread bridging (React Native specific)
□ Validates offline-first data handling and sync strategy
□ Checks app startup time (cold vs warm launch)
□ Reviews battery usage (background tasks, location polling)
□ Validates push notification payload sizes
```

**`.claude/agents/mobile-platform.md` — Platform Compliance Agent**
```
□ Reviews App Store / Play Store guideline compliance
□ Checks permissions usage (only request what's needed, with rationale)
□ Validates deep link / universal link handling
□ Reviews in-app purchase implementation (if applicable)
□ Checks privacy manifest requirements (iOS 17+)
□ Reviews background task registration and limits
□ Validates push notification entitlements
□ Checks for deprecated API usage per target OS version
```

### 8.4 Project-Specific Agents

```
□ Identify if the project needs specialised agents beyond the standard set
□ Examples: USSD Agent (telco projects), ML Agent (AI projects), 
  Payment Agent (fintech), Content Agent (CMS), Search Agent (search-heavy apps)
□ Each custom agent needs: focused scope, mandatory references, hard constraints, implementation patterns
```

---

## Phase 9: MCP Configuration

> **Purpose:** Give Claude Code direct access to development infrastructure.
>
> **Dependency:** Tech stack decisions from Phase 2.

### 9.1 MCP Server Selection

**Based on the tech stack, configure relevant MCPs:**

#### Core MCPs (every project)

```
□ filesystem        — Direct file read/write access across the monorepo
□ memory            — Persistent context that survives session restarts
□ sequential-thinking — Structured reasoning for complex multi-step problems
□ github            — Issue creation, PR management, branch operations
□ fetch             — HTTP requests for testing APIs and webhooks
```

#### Database MCPs (choose based on your stack)

```
□ postgres          — Direct SQL execution, schema inspection, query analysis
□ mysql             — MySQL/MariaDB equivalent
□ mongodb           — Document query and aggregation pipeline access
□ sqlite            — Lightweight local database access
```

#### Cache & Queue MCPs

```
□ redis             — Key inspection, pub/sub testing, cache debugging
```

#### Web App MCPs

Recommended for projects with a web frontend.

```
□ playwright        — Browser automation for E2E testing and visual regression
                      Best for: React/Next.js/Vue/Svelte frontends
                      Use when: Running automated UI tests, screenshot diffs, form testing

□ puppeteer         — Headless Chrome control for scraping and screenshots
                      Best for: Generating PDF reports, crawling, visual testing
                      Use when: You need simpler browser control without full Playwright setup

□ browsertools      — Live browser inspection and console access
                      Best for: Debugging JavaScript errors in real-time during development
                      Use when: Diagnosing client-side bugs without leaving the terminal

□ exa               — Semantic web search with structured results
                      Best for: Research agents, content enrichment, competitor analysis
                      Use when: Your app needs to pull in live web data or your team needs research

□ firecrawl         — Structured web scraping and crawling
                      Best for: Ingesting competitor data, documentation sites, public datasets
                      Use when: Building data pipelines that pull from websites

□ vercel            — Deployment management and edge function control
                      Best for: Next.js projects hosted on Vercel
                      Use when: Deploying to preview/production, checking deployment status

□ cloudflare        — CDN configuration, Workers, and DNS management
                      Best for: Projects using Cloudflare for CDN/edge compute
                      Use when: Managing Workers, setting cache rules, updating DNS
```

#### Mobile App MCPs

Recommended for projects with a React Native or Flutter mobile client.

```
□ xcode             — iOS simulator control, build status, device logs
                      Best for: React Native (iOS) and native Swift projects
                      Use when: Running iOS builds, inspecting crash logs, testing on simulator

□ android-studio    — Android emulator control and ADB command access
                      Best for: React Native (Android) and native Kotlin projects
                      Use when: Running Android builds, testing on emulator, inspecting logcat

□ expo              — Expo project management and OTA update deployment
                      Best for: React Native projects using Expo managed workflow
                      Use when: Publishing OTA updates, managing Expo app config, dev client

□ appstore-connect  — App Store submission status and TestFlight management
                      Best for: All iOS app projects at release stage
                      Use when: Checking review status, managing beta testers, version metadata

□ firebase          — Firestore, Auth, Storage, and Remote Config access
                      Best for: Mobile apps using Firebase backend (very common in mobile)
                      Use when: Querying production data, testing auth flows, updating remote config

□ sentry            — Error monitoring, crash reports, and performance traces
                      Best for: Production mobile apps needing real-time error triage
                      Use when: Triaging crash reports, correlating errors with releases
```

#### Cloud Provider MCPs (choose based on your cloud)

```
□ aws-cli           — S3, Lambda, ECS, RDS and other AWS service management
□ gcp               — Cloud Run, BigQuery, Cloud Storage management
□ azure             — Azure resource and service management
```

#### Domain-Specific MCPs (add as needed)

```
□ stripe            — Payment intent inspection, webhook testing, subscription management
□ slack             — Team notifications, alert routing, deployment announcements
□ linear            — Issue creation, sprint planning, project tracking (alternative to GitHub issues)
□ notion            — Documentation sync, project wiki management
□ sendgrid          — Email template testing, delivery monitoring
□ twilio            — SMS and voice testing
□ openai            — AI feature integration testing
□ anthropic         — Claude API integration testing
```

### 9.2 MCP Config File

**Generate `.claude/mcp/mcp-config.json` with:**

```
□ Each MCP server with command, args, and description
□ Placeholder values clearly marked for user to fill in
□ Environment-specific notes (these connect to LOCAL dev, not production)
```

**Example structure for a web app with PostgreSQL:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/your/project"],
      "description": "File access across the monorepo"
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "description": "Persistent context across sessions"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "YOUR_GITHUB_TOKEN_HERE" },
      "description": "Issue creation and PR management"
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/YOUR_DB_NAME"],
      "description": "Direct database access for local dev"
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"],
      "description": "Browser automation for E2E testing"
    },
    "fetch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-fetch"],
      "description": "HTTP requests for API testing"
    }
  }
}
```

**Example structure for a mobile app (React Native + Firebase):**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/your/project"],
      "description": "File access across the project"
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "description": "Persistent context across sessions"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "YOUR_GITHUB_TOKEN_HERE" },
      "description": "Issue creation and PR management"
    },
    "expo": {
      "command": "npx",
      "args": ["-y", "@expo/mcp"],
      "env": { "EXPO_TOKEN": "YOUR_EXPO_TOKEN_HERE" },
      "description": "Expo build and OTA update management"
    },
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-mcp"],
      "env": {
        "FIREBASE_PROJECT_ID": "YOUR_PROJECT_ID",
        "GOOGLE_APPLICATION_CREDENTIALS": "/path/to/service-account.json"
      },
      "description": "Firestore, Auth, and Remote Config access"
    },
    "sentry": {
      "command": "npx",
      "args": ["-y", "@sentry/mcp"],
      "env": { "SENTRY_AUTH_TOKEN": "YOUR_SENTRY_TOKEN" },
      "description": "Crash reports and error monitoring"
    }
  }
}
```

---

## Phase 10: Final Assembly & Verification

> **Purpose:** Package everything, verify completeness, and prepare for first Claude Code session.
>
> **Dependency:** All previous phases complete.

### 10.1 File Placement Verification

```
□ CLAUDE.md at repo root
□ docs/ contains: PRD.md, EDD.md, schema.sql, architecture.md, CHANGELOG.md, projectstatus.md
□ .claude/plugins/ contains all plugin files
□ .claude/commands/ contains all command files
□ .claude/agents/ contains all agent files
□ .claude/mcp/ contains mcp-config.json
□ .env.example at repo root
□ docker-compose.yml at repo root
□ README.md at repo root
□ All config files (turbo.json, tsconfig, pnpm-workspace, .gitignore, .prettierrc)
□ CI/CD workflows in .github/workflows/
□ Dockerfiles in infrastructure/docker/
□ Service scaffolds in apps/
□ Shared packages in packages/
```

### 10.2 Cross-Reference Verification

```
□ CLAUDE.md Section 3 links to all docs that exist
□ CLAUDE.md Section 14 lists all plugins that exist in .claude/plugins/
□ CLAUDE.md Section 15 lists all commands and agents that exist
□ CLAUDE.md Section 16 lists all MCP servers configured
□ architecture.md endpoint map matches EDD endpoint list
□ projectstatus.md tasks match PRD milestones and EDD deliverables
□ CHANGELOG.md [Unreleased] includes all scaffold items
□ .env.example covers every env var referenced in code and config
□ docker-compose.yml services match the architecture
□ CI/CD workflows test all services in the monorepo
□ Dockerfiles exist for every deployable service
```

### 10.3 First Claude Code Prompt Template

After all files are in place and infrastructure is running, use this prompt:

```
Read the CLAUDE.md file at the project root to understand the full project context.

Then read docs/[EDD filename] for the technical specification and 
docs/[schema filename] for the database schema.

Your first task: [Usually: Create the ORM schema from the SQL schema, 
run migrations, create seed script, verify database is working]

Then implement [the first P0 endpoint] end-to-end. Read the EDD 
Section [X] for the exact specification. Follow the dev-streamline 
plugin patterns. Write tests. Update living docs when done.
```

---

## Quick Reference: Artifact Inventory

Every completed project setup should have these files:

| # | Artifact | Location | Format |
|---|----------|----------|--------|
| 1 | Product Requirements Document | `docs/PRD.md` + `docs/PRD.pdf` | MD + PDF |
| 2 | Engineering Design Document | `docs/EDD.md` + `docs/EDD.pdf` | MD + PDF |
| 3 | Constraints & Edge Cases | `docs/risks.pdf` | PDF |
| 4 | Database Schema (SQL) | `docs/001_initial_schema.sql` | SQL |
| 5 | Database Schema (Visual) | Generated artifact | JSX/HTML |
| 6 | Environment Config | `.env.example` | Env |
| 7 | Architecture (Living) | `docs/architecture.md` | MD |
| 8 | Changelog (Living) | `docs/CHANGELOG.md` | MD |
| 9 | Project Status (Living) | `docs/projectstatus.md` | MD |
| 10 | AI Dev Guide | `CLAUDE.md` | MD |
| 11 | Frontend Design Plugin | `.claude/plugins/frontend-design.md` | MD |
| 12 | Dev Streamline Plugin | `.claude/plugins/dev-streamline.md` | MD |
| 13 | Compound Engineering Plugin | `.claude/plugins/compound-engineering.md` | MD |
| 14 | Domain Context Plugin | `.claude/plugins/domain-context.md` | MD |
| 15 | Testing & QA Plugin | `.claude/plugins/testing-qa.md` | MD |
| 16 | /commit Command | `.claude/commands/commit.md` | MD |
| 17 | /update-docs Command | `.claude/commands/update-docs.md` | MD |
| 18 | /create-issue Command | `.claude/commands/create-issue.md` | MD |
| 19 | /new-endpoint Command | `.claude/commands/new-endpoint.md` | MD |
| 20 | /sprint-status Command | `.claude/commands/sprint-status.md` | MD |
| 21 | /deploy-check Command | `.claude/commands/deploy-check.md` | MD |
| 22 | Changelog Agent | `.claude/agents/changelog.md` | MD |
| 23 | Frontend Agent | `.claude/agents/frontend.md` | MD |
| 24 | Retro Agent | `.claude/agents/retro.md` | MD |
| 25 | Docs Agent | `.claude/agents/docs.md` | MD |
| 26 | Security Agent | `.claude/agents/security.md` | MD |
| 27 | API/Backend Agent | `.claude/agents/api.md` | MD |
| 28 | Web Performance Agent | `.claude/agents/web-performance.md` | MD |
| 29 | SEO Agent | `.claude/agents/web-seo.md` | MD |
| 30 | Accessibility Agent | `.claude/agents/web-accessibility.md` | MD |
| 31 | Mobile UX Agent | `.claude/agents/mobile-ux.md` | MD |
| 32 | Mobile Performance Agent | `.claude/agents/mobile-performance.md` | MD |
| 33 | Platform Compliance Agent | `.claude/agents/mobile-platform.md` | MD |
| 34 | Project-Specific Agent(s) | `.claude/agents/{name}.md` | MD |
| 35 | MCP Config | `.claude/mcp/mcp-config.json` | JSON |
| 36 | Docker Compose | `docker-compose.yml` | YAML |
| 37 | Dockerfiles | `infrastructure/docker/Dockerfile.*` | Dockerfile |
| 38 | CI Workflow | `.github/workflows/ci.yml` | YAML |
| 39 | Deploy Workflow | `.github/workflows/deploy.yml` | YAML |
| 40 | README | `README.md` | MD |
| 41 | Monorepo Config | `turbo.json`, `pnpm-workspace.yaml`, `tsconfig.base.json` | Various |
| 42 | Git Config | `.gitignore`, `.prettierrc`, `.nvmrc` | Various |
| 43 | Service Scaffolds | `apps/*/` | Code |
| 44 | Shared Packages | `packages/*/` | Code |

**Total: 44 artifacts per project**

---

## Versioning This Framework

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | 2026-03 | Added web & mobile agents (Phases 8.2/8.3), expanded MCP catalogue with web/mobile examples and annotated configs |
| 1.0 | 2026-03 | Initial framework based on FraudShield project setup |

Update this framework after each project to capture new patterns, better questions, and improved templates.

---

*Built with the principle: Plan thoroughly, build confidently, iterate continuously.*
