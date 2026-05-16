# V8 for Agents & ACPX

## Agent OS (V8 for Agents)

**Repo:** https://github.com/rivet-dev/agent-os
**Stars:** ~2,900
**Last updated:** 2026-05-14

A portable open-source operating system for agents. ~6ms cold starts, 32x cheaper than sandboxes. Powered by WebAssembly and V8 isolates.

### Why it matters
- Lightweight agent runtime using V8 isolates instead of full containers/sandboxes
- Sub-10ms cold starts make it viable for per-request agent spawning
- Dramatically cheaper than traditional sandbox approaches

### Related projects
- `r33drichards/mcp-js` - MCP server exposing a V8 JS runtime as a tool for AI agents
- `plasmate-labs/plasmate` - "The browser engine for agents" using V8 JS rendering
- `faveos8758/ts-agent-sandbox` - TypeScript sandbox using V8 isolates for agent code

---

## ACPX

**Repo:** https://github.com/openclaw/acpx
**Stars:** ~2,600
**Last updated:** 2026-05-15
**Runtime:** Node.js / TypeScript (`npm i -g acpx`)

Headless CLI client for stateful Agent Client Protocol (ACP) sessions.

### Architecture: Sync CLI with queue-owner lingering

**Not a daemon.** Each `acpx` invocation is a foreground CLI process. But it has a "queue owner" model:
- The first `acpx` process for a session becomes the **queue owner** and stays alive (configurable TTL, default 300s) to drain queued prompts
- Subsequent `acpx` calls for the same session submit prompts via **IPC** to the existing queue owner instead of spawning a new agent
- So it's a short-lived CLI that can linger as a queue owner, not a persistent daemon

### What "headless" means

Traditional agent CLIs (Claude Code, Codex) use interactive TUIs with ANSI formatting. acpx strips that away: it communicates with agents over **structured JSON-RPC messages** (the ACP protocol) on stdin/stdout pipes, not by scraping a pseudo-terminal. This makes it suitable for **machine-to-machine use** -- one AI agent orchestrating another.

### Stateful sessions via file persistence

- Session metadata stored under `~/.acpx/sessions/` as JSON files
- Sessions scoped to `(agent, git-root directory, optional name)`
- When you run a prompt, acpx walks up from cwd to find the git root and matches the nearest active session
- If the agent process dies before the next prompt, acpx respawns it and attempts ACP `session/load` to restore conversation history

### Protocol: local child processes over JSON-RPC/stdio

- Spawns the agent as a **local child process** with piped stdin/stdout/stderr (`child_process.execFile`)
- Communication uses **JSON-RPC 2.0 over stdio** -- the ACP spec
- **No HTTP or WebSocket** between acpx and the agent
- Each built-in agent name (codex, claude, pi, etc.) maps to either a native ACP command or an npm adapter package (e.g., `codex-acp`, `claude-agent-acp`) that wraps the real CLI in an ACP-compatible stdio interface

### Usage examples

```bash
acpx codex sessions new                    # create a session
acpx codex "fix the flaky test"            # send prompt (auto-routes to session by cwd)
acpx codex -s backend "add pagination"     # named parallel session
acpx codex --no-wait "draft migration"     # fire-and-forget
acpx codex cancel                          # cooperative cancel via ACP
acpx codex status                          # check if agent is running/idle/dead
acpx codex exec "summarize this repo"      # one-shot, no persistent session
acpx claude "refactor auth"                # works with any registered agent
acpx flow run ./workflow.ts                # multi-step orchestrated workflow
```

### Key deps
- `@agentclientprotocol/sdk` (ACP message types)
- Node.js `child_process` for spawning
- File-based session store
- `AcpxRuntime` -> `AcpRuntimeManager` handles session lifecycle, turn execution, cancellation

### Related projects
- `imumesh18/acpx` - Simple Rust library and tooling for building on ACP
- `motosan-dev/acp-cli` - Rust port of ACPX
