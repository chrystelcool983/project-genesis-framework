# Aider Adapter — Project Genesis Framework

## How Aider Works

Aider is a terminal-based AI coding assistant that works directly with git. It reads context from:
- **`.aider.conf.yml`** — Configuration file (model, auto-commit, lint settings)
- **`CONVENTIONS.md`** (or any file specified in `--read`) — Project conventions loaded into every session
- **Files you explicitly add** to the chat (`/add filename`)
- **`aider.chat.history.md`** — Auto-generated session history (never manually edit)

Aider's strength is precise, diff-level edits driven by clear conventions and targeted file context.

---

## Setup Instructions

### Step 1: Install Aider

```bash
pip install aider-chat
# or
brew install aider
```

### Step 2: Generate your config files

```
Read core/UNIVERSAL_SPEC.md from this project.
Generate an Aider .aider.conf.yml and CONVENTIONS.md following adapters/aider/ADAPTER.md.
```

### Step 3: File placement

```
/                           # Repo root
├── .aider.conf.yml         # Aider configuration
├── CONVENTIONS.md          # Project conventions (loaded every session)
└── .aider.gitignore        # Files Aider should never modify
```

---

## `.aider.conf.yml` Template

```yaml
# .aider.conf.yml
# Aider configuration for [PROJECT_NAME]

##########
# Model
##########
model: claude-sonnet-4-5          # Best for complex codebase tasks
# model: gpt-4o                  # Alternative
# model: claude-opus-4-5         # For hardest problems (slower + costly)

##########
# Context
##########
read:
  - CONVENTIONS.md               # Always loaded — project conventions
  - core/UNIVERSAL_SPEC.md       # Always loaded — project spec
  # Add more files here if they should always be in context:
  # - docs/EDD.md                # Uncomment if you want EDD always loaded

##########
# Git behaviour
##########
auto-commits: false               # Review diffs before committing
dirty-commits: true               # Allow commits even with unstaged changes
commit-prompt: |
  Generate a conventional commit message for these changes.
  Format: type(scope): subject
  Types: feat | fix | chore | docs | test | refactor | perf
  Scope: the service or feature name (e.g. auth, api, web, mobile)
  Subject: imperative mood, max 72 chars, no period at end
  
  If the change warrants it, add a body explaining WHY (not what).

##########
# Code quality
##########
lint-cmd: pnpm lint               # Run linter after edits
test-cmd: pnpm test               # Run tests after edits (optional — can be slow)
auto-test: false                  # Set true to auto-run tests after every change

##########
# Editor
##########
editor: code                      # Opens diffs in VS Code (change to your editor)
pretty: true
stream: true

##########
# Behaviour
##########
yes-always: false                 # Always confirm before applying changes
show-diffs: true                  # Show diffs before applying
map-tokens: 2048                  # Repo map tokens (increase for large repos)
```

---

## `CONVENTIONS.md` Template

This file is loaded into every Aider session via the `read` config above.

