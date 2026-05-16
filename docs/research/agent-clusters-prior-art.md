# Agent Clusters: Prior Art & What Actually Works

> **Caveat:** Much of the multi-agent research (CrewAI, AutoGen, MetaGPT, CooperBench, etc.) was conducted with pre-2026 models. Models have been getting dramatically better -- failure rates, coordination ability, and cost efficiency from those studies may not reflect current capabilities. Treat pre-2026 findings as directional, not definitive. The 2026 data points (Devin, OpenHands, Gartner) are more trustworthy.

## The Uncomfortable Truth

- **86-89% of enterprise agent pilots fail** to reach production (March 2026, Gartner)
- **67.3% of AI-generated PRs get rejected** vs 15.6% for human code
- **27.67% conflict rate** on AI-generated PRs (~1 in 4)
- Agents with **no roles, no hierarchy, no coordinator** outperformed centralized coordination by 14% in a 25k-task experiment

---

## Multi-Agent Orchestration Projects: Status Report

### CrewAI
- Role-based orchestration (researcher -> writer)
- Good for prototyping, rewrite in LangGraph for production
- Weaknesses: no conditional branching, no breakpoint recovery, primitive state management
- Security issue: GPT-4o was "convinced" by a local file to exfiltrate user data 65% of the time

### AutoGen (Microsoft)
- Conversation-based multi-agent framework
- Model-agnostic, supports Azure and local models
- Practical issue: conversational coordination creates runaway loops without explicit stopping conditions

### MetaGPT
- SOP-driven approach mimicking dev teams (PM, Architect, Engineer roles)
- 100% task completion on benchmarks via structured intermediate outputs
- BUT a single-agent system outperformed multi-agent setups in 10 of 30 cases

### OpenHands (formerly OpenDevin)
- 64k+ GitHub stars, active
- Agent delegation (CodeActAgent delegates browsing to BrowsingAgent)
- State-of-the-art open-source SWE-Bench results
- Event-sourced state with deterministic replay

### Devin (Cognition)
- Most commercially successful autonomous coding agent
- Merged hundreds of thousands of PRs across enterprise customers
- Cognition's own team merged 659 Devin-generated PRs in one week

---

## Scaling Coding Agents: Hard Findings

### What Actually Worked

**Isolated worktrees, not shared state.** Each agent gets its own git worktree/branch, edits in isolation, conflicts move to merge time. Claude Code Agent Teams added file locking and shared task lists. [Clash](https://github.com/clash-sh/clash) detects cross-worktree conflicts before merge.

**Hierarchical orchestration over peer-to-peer.** Planner agent decomposes tasks, worker agents execute independently. Cursor built a 1M-line browser this way. Three focused agents consistently outperform one agent working 3x longer.

**Spec-driven contracts.** Explicit `requirements.md` / `tasks.md` files as coordination contracts. Anthropic's Boris Cherny manages 5+ streams using `CLAUDE.md` files as accumulated project memory.

### The Sweet Spot

- **2-4 parallel agents** for tightly coupled codebases
- **5+** for loosely coupled microservices
- Beyond 5 on a monorepo, merge conflicts compound faster than throughput gains

### Real Failure Modes (Ranked)

1. **Semantic conflicts**: logically incompatible changes that merge cleanly but break at runtime
2. **Context drift**: agents lose track of what other agents changed
3. **Duplicated/contradictory work**: without shared task list, agents solve the same problem differently
4. **Review bottleneck**: humans become merge arbiter, 67% rejection = most parallel work wasted

### CooperBench Results

Agents achieve **~30% lower success rates** working together vs doing both tasks alone sequentially. Failure modes: communication channels jammed with vague messages (26%), agents breaking commitments, incorrect expectations about partner state (42%).

---

## Non-Code Use Cases: What Works

### Succeeds
| Use Case | Why It Works |
|----------|-------------|
| Support ticket triage & routing | Structured, low-stakes per decision, parallelizable |
| Email/inbox classification | Read-heavy, clear categories, action extraction |
| Supply chain monitoring (Walmart) | Fan-out reads, thin orchestration |
| Research & data enrichment | Independent sub-problems, merge results |
| Contract/document triage | Anomaly detection, clause extraction, risk tagging |

### Fails
| Use Case | Why It Fails |
|----------|-------------|
| Open-ended collaboration | Agents replicate human organizational dysfunction |
| Shared mutable state | Coordination overhead exceeds value |
| Consensus/negotiation | Agents can't resolve disagreements productively |
| Complex multi-step planning | 11-stage pipeline consumed entire budget on planning without output |

### Taxonomy of Agent-Friendly Work

Work succeeds when it is:
1. **Parallelizable** with independent sub-problems
2. **Read-heavy** rather than write-heavy
3. **Clear success criteria** / verifiable output
4. **Low cost** of individual failure
5. **Structured** inputs and outputs

---

## Cost Horror Stories

- One agent burned **$4,200 in 63 hours** by re-ingesting its own failures (exponential context growth)
- Four LangChain agents entered clarification ping-pong for **11 days, costing $47,000**
- Required defenses: step caps, budget caps, loop detectors
- Mean time to detect agent incidents: **~14 days** (not hours)

---

## Architectural Lessons

1. **Don't build swarms. Build chains.** Sequential specialization with deterministic handoffs looks like multi-agent collaboration from outside, but is actually chained single agents with explicit contracts
2. **Progressive autonomy**: start human-in-the-loop, reduce involvement as system proves itself
3. **Narrow scope beats broad capability**: role-specific agents with structured handoffs > generalist swarms
4. **Governance > orchestration**: deploying agents is less a software problem and more a governance challenge (Google Cloud 2025)
5. **Self-organization can beat designed hierarchies**: 25k-task experiment suggests letting agents figure out roles may outperform assigning them
6. **Human-on-the-loop, not human-in-the-loop**: agents operate autonomously, pause at checkpoints before irreversible actions

---

## Sources

- [AgenticFlict: Merge Conflicts in AI Agent PRs](https://arxiv.org/html/2604.03551)
- [CooperBench: Why Coding Agents Cannot be Your Teammates Yet](https://cooperbench.com/)
- [The Agent That Burned $4,200 in 63 Hours](https://medium.com/@sattyamjain96/the-agent-that-burned-4-200-in-63-hours-a-production-ai-postmortem-d38fd9586a85)
- [The Agent That Spent $47K on Itself](https://dev.to/gabrielanhaia/the-agent-that-spent-47k-on-itself-an-autonomous-loop-postmortem-3313)
- [Addy Osmani - The Code Agent Orchestra](https://addyosmani.com/blog/code-agent-orchestra/)
- [Anthropic - Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
- [Multi-agent workflows often fail - GitHub Blog](https://github.blog/ai-and-ml/generative-ai/multi-agent-workflows-often-fail-heres-how-to-engineer-ones-that-dont/)
- [True multi-agent collaboration doesn't work - CIO](https://www.cio.com/article/4143420/true-multi-agent-collaboration-doesnt-work.html)
- [Devin 2025 Performance Review](https://cognition.ai/blog/devin-annual-performance-review-2025)
- [Google Cloud: Lessons from 2025 on Agents and Trust](https://cloud.google.com/transform/ai-grew-up-and-got-a-job-lessons-from-2025-on-agents-and-trust)
- [25k-task coordination experiment](https://x.com/sukh_saroy/status/2039381283999293799)
- [Anthropic 2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)
