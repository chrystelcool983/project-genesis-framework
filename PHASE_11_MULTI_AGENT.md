# Phase 11: Multi-Agent Support

> **Purpose:** Make the Genesis project setup portable across AI coding agents — so any team member can work with their preferred tool, and the project conventions are consistent regardless of agent.
>
> **Dependency:** `core/UNIVERSAL_SPEC.md` must be filled in with your project details first. All adapters read from this file.

---

## 11.1 The Universal Spec

**Generate `core/UNIVERSAL_SPEC.md` with:**

```
□ Project identity (name, elevator pitch, problem, business model, deadline)
□ Platform declaration (web, mobile iOS, mobile Android, API — yes/no + framework)
□ Tech stack table (every layer, technology, and pinned version)
□ Repository layout (directory tree with descriptions)
□ Key reference documents table (document, path, when to read it)
□ Code conventions (language, file naming, variable naming, import rules)
□ Git conventions (branch naming, commit format, PR rules)
□ Design system summary (colours, typography, spacing, accessibility target)
□ Current sprint task table (task, priority, status, owner)
□ Hard constraints list (non-negotiable rules for all agents)
□ Agent adapter index (table of all supported agents and their config files)
```

This is the **single source of truth** for all adapters. Update it when conventions change.

---

## 11.2 Agent Adapters

Generate adapter files for each AI coding agent your team uses.

### Supported Agents

| Agent | Config files | Strengths | Best for |
|-------|-------------|-----------|----------|
| **Claude Code** | `.claude/` + `CLAUDE.md` | Deep context, plugins, subagents, MCP integration | Complex multi-phase features, living doc maintenance |
| **Cursor** | `.cursor/rules/*.mdc` + `.cursorrules` | In-editor rules, file-scoped context, Composer | Rapid iteration, single-file and small multi-file edits |
| **GitHub Copilot** | `.github/copilot-instructions.md` + `instructions/*.instructions.md` | IDE integration, Codex cloud tasks, PR context | Code completion, quick inline suggestions, PR-linked tasks |
| **Windsurf** | `.windsurf/rules/*.md` + `.windsurfrules` | Large context window, Flows automation | Large multi-file features, codebase-wide refactors |
| **Aider** | `.aider.conf.yml` + `CONVENTIONS.md` | Terminal-native, git-native, precise diffs | Focused edits, scripted workflows, CI-integrated tasks |
| **OpenHands** | `.openhands/microagents/*.md` | Fully autonomous, full environment access | Autonomous multi-step tasks, end-to-end feature implementation |
| **Continue** | `.continue/config.json` + `rules/*.md` | Model-agnostic, customisable, open-source | Teams with model preference flexibility, local model usage |

### Generate adapter files

For each agent your team uses, run:

```
Read core/UNIVERSAL_SPEC.md and adapters/[AGENT]/ADAPTER.md.
Generate the production config files for [AGENT] based on my project spec.
Output each file with its exact path.
```

---

## 11.3 File Placement

After generating, your repo should have:

```
/
├── core/
│   └── UNIVERSAL_SPEC.md              # Universal project spec (all agents read this)
├── adapters/
│   ├── cursor/ADAPTER.md              # How-to guide for Cursor setup
│   ├── copilot/ADAPTER.md             # How-to guide for Copilot setup
│   ├── windsurf/ADAPTER.md            # How-to guide for Windsurf setup
│   ├── aider/ADAPTER.md               # How-to guide for Aider setup
│   ├── openhands/ADAPTER.md           # How-to guide for OpenHands setup
│   └── continue/ADAPTER.md            # How-to guide for Continue setup
│
# Generated config files (from adapter instructions):
├── .cursor/rules/*.mdc                # Cursor rules
├── .cursorrules                       # Cursor legacy fallback
├── .github/copilot-instructions.md   # Copilot primary instructions
├── .github/instructions/*.md          # Copilot scoped instructions
├── .windsurf/rules/*.md               # Windsurf rules
├── .windsurfrules                     # Windsurf legacy fallback
├── .aider.conf.yml                    # Aider config
├── CONVENTIONS.md                     # Aider conventions (also human-readable!)
├── .openhands/microagents/*.md        # OpenHands microagents
├── .continue/config.json              # Continue config
├── .continue/rules/*.md               # Continue rules
└── .claude/                           # Claude Code tooling (from Phase 8-9)
```

---

## 11.4 Keeping Adapters in Sync

When project conventions change (new naming rule, new module pattern, new constraint):

1. Update `core/UNIVERSAL_SPEC.md` first
2. Run the regeneration prompt for each affected adapter
3. Commit all adapter changes together

**Automation tip:** Add a GitHub Action that detects changes to `core/UNIVERSAL_SPEC.md` and opens a PR reminder to update adapters.

---

## 11.5 Multi-Agent Artifact Inventory

| # | Artifact | Location | Format |
|---|----------|----------|--------|
| A1 | Universal Spec | `core/UNIVERSAL_SPEC.md` | MD |
| A2 | Cursor Rules | `.cursor/rules/*.mdc` | MDC |
| A3 | Cursor Legacy | `.cursorrules` | Text |
| A4 | Copilot Instructions | `.github/copilot-instructions.md` | MD |
| A5 | Copilot Scoped | `.github/instructions/*.instructions.md` | MD |
| A6 | Copilot Codex Setup | `.github/copilot-setup-steps.yml` | YAML |
| A7 | Windsurf Rules | `.windsurf/rules/*.md` | MD |
| A8 | Windsurf Legacy | `.windsurfrules` | Text |
| A9 | Aider Config | `.aider.conf.yml` | YAML |
| A10 | Aider Conventions | `CONVENTIONS.md` | MD |
| A11 | OpenHands Repo Agent | `.openhands/microagents/repo.md` | MD |
| A12 | OpenHands Task Agents | `.openhands/microagents/*.md` | MD |
| A13 | Continue Config | `.continue/config.json` | JSON |
| A14 | Continue Rules | `.continue/rules/*.md` | MD |

**Total: 14 additional artifacts for full multi-agent support**