```markdown
# [PROJECT_NAME] — Development Conventions

## What this project is
[Elevator pitch]. Platforms: [Web / Mobile / API].
Stack: [Frontend] · [Backend] · [Database] · [Cache] · [Auth] · [Cloud]

## Repository layout
```
apps/web/      # [Web app — Next.js / React / Vue]
apps/api/      # [API — Node.js / Python / Go]
apps/mobile/   # [Mobile — React Native / Flutter] (if applicable)
packages/      # Shared: types, utils, logger, config
docs/          # PRD.md, EDD.md, schema.sql, architecture.md
```

## Code rules — always follow these

### TypeScript
- Strict mode: `noImplicitAny`, `strictNullChecks`, `noUncheckedIndexedAccess`
- Absolute imports via tsconfig paths — never `../../relative/paths`
- Zod schemas at every external boundary (API input, env vars, external API responses)
- `unknown` over `any` — always narrow before use
- No `ts-ignore` without an explanatory comment

### Logging
- Use `packages/logger` — never `console.log`, `console.error`, or `console.warn`
- Never log: passwords, tokens, card data, SSNs, or any PII

### Database
- Parameterised queries or ORM only — no string-concatenated SQL ever
- New columns always require a migration file in `docs/` or `infrastructure/migrations/`
- Add indexes for any column used in WHERE, JOIN, or ORDER BY

### Security
- Validate all external input before use (Zod schema at the boundary)
- Check authentication and authorisation before every data operation
- Never store or commit secrets — use environment variables
- HTTPS-only cookies: HttpOnly, Secure, SameSite=Strict

## Naming conventions
| Thing | Convention | Example |
|-------|-----------|---------|
| Files | kebab-case | `user-profile.ts` |
| React components | PascalCase | `UserProfile.tsx` |
| Functions / variables | camelCase | `getUserProfile` |
| Constants | SCREAMING_SNAKE | `MAX_RETRY_COUNT` |
| Database tables | snake_case | `user_profiles` |
| Env variables | SCREAMING_SNAKE | `DATABASE_URL` |

## Module structure (backend)
```
feature/
├── feature.router.ts      # Route definitions only
├── feature.controller.ts  # Input parsing + response shaping
├── feature.service.ts     # Business logic + DB/cache
├── feature.types.ts       # Zod schemas + TypeScript types
└── feature.test.ts        # Unit + integration tests
```

## API response format
```typescript
// Success
{ success: true, data: T, meta?: { page, total } }
// Error
{ success: false, error: { code: ErrorCode, message: string } }
```

## Error codes
VALIDATION_ERROR (400) · UNAUTHORISED (401) · FORBIDDEN (403) · NOT_FOUND (404) · CONFLICT (409) · INTERNAL_ERROR (500)

## Implementation order (for new endpoints)
1. Zod schema → 2. Service → 3. Controller → 4. Router → 5. Tests → 6. Update docs/architecture.md

## After completing any task
- Update `docs/projectstatus.md` — mark the task ✅
- Add entry to `docs/CHANGELOG.md` under [Unreleased]
- Update `docs/architecture.md` if service, endpoint, or schema changed
```

---

## `.aider.gitignore`

Files Aider should never modify:

```
# Aider should not edit these files directly
.env
.env.*
!.env.example
docs/PRD.md
docs/EDD.md
*.pem
*.key
*.p12
infrastructure/terraform/**
.github/workflows/**
```

---

## Aider Workflow for Genesis Projects

### Starting a session

```bash
# From repo root — CONVENTIONS.md and UNIVERSAL_SPEC.md load automatically
aider

# Add specific files for context before starting a task
/add apps/api/src/auth/auth.service.ts
/add docs/EDD.md
/read docs/schema.sql
```

### Task workflow

```bash
# 1. Add relevant files
/add apps/api/src/users/users.service.ts
/add apps/api/src/users/users.types.ts
/add apps/api/src/users/users.test.ts

# 2. Give a specific task
Implement the getUserById service function following CONVENTIONS.md.
The function should: check cache first, query the DB on miss, cache the result for 300s, return null if not found.

# 3. Review the diff, confirm
# 4. Run tests
/run pnpm test apps/api/src/users

# 5. Commit
/commit
```

### Useful Aider commands

```bash
/add <file>        # Add file to context
/drop <file>       # Remove file from context
/read <file>       # Load file as read-only context (won't be edited)
/diff              # Show pending changes
/undo              # Undo last change
/run <command>     # Run a shell command
/commit            # Commit with AI-generated message
/ask <question>    # Ask a question without making changes
/map               # Show the repo map (which files Aider knows about)
/tokens            # Show token usage
/voice             # Voice input mode
```

### Loading the EDD for an implementation task

```bash
# Load EDD as read-only reference, then implement
/read docs/EDD.md
Implement the endpoint described in Section 4.2 of the EDD I just loaded.
Add the files you need to implement it.
```

### Running genesis-style living doc updates

```bash
/add docs/architecture.md
/add docs/CHANGELOG.md
/add docs/projectstatus.md

Update these living docs to reflect the work done in the last commit.
Read the git log to understand what changed.
```

---

## Aider Tips for Genesis Projects

**Keep sessions focused** — Add only the files relevant to the current task. Aider's diff-based approach works best with tight context.

**Use `/read` for specs** — Load `docs/EDD.md` as read-only so Aider references it without trying to edit it.

**Pair with `--auto-test`** — For test-driven tasks, set `auto-test: true` in `.aider.conf.yml` to run tests after every edit.

**Use voice mode for complex tasks** — `aider --voice` lets you describe what you want naturally while Aider handles the code.

**Git history as context** — Aider reads recent git history automatically. Write good commit messages and Aider will follow the project's patterns more accurately.
