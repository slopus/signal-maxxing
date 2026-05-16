# Ralph Loop - Agent Loop / Software Factory Pattern

Source: `bra1nDump/skillbench` (local: `/Users/kirilldubovitskiy/projects/skillbench`)

## Overview

The **Ralph Loop** is a multi-stage agentic orchestration pattern implementing a research-to-publication pipeline. It's a specialized software factory pattern adapted for evidence-driven content generation.

## Core Files

| File | Role |
|------|------|
| `scripts/ralph.mjs` | Main orchestrator (~800 lines). Spawns Claude subprocess agents, manages parallel execution, auto-retry, phase transitions, QA gates |
| `agents/discover.md` | Agent prompt: find new contenders and signals |
| `agents/deep-dive.md` | Agent prompt: collect multi-source evidence with hard quality gates |
| `agents/rank.md` | Agent prompt: produce editorial rankings backed by evidence |
| `src/lib/catalog.ts` | TypeScript skill registry updated by agents, served to Next.js for SSG |

## How It Works

### Phase 1: Parallel Research

Three Claude agents run sequentially per category (categories parallelizable):

1. **Discover** - Searches web, HN Algolia, Twitter/X, Reddit, GitHub, registries. Outputs "NEW CONTENDER ALERT" flags and meta-shifting signals.

2. **Deep-Dive** - Consumes discover findings, builds evidence-backed understanding. Collects quantitative traction (stars, downloads, engagement), official artifacts, public comparisons, usage evidence, YouTube videos. Hard gates: 2-3 independent sources, <2 months old, engagement floor met, visual proof of actual use.

3. **Rank** - Consumes discover + deep-dive, produces editorial ranking per category. Weights evidence quality, workflow fit, recency, credibility.

Each run stores findings in `agent-runs/{timestamp}_run_{category}/` as structured markdown.

### Phase 2: Sequential Finalization

1. **Catalog Update** - Claude reads rank findings, updates `catalog.ts` with evidence arrays, rankings, verdicts, new skill entries
2. **Screenshot Collection** - Parallel search for product-in-use images (min 800x400px, quality 8-10)
3. **Metrics Collection** - Non-AI collection of GitHub stars, npm/PyPI downloads, HN mentions (7-day window)
4. **QA Loop** - Build + link check with auto-fix retry (max 5 attempts)

### Orchestration Features

- `--parallel N` runs N categories' research stages concurrently
- `--loop --interval 30` repeats every 30 minutes indefinitely
- Subprocess isolation: each agent runs as `claude -p` with allowlisted tools (Bash, Read, Write, Edit, Glob, Grep, WebSearch, WebFetch, Agent)
- Signal quality enforcement: deep-dive discards claims failing the bar; every shipped signal requires multi-source verification
- Graceful degradation: Twitter/X and Reddit tools optional
- Human steering via `human-steering-history.md`
