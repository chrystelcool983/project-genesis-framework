# Genesis Translation Map

> This document defines exactly how every section of a project's `CLAUDE.md` 
> maps to each target agent's native format.
>
> **This is the schema the translator prompt uses.** It is not meant to be 
> read by humans during normal development — it is the instruction set for 
> Claude when running a translation task.

---

## Source Format: CLAUDE.md

A Genesis `CLAUDE.md` has 16 canonical sections:

| Section | Name | Content |
|---------|------|---------|
| 1 | Project Overview | Product description, goals, personas, business context |
| 2 | Architecture Overview | ASCII diagram, monorepo tree, service table, data flow, design principles |
| 3 | Reference Documents | Links to PRD, EDD, schema, risks, .env.example |
| 4 | Technology Stack | Backend, frontend, infra tables with pinned versions |
| 5 | Design System | Colours, typography, spacing, components, animations, accessibility |
| 6 | Code Conventions | Naming, module structure, typing rules, logging, error handling |
| 7 | Repository Etiquette | Branch naming, commit format, PR process, review checklist |
| 8 | Constraints & Policies | Performance, security, privacy, platform-specific constraints |
| 9 | Testing Instructions | How to run tests, coverage targets, fixtures, required scenarios |
| 10 | Local Development | Prerequisites, setup steps, verification commands |
| 11 | Deployment | Environments, deploy process, env var management |
| 12 | Common Tasks | Step-by-step patterns for recurring dev tasks |
| 13 | Living Docs Rules | What to update, when, how — the post-task update protocol |
| 14 | Plugins | Plugin table, usage rules, interaction model |
| 15 | Slash Commands & Subagents | Commands table, agents table |
| 16 | MCP Servers | Configured servers, setup instructions |

---

## Translation Rules Per Agent

### → Cursor

**Output files:**
- `.cursor/rules/` — one `.mdc` file per rule group
- `.cursorrules` — condensed single-file fallback

**Section mapping:**

