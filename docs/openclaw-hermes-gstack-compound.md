# OpenClaw, Hermes, gstack, Compound Engineering (2026)

These sit at different layers of the same stack:
- **OpenClaw / Hermes** = competing agent runtimes
- **gstack** = role/skill library that plugs into those runtimes
- **Compound Engineering** = methodology/workflow pattern applied on top of any of them

---

## OpenClaw

**Created by:** Peter Steinberger (Nov 2025 as "Clawdbot", renamed Jan 2026)
**Stars:** 347K+ | **Users:** 3.2M active (April 2026)
**Stack:** TypeScript monorepo, local-first

A channel-agnostic personal AI assistant. Gateway control plane connects 20+ messaging channels (WhatsApp, Telegram, Discord, iMessage, Slack, etc.) to LLMs.

### Key ecosystem pieces
| Repo | Purpose |
|------|---------|
| `openclaw/openclaw` | Core: Gateway, multi-agent routing, voice, Canvas (A2UI), sandboxed tools |
| `acpx` | ACP CLI -- typed agent-to-agent IPC, no terminal scraping |
| `lobster` | Workflow shell -- composes skills/tools into pipelines and automations |
| `clawbench` | Full-stack agent benchmark (harness+config+model, not just LLM) |
| `crabbox` | Sandbox runner: warm a box, sync diff, run suite |
| `clownfish` | Maintainer harness for resolving issue clusters at scale |
| `hermit` | Unknown (TypeScript, last pushed May 12) |
| Crawlers | `gitcrawl`, `notcrawl`, `discrawl`, `telecrawl` -- local-first SQLite crawlers |
| Native | `Peekaboo` (macOS screenshots), `AXorcist` (Accessibility), `imsg`, `gogcli`, `wacli` |

### Design philosophy
1. Local-first data ownership
2. Channel-agnostic via unified Gateway
3. ACP as interop standard (agents talk via typed protocols)
4. Composable automation (Lobster pipelines)
5. Plugin extensibility (ClawHub + crabpot testing)

---

## Hermes Agent (Nous Research)

**Stars:** 140K+ (in <3 months)
**Raised:** $65M
**Released:** February 2026

Self-hosted autonomous AI agent with persistent memory. Auto-generates reusable "skill documents" every time it solves a hard problem -- literally improves the longer it runs.

- #1 agent on OpenRouter (224B daily tokens as of May 10, surpassing OpenClaw's 186B)
- 400+ model support (including local via Ollama)
- Runs on Telegram/Discord/Slack/Signal/CLI
- Major releases every 7-10 days

### Key differentiator
Self-improving skill accumulation. Not just chat -- it builds a library of proven approaches from its own experience.

---

## gstack (Garry Tan / YC)

**Stars:** ~89.7K
**Repo:** github.com/garrytan/gstack

Opinionated Claude Code configuration. 23 role-based "skills" (CEO, Designer, Eng Manager, QA, Release Manager, etc.) as SKILL.md files that run as slash commands.

### Philosophy: "Cognitive Gearing"
Force the LLM into distinct specialist roles executing in sprint order:
**Think -> Plan -> Build -> Review -> Test -> Ship -> Reflect**

Claims: 810x his 2013 productivity, 600K lines of production code in 60 days.

Also published **gbrain** (`garrytan/gbrain`) -- persistent memory layer targeting OpenClaw/Hermes agents.

### What it is
Not a framework or protocol. A prompt/skill library riding on top of existing agents.

---

## Compound Engineering (Every, Inc.)

**Plugin:** github.com/EveryInc/compound-engineering-plugin
**Supports:** Claude Code, Codex, Cursor, Pi, Copilot (v3, May 2026)

### Core idea
Each unit of engineering work should make future work *easier*, not harder.

### Four-step loop
1. **Plan** -- agents research codebase + best practices
2. **Work** -- agents write code/tests
3. **Assess** -- linters, tests, parallel sub-agents checking security/perf/complexity
4. **Compound** -- record lessons learned so agents use them next time

Engineers spend **80% on Plan+Assess, 20% on Work+Compound**.

Kevin Rose demoed publicly January 2026. Has spawned derivative projects (e.g., "Praxis").

---

## How They Relate

```
┌─────────────────────────────────────────────┐
│         Compound Engineering (method)        │
│     Plan -> Work -> Assess -> Compound       │
├─────────────────────────────────────────────┤
│          gstack (role/skill library)         │
│  Think -> Plan -> Build -> Review -> Ship    │
├──────────────────────┬──────────────────────┤
│   OpenClaw (runtime) │  Hermes (runtime)    │
│   Gateway + Channels │  Self-improving      │
│   ACP interop        │  Skill accumulation  │
│   Lobster workflows  │  400+ models         │
└──────────────────────┴──────────────────────┘
```
