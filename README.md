# 🚀 Project Genesis Framework

**The complete AI-assisted project setup system for Claude Code.**

Give this framework to Claude at the start of any software project. It walks you through 10 phases — from discovery questions to a fully scaffolded, production-ready codebase — generating every document, config file, and AI development tool along the way.

> Built on the principle: *Plan thoroughly, build confidently, iterate continuously.*

---

## What It Produces

By the end of a Genesis session, you'll have **44 artifacts** ready to go:

- 📋 **Strategic docs** — PRD, EDD, Constraints & Edge Cases register
- 🗃️ **Technical foundation** — Database schema, environment config, stack decisions
- 🏗️ **Project scaffold** — Full monorepo structure, service boilerplate, CI/CD workflows
- 🤖 **AI dev tooling** — CLAUDE.md, plugins, slash commands, subagents, MCP config
- 📊 **Living documentation** — Architecture map, changelog, project status dashboard

---

## How to Use It

1. **Copy** the entire `Project_Genesis_Framework.md` into a Claude conversation
2. **Add** a brief description of your project
3. **Paste** this starter prompt:

```
I'm starting a new software project. I've attached my Project Genesis Framework 
which outlines every artifact I need you to help me create.

Here's my project idea:
[Describe your project — what it does, who it's for, what problem it solves]

Walk me through the framework phase by phase. Ask me the intake questions first, 
then generate each artifact in order. Don't skip any phase.
```

4. Claude will ask discovery questions, then generate each artifact in dependency order.

---

## Framework Phases

| Phase | Name | What It Builds |
|-------|------|----------------|
| 0 | Project Intake | Discovery questions across product, technical, process, and design dimensions |
| 1 | Strategic Documents | PRD, EDD, Constraints & Edge Cases register |
| 2 | Technical Foundation | Tech stack decisions, database schema, environment config |
| 3 | Project Structure | Monorepo scaffold, service boilerplate, CI/CD workflows |
| 4 | CLAUDE.md | The master AI dev guide — 16 sections covering everything Claude Code needs |
| 5 | Living Documentation | architecture.md, CHANGELOG.md, projectstatus.md |
| 6 | Plugins | Skill files for frontend design, backend patterns, testing, domain context |
| 7 | Slash Commands | `/commit`, `/update-docs`, `/create-issue`, `/new-endpoint`, `/sprint-status`, `/deploy-check` |
| 8 | Subagents | Specialist agents for API, frontend, security, docs, retro — plus web & mobile agents |
| 9 | MCP Configuration | Claude Code infrastructure connections with annotated examples |
| 10 | Final Assembly | Completeness verification + first Claude Code prompt template |
| **11** | **Multi-Agent Support** | **Universal spec + adapters for Cursor, Copilot, Windsurf, Aider, OpenHands, Continue** |

---

## Agents Included

### Standard (every project)
`changelog` · `frontend` · `retro` · `docs` · `security` · `api`

### Web App Agents
`web-performance` · `web-seo` · `web-accessibility`

### Mobile App Agents
`mobile-ux` · `mobile-performance` · `mobile-platform`

### Project-Specific
Define your own: `payment` · `ml` · `search` · `content` · `ussd` · and more

---

## Multi-Agent Support (Phase 11)

Project Genesis isn't locked to Claude Code. **Phase 11** adds a `core/UNIVERSAL_SPEC.md` — a single agent-agnostic source of truth — and adapter files that translate it into each AI coding agent's native format.

| Agent | Config format | Strengths |
|-------|--------------|-----------|
| **Claude Code** | `.claude/` + `CLAUDE.md` | Deep context, plugins, subagents, MCP integration |
| **Cursor** | `.cursor/rules/*.mdc` | In-editor scoped rules, Composer for multi-file |
| **GitHub Copilot / Codex** | `.github/copilot-instructions.md` | IDE integration, Codex cloud agent tasks |
| **Windsurf** | `.windsurf/rules/*.md` | Large context window, Flows automation |
| **Aider** | `.aider.conf.yml` + `CONVENTIONS.md` | Terminal-native, git-native, precise diffs |
| **OpenHands** | `.openhands/microagents/*.md` | Fully autonomous, full environment, end-to-end tasks |
| **Continue** | `.continue/config.json` + `rules/` | Open-source, model-agnostic, local model support |

Each adapter lives in `adapters/[agent]/ADAPTER.md` and includes setup instructions, generated config templates, and agent-specific usage tips. See [`PHASE_11_MULTI_AGENT.md`](./PHASE_11_MULTI_AGENT.md) for the full phase.

---

## MCP Coverage

The framework covers MCPs for every layer of your stack:

- **Core** — `filesystem`, `memory`, `sequential-thinking`, `github`, `fetch`
- **Database** — `postgres`, `mysql`, `mongodb`, `sqlite`, `redis`
- **Web** — `playwright`, `puppeteer`, `browsertools`, `exa`, `firecrawl`, `vercel`, `cloudflare`
- **Mobile** — `expo`, `firebase`, `xcode`, `android-studio`, `appstore-connect`, `sentry`
- **Cloud** — `aws-cli`, `gcp`, `azure`
- **Domain** — `stripe`, `slack`, `linear`, `notion`, `sendgrid`, `twilio`, `openai`, `anthropic`

Each entry includes: what it does, when to use it, and example config snippets.

---

## Examples

See the [`examples/`](./examples/) directory for completed Genesis setups:

| Example | Stack | Description |
|---------|-------|-------------|
| [`saas-web-app`](./examples/saas-web-app/) | Next.js + Postgres + Vercel | B2B SaaS with subscription billing |
| [`mobile-consumer-app`](./examples/mobile-consumer-app/) | React Native + Firebase | Consumer iOS/Android app with offline support |

*More examples welcome — see [Contributing](#contributing).*

---

## Contributing

This framework improves with every project it's used on. Contributions are very welcome.

### Ways to Contribute

- 🐛 **Report issues** — Something unclear, missing, or wrong? [Open an issue](../../issues/new?template=bug_report.md)
- 💡 **Suggest improvements** — Better questions, new phases, improved templates? [Start a discussion](../../discussions)
- 🔌 **Add an MCP** — Know a useful MCP that's missing from Phase 9? Submit a PR
- 🤖 **Add an agent** — Built a useful agent for a specific domain? Share it
- 📖 **Add an example** — Completed a Genesis setup? Add it to `examples/`
- 📝 **Fix docs** — Typos, broken links, unclear explanations

### Contribution Guidelines

1. **Fork** the repo and create a branch: `feature/your-improvement`
2. **Follow** the existing format and tone (checklist-based, framework-consistent)
3. **Test** your additions — if it's a new agent or MCP, use it on a real project first
4. **Submit** a PR with a clear description of what you're adding and why

### What Makes a Good Contribution

**For new MCPs (Phase 9):**
- What the MCP does (one sentence)
- Which platform/stack it's best for
- When to use it (the specific trigger scenario)
- A working example config snippet

**For new agents (Phase 8):**
- Focused scope (one domain, not a catch-all)
- 4–8 concrete checklist items with specific outputs
- Clear "when to invoke" condition

**For new examples:**
- Real project type (anonymised is fine)
- Complete `mcp-config.json` for that stack
- Key decisions and why they were made

---

## Versioning

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | 2026-03 | Added web & mobile agents, expanded MCP catalogue with annotated examples |
| 1.0 | 2026-03 | Initial release — based on FraudShield project setup |

---

## License

MIT — use freely, improve openly.

---

*If this framework saved you setup time, consider starring ⭐ the repo so others can find it.*
