# slopus/scale - Portable AI Compute Cluster

**Repo:** https://github.com/slopus/scale
**Stack:** TypeScript, Node.js, pnpm monorepo, zero runtime deps
**Mental model:** k3s for agents -- a self-hosted portable cluster, not a pipeline

## Core Idea

Give it a goal (or just raw data with "make me happy"), and it maintains a durable cluster of agents, skills, tools, and sandboxes that keep working toward outcomes. No DAGs, no chains -- a living cluster that adapts to new inputs over time.

## Architecture

Two packages:

### `scale-server` -- vanilla `node:http` server, 3 endpoints
- `GET /health` -- liveness + summary counts
- `GET /state` -- full system snapshot
- `POST /configuration` -- applies a declarative config diff to the running cluster

### `scale-cli` -- zero-dependency CLI (`node:util.parseArgs`)
- `status` and `configure` subcommands
- Flags: `--agent`, `--skill`, `--sandbox`, `--tool`, `--grant`, `--input`

## Key Abstractions (`system.ts`)

| Abstraction | Description |
|-------------|-------------|
| **ScaleSystemSnapshot** | Entire cluster state: goals, inputs, agents, skills, sandboxes, tools, grants, timestamps. Single source of truth |
| **Agents** | Named entities with status (`idle`/`working`), explicit lists of skills, sandboxes, and tools they can access |
| **Skills** | LLM instructions or deterministic procedures (id + description) |
| **Sandboxes** | Isolated workspaces (id, description, path) |
| **Tools** | External capabilities (git, gmail, etc.) registered by id |
| **AccessGrants** | Fine-grained RBAC: `agent -> resource (skill|sandbox|tool) -> modes (read|write|execute|admin)` |
| **Goals/Inputs** | Append-only lists. Goals drive agent work; inputs are raw data without a specific objective |

## Configuration Model

**Declarative, additive.** `applySystemConfiguration()` takes current snapshot + delta:

1. Appends goals and inputs (deduplicated, never removed)
2. Upserts skills, sandboxes, tools, agents by id
3. When an agent declares skills/sandboxes/tools, those resources are **auto-created if missing**, and access grants are **auto-generated** (skills get `execute`, sandboxes get `read+write+execute`, tools get `execute`)
4. Explicit grants can override or extend permissions
5. System boots with a built-in "director" agent with a "task-solving" skill

## What Makes It Different

- **Cluster, not pipeline.** No DAGs, no chains. Steer a living cluster that adapts to new inputs
- **Goal-optional.** Feed raw data, let agents decide what is useful
- **RBAC first-class.** Per-agent access boundaries on skills, tools, sandboxes with 4 permission modes
- **Portability.** Laptop to VM, state in PostgreSQL, modeled after k3s
- **Zero deps.** Pure Node.js stdlib for server and CLI

## Current State

Early-stage scaffolding. What exists: HTTP state server, CLI client, file-backed persistence (`.happy-scale/state.json`), type system for resource model. What does NOT exist yet: actual LLM agent execution loops, sandbox provisioning, skill loading/composition, PostgreSQL migrations, continuous input feeds, vendor routing.

## Relevance to signal-maxxing

This is the same design space -- agent orchestration for loose, high-effort digital work. Key design decisions to compare:
- Cluster metaphor vs pipeline/loop
- Declarative config vs imperative orchestration (Ralph loop)
- RBAC/grants model vs tool allowlists
- Goal-optional input model vs structured research phases
