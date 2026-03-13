# Genesis Translator

**Translate your `CLAUDE.md` into native config files for Cursor, GitHub Copilot, Windsurf, and Aider — in one Claude conversation.**

---

## The Concept

Genesis projects are built around a `CLAUDE.md` — a 16-section master guide that tells Claude Code everything about the project. The translator takes that single source of truth and converts it into each other agent's native format, so any developer on the team can work with their preferred tool while following identical conventions.

```
CLAUDE.md  ──translator──►  .cursor/rules/*.mdc
           ──translator──►  .github/copilot-instructions.md
           ──translator──►  .windsurf/rules/*.md
           ──translator──►  .aider.conf.yml + CONVENTIONS.md
```

**One update, four targets.** When your conventions change, you update `CLAUDE.md` and re-run the translator. All agent configs regenerate from the same source.

---

## Files in This Directory

```
translator/
├── TRANSLATION_MAP.md          # The schema: how each CLAUDE.md section maps to each agent
├── TRANSLATOR_PROMPT.md        # Full translation prompt — paste to Claude with your CLAUDE.md
└── PARTIAL_RETRANSLATOR_PROMPT.md  # Re-run only affected files when sections change

examples/
└── saas-translated/
    ├── SOURCE_CLAUDE.md        # Example CLAUDE.md (PayFlow SaaS app)
    ├── cursor-output.md        # All 7 Cursor files generated from it
    ├── copilot-output.md       # All 5 Copilot files generated from it
    └── windsurf-aider-output.md # All 8 Windsurf + 2 Aider files generated from it
```

---

## How to Use

### Full translation (first time)

1. Open a new Claude conversation
2. Open `translator/TRANSLATOR_PROMPT.md` — copy its contents
3. Paste your project's `CLAUDE.md` at the end
4. Send — Claude generates all ~20 agent config files in one pass
5. Save each file to its indicated path in your repo

### Partial re-translation (after CLAUDE.md updates)

When you update specific sections of `CLAUDE.md`:

1. Check `translator/PARTIAL_RETRANSLATOR_PROMPT.md` — find which agent files are affected
2. Open a new Claude conversation, paste the partial prompt
3. Tell Claude which sections changed and paste the updated content
4. Claude regenerates only the affected files

### After translation — what to commit

```bash
git add .cursor/ .github/copilot-instructions.md .github/instructions/ \
        .github/copilot-setup-steps.yml .windsurf/ .windsurfrules \
        .aider.conf.yml CONVENTIONS.md
git commit -m "chore: sync agent configs from CLAUDE.md"
```

---

## What the Translator Does (and Doesn't)

### What it translates
Every section of CLAUDE.md has a defined mapping:
- Project identity, stack, and architecture → project context rules
- Code conventions and naming → always-on conventions
- Security constraints → dedicated security rules (always-on in every agent)
- Design system → frontend-scoped rules
- API patterns → backend-scoped rules
- Testing → test-scoped rules
- Living docs protocol → post-task reminders in every agent

### What it extracts from plugins/commands/agents (§14–16)
Claude Code's plugin and agent system has no direct equivalent in other agents. The translator extracts the *intent* and inlines it:
- `frontend-design` plugin → design system rules in frontend files
- `dev-streamline` plugin → module templates inlined into conventions
- `security` agent checks → security rules file
- `api` agent implementation order → api-patterns file
- `/commit` command → Aider's `commit-prompt`, git conventions for others
- `/update-docs` command → post-task doc update protocol in all agents

### What gets dropped
Claude Code-specific constructs with no equivalent:
- Plugin file references (other agents don't have a plugin system)
- Slash command definitions (`/commit`, `/new-endpoint` etc.)
- Subagent delegation syntax
- MCP server configuration

---

## Condensation Levels by Agent

Different agents have different effective context windows for instructions:

| Agent | Detail level | Reason |
|-------|-------------|--------|
| Windsurf | Full detail | Cascade has large context; numbered files load in order |
| Aider | Full detail | CONVENTIONS.md is the whole reference; Aider reads it cover to cover |
| Cursor | Medium | Rules are injected per-interaction; scoped to file globs |
| Copilot | Condensed | copilot-instructions.md capped at ~80 lines for effectiveness |

---

## Keeping Everything in Sync

### The sync problem
With four agent config sets, drift is inevitable unless there's discipline. The translator solves this by making `CLAUDE.md` the only file you manually edit.

### Recommended workflow
1. **All convention changes go to `CLAUDE.md` first**
2. **Run the partial re-translator** with the changed sections
3. **Commit everything together** in a single `chore: sync agent configs` commit
4. **Never edit agent config files directly** — they are generated outputs

### Optional: GitHub Action for sync reminders

```yaml
# .github/workflows/conventions-sync-reminder.yml
name: Convention sync reminder

on:
  push:
    paths:
      - 'CLAUDE.md'

jobs:
  remind:
    runs-on: ubuntu-latest
    steps:
      - name: Comment on commit
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.repos.createCommitComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              commit_sha: context.sha,
              body: '⚠️ CLAUDE.md was updated. Run the Genesis Partial Translator to sync agent configs (.cursor/, .windsurf/, copilot-instructions.md, CONVENTIONS.md).'
            })
```

---

## Extending the Translator

To add support for a new agent:

1. Add a section to `TRANSLATION_MAP.md` with the section-by-section mapping
2. Add the new agent to the output format block in `TRANSLATOR_PROMPT.md`
3. Add the new agent's affected files to the section→files table in `PARTIAL_RETRANSLATOR_PROMPT.md`
4. Add an example output file in `examples/saas-translated/`

See the existing agent mappings in `TRANSLATION_MAP.md` as templates.
