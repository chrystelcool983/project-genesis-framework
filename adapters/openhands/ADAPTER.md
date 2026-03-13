# OpenHands Adapter — Project Genesis Framework

> **Also known as:** OpenDevin (previous name). Sometimes referred to as "Open Claw" in the community.

## How OpenHands Works

OpenHands is a fully autonomous AI software agent that runs in a sandboxed environment. Unlike editor-integrated tools (Cursor, Copilot), OpenHands:

- Operates a **full Linux environment** with browser, terminal, and file system
- Can run code, install packages, execute tests, and browse the web
- Reads instructions from **microagent files** — specialised markdown prompts for different task types
- Supports **task delegation** between a primary agent and specialist microagents

OpenHands is best suited for **end-to-end task execution** — give it a task and it handles the full implementation cycle autonomously.

---

## Setup Instructions

### Step 1: Create the microagents directory

```bash
mkdir -p .openhands/microagents
```

### Step 2: Generate your microagent files

```
Read core/UNIVERSAL_SPEC.md from this project.
Generate OpenHands microagent files following adapters/openhands/ADAPTER.md.
Output each file with its exact filename.
```

### Step 3: File placement

```
.openhands/
└── microagents/
    ├── repo.md                  # Repository-level context (loaded for every task)
    ├── implement-endpoint.md    # Task agent: implement a new API endpoint end-to-end
    ├── implement-feature.md     # Task agent: implement a full product feature
    ├── fix-bug.md               # Task agent: diagnose and fix a bug
    ├── write-tests.md           # Task agent: write tests for existing code
    ├── update-docs.md           # Task agent: sync living documentation
    ├── security-review.md       # Task agent: review code for security issues
    └── mobile-build.md          # Task agent: mobile-specific tasks (if applicable)
```

---

## Microagent File Format

OpenHands microagents have two types:

### `repo.md` — Repository-level context (always loaded)

```markdown
---
name: repo
type: repo
agent: CodeActAgent
---

# [PROJECT_NAME] Repository

## What this is
[Elevator pitch]. Platforms: [Web / Mobile / API].

## Stack
- Frontend: [framework + version] in `apps/web/`
- Backend: [language + framework] in `apps/api/`
- Mobile: [framework] in `apps/mobile/` (if applicable)
- Database: [database] — schema at `docs/schema.sql`
- Cache: [cache layer]
- Auth: [auth provider]
- Cloud: [cloud provider]

## Key documents — READ THESE before starting any task
- `docs/EDD.md` — Engineering spec with endpoint definitions
- `docs/PRD.md` — Product requirements and feature priority
- `docs/schema.sql` — Full database schema
- `docs/architecture.md` — Living service map (update after changes)
- `core/UNIVERSAL_SPEC.md` — Universal project spec

## Development environment setup
```bash
# Install dependencies
pnpm install

# Start all services
docker compose up -d

# Run database migrations
pnpm db:migrate

# Seed development data
pnpm db:seed

# Start development servers
pnpm dev
```

## Hard constraints — never violate
- TypeScript strict mode always
- Parameterised queries only — never string SQL
- Validate all input at API boundaries using Zod
- Check auth before every data access
- Never log sensitive fields (passwords, tokens, PII)
- Never commit secrets — all credentials via environment variables
- Write tests for every new endpoint or service function (80% coverage)
- Update living docs after every task completion

## After completing any task
1. Run the full test suite: `pnpm test`
2. Run the linter: `pnpm lint`
3. Update `docs/projectstatus.md` — mark the task ✅
4. Add entry to `docs/CHANGELOG.md` under [Unreleased]
5. Update `docs/architecture.md` if endpoints or schema changed
6. Commit with a conventional commit message: `type(scope): subject`
```

### Task microagents — triggered by keyword

```markdown
---
name: implement-endpoint
type: task
agent: CodeActAgent
triggers:
  - implement endpoint
  - add endpoint
  - new api endpoint
  - create endpoint
---
```

---

## Task Microagent Templates

### `implement-endpoint.md`

```markdown
---
name: implement-endpoint
type: task
agent: CodeActAgent
triggers:
  - implement endpoint
  - add endpoint  
  - new api endpoint
---

# Implement New API Endpoint

## Before starting
1. Read `docs/EDD.md` — find the exact endpoint specification
2. Read `docs/schema.sql` — understand the data model
3. Look at an existing similar endpoint as a reference implementation

## Implementation order (always follow this)
1. **Types** — Create Zod schema + TypeScript types in `feature.types.ts`
2. **Service** — Implement business logic in `feature.service.ts`
   - Check cache first, query DB on miss
   - Cache results with appropriate TTL
   - Return typed Result — never throw across service boundaries
3. **Controller** — Input parsing + response shaping in `feature.controller.ts`
   - Validate input using the Zod schema
   - Call service
   - Return standard response envelope
4. **Router** — Register route in `feature.router.ts`
5. **Tests** — Write in `feature.test.ts`
   - Happy path
   - Invalid input (each validation rule)
   - Unauthenticated request
   - Not found case

## Standard response envelope
```typescript
// Success
{ success: true, data: T, meta?: PaginationMeta }
// Error  
{ success: false, error: { code: ErrorCode, message: string } }
```

## Verification steps
```bash
pnpm test apps/api/src/[feature]    # Run feature tests
pnpm lint                            # Check for lint errors
pnpm typecheck                       # TypeScript validation
```

## After implementation
- Add endpoint to `docs/architecture.md` endpoint map
- Update `docs/CHANGELOG.md` under [Unreleased] → Added
- Mark task ✅ in `docs/projectstatus.md`
- Commit: `feat([scope]): add [endpoint description] endpoint`
```

