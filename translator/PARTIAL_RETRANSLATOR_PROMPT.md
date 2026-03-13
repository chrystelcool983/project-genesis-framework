# Genesis Partial Re-Translator Prompt

> **Use this when:** You've updated specific sections of your `CLAUDE.md` and need to 
> regenerate only the affected agent config files — not the full translation.
>
> This is faster and avoids overwriting agent files that haven't changed.

---

## How to use

1. Open a new Claude conversation
2. Paste this file
3. Tell Claude which CLAUDE.md sections changed (see section map below)
4. Paste the updated CLAUDE.md sections
5. Claude will output only the target files that need updating

---

## Section → Affected Agent Files Map

Use this to know which files to regenerate when a section changes.

| CLAUDE.md Section changed | Regenerate these files |
|---------------------------|----------------------|
| §1 Project Overview | `.cursor/rules/01-project-context.mdc` · `.github/copilot-instructions.md` · `.windsurf/rules/01-project-context.md` · `CONVENTIONS.md` (§1) |
| §2 Architecture | `.cursor/rules/01-project-context.mdc` · `.github/copilot-instructions.md` · `.windsurf/rules/01-project-context.md` · `CONVENTIONS.md` (§2) |
| §4 Tech Stack | `.cursor/rules/01-project-context.mdc` · `.github/copilot-instructions.md` · `.windsurf/rules/01-project-context.md` · `CONVENTIONS.md` (§1) · `.aider.conf.yml` (model block) |
| §5 Design System | `.cursor/rules/03-frontend-patterns.mdc` · `.github/instructions/frontend.instructions.md` · `.windsurf/rules/04-frontend-patterns.md` |
| §6 Code Conventions | `.cursor/rules/02-code-conventions.mdc` · `.cursorrules` · `.github/copilot-instructions.md` · `.github/instructions/typescript.instructions.md` · `.windsurf/rules/02-code-conventions.md` · `.windsurfrules` · `CONVENTIONS.md` (§3) |
| §7 Repo Etiquette | `.cursor/rules/02-code-conventions.mdc` · `.github/copilot-instructions.md` · `.windsurf/rules/02-code-conventions.md` · `CONVENTIONS.md` (§4) · `.aider.conf.yml` (commit-prompt) |
| §8 Security | `.cursor/rules/05-security.mdc` · `.cursorrules` · `.github/copilot-instructions.md` · `.windsurf/rules/03-security.md` · `.windsurfrules` · `CONVENTIONS.md` (§5) |
| §8 Performance | `.cursor/rules/04-api-patterns.mdc` · `.windsurf/rules/05-api-patterns.md` · `CONVENTIONS.md` (§8) |
| §9 Testing | `.cursor/rules/06-testing-patterns.mdc` · `.github/instructions/testing.instructions.md` · `.windsurf/rules/06-testing-patterns.md` · `CONVENTIONS.md` (§6) · `.aider.conf.yml` (test-cmd) |
| §10 Dev Setup | `.github/copilot-setup-steps.yml` · `CONVENTIONS.md` (§7) |
| §12 Common Tasks | `.cursor/rules/04-api-patterns.mdc` · `.cursor/rules/03-frontend-patterns.mdc` · `.github/instructions/api.instructions.md` · `.windsurf/rules/05-api-patterns.md` · `CONVENTIONS.md` (§8) |
| §13 Living Docs | `.cursor/rules/02-code-conventions.mdc` · `.github/copilot-instructions.md` · `.windsurf/rules/07-living-docs.md` · `CONVENTIONS.md` (§9) |
| §14 Plugins (updated) | Re-extract relevant plugin content into all api/frontend/testing rules files |
| §15 Subagents (updated) | Re-extract security/frontend/api agent rules into relevant rules files |

---

## Partial Re-Translation Prompt

```
You are the Genesis Framework Partial Translator. 

I've updated specific sections of my project's CLAUDE.md and need to regenerate 
only the affected agent config files for: Cursor, GitHub Copilot, Windsurf, and Aider.

## Sections I changed:
[LIST THE SECTION NUMBERS AND NAMES, e.g. "§6 Code Conventions, §8 Security"]

## Files to regenerate (from the section→files map):
[LIST THE EXACT FILES THAT NEED UPDATING]

## Translation rules:
- Same as the full translator: faithful translation, no invented content
- Security rules always get full detail
- Drop ASCII diagrams, plugin/command/MCP references
- Extract plugin intent inline (patterns, constraints, templates)

## Updated section content:

### §[N] [Section Name]
[PASTE THE UPDATED SECTION CONTENT FROM YOUR CLAUDE.md]

### §[N] [Section Name]  
[PASTE THE UPDATED SECTION CONTENT]

---

Generate only the files listed above. Use the same format as the full translator:
markdown header with the exact save path, full file content in triple backticks.

After generating, print the sync reminder with today's date and which files were updated.
```