| CLAUDE.md Section | Cursor Output | File | Notes |
|-------------------|---------------|------|-------|
| §1 Project Overview | Project identity block | `01-project-context.mdc` | alwaysApply: true |
| §2 Architecture | Repo layout + service table | `01-project-context.mdc` | Include directory tree |
| §3 Reference Docs | "Read before implementing" block | `01-project-context.mdc` | List doc paths and when to read each |
| §4 Tech Stack | Stack summary | `01-project-context.mdc` | Pinned versions |
| §5 Design System | Component + colour + spacing rules | `03-frontend-patterns.mdc` | alwaysApply: false, globs: apps/web/** |
| §6 Code Conventions | All naming + import + error rules | `02-code-conventions.mdc` | alwaysApply: true |
| §7 Repo Etiquette | Branch + commit + PR rules | `02-code-conventions.mdc` | Add to bottom of conventions file |
| §8 Constraints §8 Security | Security rules block | `05-security.mdc` | alwaysApply: true — security is always-on |
| §8 Constraints §8 Performance | Performance constraints | `04-api-patterns.mdc` | Inline as constraints block |
| §9 Testing | Test structure + coverage + scenarios | `06-testing-patterns.mdc` | alwaysApply: false, globs: **/*.test.* |
| §12 Common Tasks §12 Endpoint | Endpoint scaffold pattern | `04-api-patterns.mdc` | alwaysApply: false, globs: apps/api/** |
| §12 Common Tasks §12 Frontend | Frontend patterns | `03-frontend-patterns.mdc` | alwaysApply: false, globs: apps/web/** |
| §13 Living Docs Rules | Post-task doc update rule | `02-code-conventions.mdc` | Add as "After every task" block |
| §14–§16 Plugins/Commands/MCP | **Drop** — Cursor has no equivalent | — | These are Claude Code-specific |

**Condensation rules for Cursor:**
- Remove ASCII architecture diagrams — Cursor doesn't render them usefully
- Collapse the 16-section CLAUDE.md into 5–7 focused `.mdc` files
- Security rules always-on; feature rules scoped to file globs
- `.cursorrules` fallback = §1 identity + §6 naming + §8 security + §13 post-task protocol, max ~60 lines

---

### → GitHub Copilot

**Output files:**
- `.github/copilot-instructions.md` — primary file, applies to all Copilot Chat
- `.github/instructions/[scope].instructions.md` — per-filetype scoped files
- `.github/copilot-setup-steps.yml` — Codex cloud agent environment bootstrap

**Section mapping:**

| CLAUDE.md Section | Copilot Output | File | Notes |
|-------------------|----------------|------|-------|
| §1 Project Overview | Project identity + platform summary | `copilot-instructions.md` | First block, max 5 lines |
| §4 Tech Stack | Stack line (one line) | `copilot-instructions.md` | Condensed — not a full table |
| §2 Architecture | Repo layout (directory list) | `copilot-instructions.md` | 5–8 lines |
| §3 Reference Docs | "Key docs" list | `copilot-instructions.md` | Path + one-line description |
| §6 Code Conventions | Condensed rule list | `copilot-instructions.md` | Bulleted, not prose |
| §7 Repo Etiquette | Branch + commit format | `copilot-instructions.md` | 3–4 lines |
| §8 Security | Security must-never list | `copilot-instructions.md` | Hard rules, imperative voice |
| §13 Living Docs | Post-task reminder | `copilot-instructions.md` | Last block, 3–4 lines |
| §5 Design System | Component + colour rules | `instructions/frontend.instructions.md` | applyTo: apps/web/**/*.tsx |
| §6 TypeScript rules | TS-specific rules | `instructions/typescript.instructions.md` | applyTo: **/*.ts,**/*.tsx |
| §9 Testing | Test structure + scenarios | `instructions/testing.instructions.md` | applyTo: **/*.test.* |
| §12 Endpoint pattern | Endpoint implementation order | `instructions/api.instructions.md` | applyTo: apps/api/** |
| §10 Dev Setup | Environment bootstrap steps | `copilot-setup-steps.yml` | install → env → db → seed |
| §14–§16 Plugins/Commands/MCP | **Drop** — no Copilot equivalent | — | Claude Code-specific |

**Condensation rules for Copilot:**
- `copilot-instructions.md` must be ≤ 80 lines — Copilot's effective context window for instructions is short
- No prose explanations — every line is a direct rule in imperative voice
- Scoped `.instructions.md` files should each be ≤ 25 lines
- No ASCII art, no tables — plain bullet points only

---

### → Windsurf

**Output files:**
- `.windsurf/rules/` — numbered `.md` files (processed in filename order)
- `.windsurfrules` — single-file fallback

**Section mapping:**

| CLAUDE.md Section | Windsurf Output | File | Notes |
|-------------------|-----------------|------|-------|
| §1 Project Overview | Project identity block | `01-project-context.md` | Full paragraph, not bullets |
| §2 Architecture | Repo tree + service table | `01-project-context.md` | Include directory tree |
| §3 Reference Docs | Docs table with read-triggers | `01-project-context.md` | "Read when..." column |
| §4 Tech Stack | Full stack table | `01-project-context.md` | Keep versions pinned |
| §6 Code Conventions | Full naming table + module anatomy | `02-code-conventions.md` | Include forbidden patterns section |
| §7 Repo Etiquette | Branch + commit + PR rules | `02-code-conventions.md` | Bottom of conventions file |
| §8 Security | Security rules | `03-security.md` | Separate file — security always loads |
| §5 Design System | CSS vars + component patterns | `04-frontend-patterns.md` | Include colour system with code |
| §12 Endpoint pattern | Full implementation pattern | `05-api-patterns.md` | Include response envelope + error codes |
| §9 Testing | Test structure + required scenarios | `06-testing-patterns.md` | Include coverage targets |
| §13 Living Docs | Update protocol table | `07-living-docs.md` | When/what to update per event |
| §14–§16 Plugins/Commands/MCP | **Drop** | — | Claude Code-specific |

**Condensation rules for Windsurf:**
- Windsurf Cascade has a large context window — keep more detail than Cursor/Copilot
- Numbered files ensure load order (context before patterns before security)
- Include code snippets inline — Cascade can use them as reference implementations
- `.windsurfrules` fallback = §1 + §6 naming + §8 security + §13 post-task, ~80 lines

---

### → Aider

**Output files:**
- `.aider.conf.yml` — model, auto-commit, lint, and `--read` config
- `CONVENTIONS.md` — human-readable project conventions (loaded every session via `--read`)

**Section mapping:**

| CLAUDE.md Section | Aider Output | File | Notes |
|-------------------|--------------|------|-------|
| §1 Project Overview | Header + elevator pitch + stack | `CONVENTIONS.md` §1 | First thing in the file |
| §2 Architecture | Repo tree | `CONVENTIONS.md` §2 | Aider uses this for file routing decisions |
| §3 Reference Docs | Key docs list with --read hints | `CONVENTIONS.md` §2 | Suggest which to `/read` for which tasks |
| §4 Tech Stack | Stack summary (not full table) | `CONVENTIONS.md` §1 | Keep it concise |
| §6 Code Conventions | Full rules section | `CONVENTIONS.md` §3 | Prose + code snippets for patterns |
| §7 Repo Etiquette | Branch + commit config | `CONVENTIONS.md` §4 + `.aider.conf.yml` `commit-prompt` | commit-prompt in YAML, human rules in MD |
| §8 Security | Security rules | `CONVENTIONS.md` §5 | Hard rules, non-negotiable |
| §9 Testing | Test structure + commands | `CONVENTIONS.md` §6 | Include `pnpm test [path]` commands |
| §10 Dev Setup | Verification commands | `CONVENTIONS.md` §7 | What to run to confirm setup works |
| §12 Common Tasks | Implementation patterns | `CONVENTIONS.md` §8 | Step-by-step, reference for complex tasks |
| §13 Living Docs | Post-task doc update steps | `CONVENTIONS.md` §9 | Last section — reinforce after every task |
| §4 Tech Stack (model hint) | Model selection | `.aider.conf.yml` `model` | claude-sonnet-4-5 default |
| §9 Testing (commands) | test-cmd + lint-cmd | `.aider.conf.yml` | Wire up pnpm test + pnpm lint |
| §3 Reference Docs | read list | `.aider.conf.yml` `read` | List CONVENTIONS.md + any always-needed docs |
| §14–§16 Plugins/Commands/MCP | **Drop** | — | Claude Code-specific |

**Condensation rules for Aider:**
- `CONVENTIONS.md` is the main file — it should be comprehensive but scannable
- Use headers so Aider can locate sections by name
- Include exact shell commands (not pseudocode) — Aider runs them literally
- `.aider.conf.yml` should wire up linting and testing so bad code fails fast
- Cross-reference key EDD/schema docs in `read:` so they load automatically

---

## What Gets Dropped in All Translations

These CLAUDE.md sections are Claude Code-specific and have no meaningful equivalent in other agents:

| Section | Reason dropped |
|---------|---------------|
| §14 Plugins | Claude Code's plugin/skill file system is unique |
| §15 Slash Commands | `/commit`, `/new-endpoint` etc. are Claude Code commands |
| §15 Subagents | Claude's subagent delegation has no direct equivalent |
| §16 MCP Servers | MCP is Claude Code's infrastructure connection layer |

The **intent** behind these sections is preserved through:
- Patterns from plugins → inlined into agent rule files
- Commands → translated to Aider `/run` workflows or Windsurf Flows prompts
- Agent specialisations → translated to scoped instruction files
- MCP context → translated to `--read` references (Aider) or `@file` hints (Cursor/Copilot)

---

## Condensation Hierarchy

When content must be shortened, apply this priority order:

1. **Keep:** Security constraints (§8 security) — always full detail
2. **Keep:** Naming conventions (§6) — agents get this wrong without explicit rules
3. **Keep:** Post-task doc update protocol (§13) — critical for living docs
4. **Compress:** Architecture overview — keep directory tree, drop ASCII diagram
5. **Compress:** Design system — keep colour variables and spacing scale; drop animation details
6. **Compress:** Tech stack — keep names and versions; drop rationale prose
7. **Drop:** ASCII diagrams — not rendered usefully by any of these agents
8. **Drop:** Plugin/command/MCP sections — Claude Code-specific
