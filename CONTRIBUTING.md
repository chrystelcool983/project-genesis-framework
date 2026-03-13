# Contributing to Project Genesis Framework

Thank you for helping make this better. Every improvement here benefits every project that uses it.

---

## Quick Start

1. Fork the repo
2. Create a branch: `git checkout -b feature/your-improvement`
3. Make your changes (see guidelines below)
4. Submit a PR using the template

---

## What to Contribute

### 🔌 New MCP Entry (Phase 9)

The MCP ecosystem is growing fast. If you've used an MCP that's missing from the catalogue, add it.

A good MCP entry includes:
- **One-line description** of what it does
- **Best for** — which stack or platform it suits
- **Use when** — the specific trigger scenario (not vague)
- **Example config snippet** — working JSON with placeholder values

Place it in the correct category in Phase 9.1 (`Core`, `Web App`, `Mobile App`, `Cloud`, or `Domain-Specific`).

---

### 🤖 New Agent (Phase 8)

Agents should be focused, not catch-alls. A good agent contribution:
- Has a **single clear domain** (not "general quality")
- Has **4–8 checklist items** that are specific and produce concrete outputs
- Has a **clear "when to invoke"** condition — what type of task triggers this agent?
- Has been **used on a real project** (please note if it's theoretical)

Place it in the correct section: 8.1 Standard, 8.2 Web App, 8.3 Mobile App, or 8.4 Project-Specific.

---

### 📖 New Example (examples/)

Real-world setups are the most valuable contribution. A good example:
- Covers a **real project type** (anonymised is fine)
- Includes a **complete `mcp-config.json`** for that stack
- Explains **key decisions and why** — this is the valuable part
- Notes which agents were used and why others were skipped

Copy the structure from `examples/saas-web-app/` or `examples/mobile-consumer-app/`.

---

### 🐛 Bug / Clarity Fix

If a section is unclear, wrong, or missing something obvious:
- Open an issue with the `[BUG]` template first for discussion
- Or just submit a PR for minor typo/clarity fixes directly

---

### 💡 Phase Improvement

If you have better intake questions, improved plugin templates, or a smarter phase structure:
- Start a [Discussion](../../discussions) first to get feedback before a large PR
- Large structural changes need consensus before implementation

---

## Formatting Guidelines

The framework uses a consistent style. Please match it:

- **Phases** start with a `>` Purpose block and a `> Dependency` block
- **Checklists** use `□` not `-` or `*`
- **Agent specs** use backtick code blocks with `□` items inside
- **MCP descriptions** include: what it does · best for · use when
- **Tone** is direct, specific, and actionable — no vague adjectives

---

## What Doesn't Belong Here

- **Completed CLAUDE.md files** — those are project-specific, not framework additions
- **Generic "best practices"** not tied to a specific phase or artifact
- **Tool recommendations** without MCP integration (use Discussions instead)
- **Advertising** — this is a framework, not a directory

---

## Code of Conduct

Be respectful. Disagree on ideas, not people. This is a collaborative project.

---

*Questions? Open a Discussion rather than an issue — discussions are for conversations, issues are for concrete problems.*
