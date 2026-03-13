# Continue Adapter — Project Genesis Framework

## How Continue Works

Continue (continue.dev) is an open-source AI coding assistant for VS Code and JetBrains. It reads context from:
- **`.continue/config.json`** — Main configuration: model selection, context providers, slash commands, and rules
- **`.continue/rules/`** — Markdown rule files (Continue 0.9+) injected into the system prompt
- **Context providers** — Named sources that feed context into the prompt (docs, codebase, files, URLs)

Continue is highly configurable and model-agnostic — you can point it at Claude, GPT-4, local models (Ollama), or any OpenAI-compatible endpoint.

---

## Setup Instructions

### Step 1: Install Continue

Install the Continue extension from the VS Code marketplace or JetBrains plugin marketplace.

### Step 2: Generate your config

```
Read core/UNIVERSAL_SPEC.md from this project.
Generate a Continue config.json and rules files following adapters/continue/ADAPTER.md.
```

### Step 3: File placement

```
.continue/
├── config.json          # Main configuration
└── rules/
    ├── project.md       # Project context and conventions
    ├── security.md      # Security hard constraints
    └── patterns.md      # Implementation patterns
```

---

## `config.json` Template

```json
{
  "models": [
    {
      "title": "Claude Sonnet (Primary)",
      "provider": "anthropic",
      "model": "claude-sonnet-4-5",
      "apiKey": "YOUR_ANTHROPIC_API_KEY",
      "contextLength": 200000,
      "systemMessage": "You are an expert software engineer working on [PROJECT_NAME]. Always read the project rules before starting any task."
    },
    {
      "title": "Claude Haiku (Fast)",
      "provider": "anthropic",
      "model": "claude-haiku-4-5-20251001",
      "apiKey": "YOUR_ANTHROPIC_API_KEY",
      "contextLength": 200000
    },
    {
      "title": "Local (Ollama)",
      "provider": "ollama",
      "model": "codellama:13b",
      "contextLength": 16384
    }
  ],

  "tabAutocompleteModel": {
    "title": "Autocomplete",
    "provider": "anthropic",
    "model": "claude-haiku-4-5-20251001",
    "apiKey": "YOUR_ANTHROPIC_API_KEY"
  },

  "contextProviders": [
    {
      "name": "code",
      "params": {}
    },
    {
      "name": "docs",
      "params": {
        "sites": [
          {
            "startUrl": "https://YOUR_DOCS_SITE.com/docs",
            "rootUrl": "https://YOUR_DOCS_SITE.com",
            "title": "[PROJECT_NAME] Docs"
          }
        ]
      }
    },
    {
      "name": "file",
      "params": {}
    },
    {
      "name": "folder",
      "params": {}
    },
    {
      "name": "terminal",
      "params": {}
    },
    {
      "name": "search",
      "params": {}
    },
    {
      "name": "url",
      "params": {}
    },
    {
      "name": "diff",
      "params": {}
    }
  ],

  "slashCommands": [
    {
      "name": "edit",
      "description": "Edit selected code"
    },
    {
      "name": "comment",
      "description": "Add comments to selected code"
    },
    {
      "name": "tests",
      "description": "Write tests for selected code",
      "prompt": "Write comprehensive tests for this code. Follow the testing conventions in .continue/rules/patterns.md. Include: happy path, validation failures, not found case, and error cases."
    },
    {
      "name": "endpoint",
      "description": "Implement a new API endpoint",
      "prompt": "Implement a new API endpoint following these steps:\n1. Define Zod schema in *.types.ts\n2. Implement service in *.service.ts\n3. Write controller in *.controller.ts\n4. Register in router\n5. Write tests\n6. Note what to update in docs/architecture.md\n\nFollow all conventions in .continue/rules/project.md."
    },
    {
      "name": "review",
      "description": "Review selected code for issues",
      "prompt": "Review this code for:\n1. Security issues (see .continue/rules/security.md)\n2. Convention violations (see .continue/rules/project.md)\n3. Missing error handling\n4. Missing tests\n5. Performance issues\n\nOutput a structured report: pass/warn/fail per check."
    },
    {
      "name": "docs",
      "description": "Generate or update documentation",
      "prompt": "Based on the selected code and recent git changes, update the relevant living documentation. Check what needs updating in: docs/architecture.md, docs/CHANGELOG.md, docs/projectstatus.md."
    },
    {
      "name": "fix",
      "description": "Fix the selected code or error",
      "prompt": "Diagnose and fix this issue. First explain the root cause, then implement the fix. Write a test that would catch this regression in future."
    }
  ],

  "rules": [
    ".continue/rules/project.md",
    ".continue/rules/security.md",
    ".continue/rules/patterns.md"
  ],

  "allowAnonymousTelemetry": false
}
```