### `implement-feature.md`

```markdown
---
name: implement-feature
type: task
agent: CodeActAgent
triggers:
  - implement feature
  - build feature
  - add feature
---

# Implement Full Feature

## Before starting
1. Read `docs/PRD.md` — find the feature spec and acceptance criteria
2. Read `docs/EDD.md` — find all endpoints this feature requires
3. Read `docs/schema.sql` — identify any schema changes needed
4. Map out the full scope: what backend endpoints, what frontend screens, what schema changes?

## Execution order (never skip steps)
1. **Schema migration** — if new tables or columns needed
   ```bash
   # Create migration file
   touch docs/migrations/[timestamp]_[description].sql
   # Apply migration
   pnpm db:migrate
   ```
2. **Backend endpoints** — implement per `implement-endpoint` microagent
3. **Frontend/Mobile screens** — implement UI consuming the new endpoints
4. **Integration tests** — test the full user flow end-to-end
5. **Living doc updates** — update all three living docs

## Acceptance criteria verification
Before marking complete, verify every acceptance criterion in `docs/PRD.md` for this feature.
Run a manual test walkthrough of the full user flow.

## After implementation
- Update `docs/projectstatus.md` — mark feature tasks ✅
- Add full CHANGELOG entry under [Unreleased]
- Update `docs/architecture.md` — schema diagram, endpoint map, service registry
- Commit: `feat([scope]): implement [feature name]`
```

### `fix-bug.md`

```markdown
---
name: fix-bug
type: task
agent: CodeActAgent
triggers:
  - fix bug
  - debug
  - fix issue
  - something is broken
---

# Bug Fix Protocol

## Investigation order
1. Reproduce the bug — confirm it's real and understand the exact conditions
2. Check logs — find the error message and stack trace
3. Identify the root cause — don't fix symptoms
4. Read the relevant EDD section — confirm what the correct behaviour should be
5. Write a failing test that captures the bug before fixing it

## Fix implementation
1. Fix the root cause (not the symptom)
2. Verify the failing test now passes
3. Run the full test suite — ensure no regressions
   ```bash
   pnpm test
   pnpm lint
   pnpm typecheck
   ```

## After the fix
- Add a test that would catch this regression in future
- Update `docs/CHANGELOG.md` under [Unreleased] → Fixed
- Commit: `fix([scope]): [what was fixed and why]`
```

### `write-tests.md`

```markdown
---
name: write-tests
type: task
agent: CodeActAgent
triggers:
  - write tests
  - add tests
  - improve coverage
---

# Write Tests

## Before starting
1. Read the relevant service file to understand what it does
2. Read the Zod schemas to understand valid/invalid inputs
3. Check `docs/EDD.md` for documented error cases

## Required test scenarios for every service function
1. ✅ Happy path — valid input, expected output
2. ❌ Each validation failure case
3. 🔍 Not found (if applicable)
4. 🔒 Unauthorised (if route is protected)
5. 🚫 Forbidden (if role-based access exists)
6. 💥 Upstream failure (DB error, external service error) — verify graceful handling

## Test structure
```typescript
describe('[ServiceName]', () => {
  describe('[methodName]', () => {
    describe('when [condition]', () => {
      it('should [outcome]', async () => {
        // Arrange
        const input = buildTestInput({ ... });
        // Act  
        const result = await service.method(input);
        // Assert
        expect(result).toMatchObject({ ... });
      });
    });
  });
});
```

## Rules
- Mock all external services — never call real APIs or DBs in unit tests
- Integration tests may use the test database — use the seed data
- Run tests after writing: `pnpm test [path]`
```

### `update-docs.md`

```markdown
---
name: update-docs
type: task
agent: CodeActAgent
triggers:
  - update docs
  - sync documentation
  - update living docs
---

# Update Living Documentation

## Step 1: Read recent git history
```bash
git log --oneline -20
git diff HEAD~5..HEAD --stat
```

## Step 2: Update projectstatus.md
- Find tasks in the current sprint section
- Mark completed tasks ✅
- Update progress bar percentage
- Add any new blockers discovered

## Step 3: Update CHANGELOG.md
- Add entries under [Unreleased] for everything in recent commits
- Use correct categories: Added | Changed | Fixed | Security | Infrastructure
- Format: `- [scope]: description of what changed`

## Step 4: Update architecture.md
Check if any of these changed:
- New endpoints → add to endpoint map table
- Changed endpoint → update the row
- New DB table/column → update schema section
- New external service → add to integrations table
- New environment deployed → update infrastructure table

## Step 5: Commit the doc updates
```bash
git add docs/
git commit -m "docs: sync living docs with recent changes"
```
```

---

## OpenHands Usage Tips for Genesis Projects

**Starting a task session:**

Go to the OpenHands UI (localhost:3000 by default) and give it the task directly. The `repo.md` microagent loads automatically when it detects the `.openhands/microagents/` directory.

**Example task prompts:**

```
Implement the user notifications endpoint from Section 7.2 of docs/EDD.md.
Follow the implement-endpoint microagent instructions.
```

```
Fix the bug where users are getting a 500 error when updating their profile.
The error appears in Sentry under the event ID [ID].
```

```
Write tests for apps/api/src/payments/payments.service.ts.
Current coverage is 45% — get it to at least 80%.
```

**Letting OpenHands run autonomously:**
OpenHands works best when you give it a clear, scoped task and let it run. It will install dependencies, run tests, fix failures, and iterate — all without you needing to intervene. Check back when it says it's done.

**Connecting to your repo:**
OpenHands can clone GitHub repos directly. Point it at your repo and it will set up the environment using the `repo.md` setup commands.
