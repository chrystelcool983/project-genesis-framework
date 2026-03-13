# Genesis Translator Prompt

> **How to use this:**
> 1. Open a new Claude conversation
> 2. Paste this entire file
> 3. Then paste your project's `CLAUDE.md` below it
> 4. Claude will generate all four agent config sets in one pass
>
> **What you get:** Ready-to-save config files for Cursor, GitHub Copilot, Windsurf, and Aider — 
> all derived from your single `CLAUDE.md` source of truth.

---

## Paste this prompt to Claude:

```
You are the Genesis Framework Translator. Your job is to take a project's CLAUDE.md 
file (the Claude Code master guide) and translate it into native config files for 
four AI coding agents: Cursor, GitHub Copilot, Windsurf, and Aider.

I will provide my CLAUDE.md below. Follow these translation rules precisely.

---

## TRANSLATION RULES

### General principles
- CLAUDE.md is the single source of truth. Never invent content not in it.
- Translate faithfully — same rules, same constraints, same patterns — just reformatted 
  for each agent's native system.
- Apply the condensation hierarchy: security rules always get full detail; ASCII diagrams 
  always get dropped.
- Sections 14 (Plugins), 15 (Commands/Subagents), and 16 (MCP Servers) are Claude Code-specific.
  Extract the *intent* (patterns, constraints) and inline them into the agent rules.
  Do not reference Claude plugins, slash commands, or MCP by name in other agent files.

### What to extract from Plugins (§14) when translating
- frontend-design plugin → extract colour variables, spacing scale, component rules 
  → include in Cursor `frontend-patterns.mdc`, Windsurf `04-frontend-patterns.md`, 
    Copilot `frontend.instructions.md`
- dev-streamline plugin → extract module structure, code templates, error patterns 
  → include in all agents' core convention files
- compound-engineering plugin → extract implementation order, cache pattern, 
  cross-service rules → include in api-patterns files
- testing-qa plugin → extract test fixtures, required scenarios, test user definitions 
  → include in all agents' testing files
- domain-context plugin → extract domain rules, data formats, third-party quirks 
  → include in all agents' project context files

### What to extract from Commands (§15) when translating
- /commit command → translate to Aider `commit-prompt` in `.aider.conf.yml`
- /update-docs command → translate to a "after every task" rule block in all agents
- /new-endpoint command → translate to the endpoint implementation pattern in api-patterns files
- /deploy-check command → translate to a pre-commit checklist in conventions files
- /sprint-status, /create-issue → Cursor/Copilot users use GitHub natively; add a note
  pointing to projectstatus.md

### What to extract from Subagents (§15) when translating
- security agent → its checks become the security rules file for all agents
- frontend agent → its constraints become the frontend rules file
- api agent → its implementation order becomes the api-patterns file
- docs agent → its protocol becomes the "after every task" block

---

## OUTPUT FORMAT

Generate files in this exact order. Use a markdown header for each file showing its 
exact save path. Output the complete file content between triple backticks.

---

## CURSOR OUTPUT (5–7 files)

### File: `.cursor/rules/01-project-context.mdc`
[full file content]

### File: `.cursor/rules/02-code-conventions.mdc`
[full file content]

### File: `.cursor/rules/03-frontend-patterns.mdc`
[full file content]

### File: `.cursor/rules/04-api-patterns.mdc`
[full file content]

### File: `.cursor/rules/05-security.mdc`
[full file content]

### File: `.cursor/rules/06-testing-patterns.mdc`
[full file content]

### File: `.cursorrules`
[condensed fallback, max 80 lines]

---

## COPILOT OUTPUT (4–6 files)

### File: `.github/copilot-instructions.md`
[max 80 lines, imperative voice, no prose]

### File: `.github/instructions/typescript.instructions.md`
[max 25 lines]

### File: `.github/instructions/api.instructions.md`
[max 25 lines]

### File: `.github/instructions/frontend.instructions.md`
[max 25 lines]

### File: `.github/instructions/testing.instructions.md`
[max 25 lines]

### File: `.github/copilot-setup-steps.yml`
[environment bootstrap steps from §10 Dev Setup]

---

## WINDSURF OUTPUT (7–8 files)

### File: `.windsurf/rules/01-project-context.md`
[full file content]

### File: `.windsurf/rules/02-code-conventions.md`
[full file content]

### File: `.windsurf/rules/03-security.md`
[full file content]

### File: `.windsurf/rules/04-frontend-patterns.md`
[full file content]

### File: `.windsurf/rules/05-api-patterns.md`
[full file content]

### File: `.windsurf/rules/06-testing-patterns.md`
[full file content]

### File: `.windsurf/rules/07-living-docs.md`
[full file content]

### File: `.windsurfrules`
[condensed fallback, max 80 lines]

---

## AIDER OUTPUT (2 files)

### File: `.aider.conf.yml`
[full configuration file]

### File: `CONVENTIONS.md`
[full conventions file, comprehensive but scannable]

---

## AFTER GENERATING

Print this summary table:

| Agent | Files generated | Lines total | Key sections included |
|-------|----------------|-------------|----------------------|
| Cursor | X | ~Y | list |
| Copilot | X | ~Y | list |
| Windsurf | X | ~Y | list |
| Aider | X | ~Y | list |

Then print this maintenance reminder:

> SYNC REMINDER: These files were generated from CLAUDE.md on [today's date].
> When you update CLAUDE.md, re-run the Genesis Translator to keep all agent 
> configs in sync. Changed sections: update only the affected target files.

---

## MY CLAUDE.md IS BELOW:

[PASTE YOUR CLAUDE.md HERE]
```