---

## Rules Files

### `.continue/rules/project.md`

```markdown
# [PROJECT_NAME] — Project Context

**What it is:** [Elevator pitch]
**Stack:** [Frontend] · [Backend] · [Database] · [Cache] · [Auth] · [Cloud]
**Platforms:** [Web / Mobile / API]

## Repository layout
- `apps/web/` — [Web app]
- `apps/api/` — [API service]
- `apps/mobile/` — [Mobile app, if applicable]
- `packages/` — Shared types, utils, logger, config
- `docs/` — PRD, EDD, schema, living docs

## Key reference documents
Always read these before implementing a feature:
- `docs/EDD.md` — Technical spec with endpoint definitions
- `docs/schema.sql` — Database schema
- `docs/architecture.md` — Living service map

## Code conventions
- TypeScript strict mode — no `any`, absolute imports via tsconfig paths
- Zod schemas at every external boundary
- Never `console.log` — use `packages/logger`
- Never raw SQL — parameterised queries or ORM

## Naming
Files: kebab-case | Components: PascalCase | Variables: camelCase | Constants: SCREAMING_SNAKE

## Module structure (backend)
`feature.router.ts` → `feature.controller.ts` → `feature.service.ts` + `feature.types.ts` + `feature.test.ts`

## After any task
- Update `docs/projectstatus.md` — mark task ✅
- Add to `docs/CHANGELOG.md` under [Unreleased]
- Update `docs/architecture.md` if endpoints or schema changed
```

### `.continue/rules/security.md`

```markdown
# Security Rules — Always Enforce

- Never include secrets or credentials in code
- Validate all external input with Zod before use
- Check auth/authorisation before every data access
- Never raw SQL — parameterised queries only
- Never log: passwords, tokens, card data, PII
- HTTPS-only cookies: HttpOnly, Secure, SameSite=Strict
- bcrypt for passwords, cost factor ≥ 12
- Rate limit all authentication endpoints
```

### `.continue/rules/patterns.md`

```markdown
# Implementation Patterns

## API response format
```typescript
{ success: true, data: T }           // Success
{ success: false, error: { code: string, message: string } }  // Error
```

## Error codes
VALIDATION_ERROR · UNAUTHORISED · FORBIDDEN · NOT_FOUND · CONFLICT · INTERNAL_ERROR

## Test structure
```typescript
describe('feature', () => {
  describe('when condition', () => {
    it('should outcome', async () => {
      // Arrange / Act / Assert
    });
  });
});
```

## Required test scenarios
1. Happy path  2. Invalid input  3. Not found  4. Unauthenticated  5. Upstream failure
```

---

## Continue Usage Tips for Genesis Projects

**Reference docs with `@`:**
```
@file docs/EDD.md implement the endpoint in Section 5.2
@folder apps/api/src/auth show me how auth is implemented
@url https://docs.example.com/api what's the rate limit for this endpoint?
```

**Use custom slash commands:**
```
/endpoint — scaffolds a new endpoint end-to-end
/tests — writes comprehensive tests for selected code
/review — security and convention review
/docs — updates living documentation
```

**Model switching:**
Use Claude Sonnet for complex multi-file implementations. Switch to Claude Haiku (or local Ollama) for quick edits, renaming, and simple completions.

**Codebase context:**
Add `@codebase` to search across the whole repo:
```
@codebase where is rate limiting implemented?
```
