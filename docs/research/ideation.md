# Signal Maxxing - Ideation

## Observation

Text files are now largely for agents (plans, skills, rules, code). Humans steer via chat/voice/feedback on planning/verification. Optimizing for human reading speed may be a dead end -- just ask the agent.

---

## Option 1: Alternative take on Scale -- burn more compute, get better results

Similar idea space to: slopus/scale, OpenClaw compound, Paperclip

Core question: **What's the law? How much better can you get by throwing more money at agents?**

Approach:
- Take acpx as the agent harness, more relaxed state management
- Spawn new containers, give permissions at the container level not agent level
- Agent can do whatever within its sandbox permissions

**The law is partially known (see docs/agent-scaling-data.md):**
- Log-linear with diminishing returns: 1->5 attempts = +6-12 points on benchmarks
- Multi-agent: +80.9% on parallelizable tasks, **-39-70% on sequential tasks** (Google/MIT)
- Saturation threshold at ~45% single-agent baseline
- **The harness is worth more than 5x model scaling** (25.7-point swing from harness alone)
- Self-improving loops (SICA: 17%->53%) break the curve by compounding

Real production numbers exist:
- Cursor: 1,000 commits/hour, thousands of concurrent agents, one week autonomous
- Cursor GPU: 63% success rate, 38% speedup over PyTorch, 3 weeks autonomous
- Meta REA: days-to-weeks autonomous, 2x accuracy, 3 engineers = 12
- Devin: 67% merge rate, hundreds of thousands of PRs
- Tars Inception Loop: 30 days straight autonomous

**What's actually missing:** nobody has published the cost-per-quality curve as a function of harness design. Everyone measured model scaling or parallelism, but **the harness variable dominates and nobody is systematically optimizing it.**

This is the real opportunity: build the harness that optimizes the harness.

---

## Option 2: Better visualizations for planning & validation

80% of time is spent on Plan+Assess (compound engineering confirms this). So better tooling there = highest leverage.

Current state of the art is literally "ask the agent." But with expensive async workflows (gstack, compound), you're already waiting. So visualization of agent state/progress/decisions could be valuable.

Ideas:
- Live dashboard of agent cluster state (what's working on what, what's blocked)
- Decision tree visualization for agent planning
- Diff preview / impact analysis before agents commit
- Cost tracking with projections

Counter-argument: if agents keep getting better, the planning phase shrinks. Might be building for a world that's about to disappear.

---

## Option 3: Design/UX validation pipeline

Direction Claude Design is taking seems promising. But needs:
- Performance tracking for React components
- Ability to bench against good reference style
- Storyboard-like per-component validation
- Auto-setup of good dev environments per project

Setting up a good environment differs project to project -- but an agent-meta system could help discover and apply best practices from other projects.

---

## Option 4: Agent-meta -- the comparison/discovery layer

Not skillpack (buyer's guide), but a live system that:
- Discovers and tracks agent frameworks, tools, methodologies
- Compares them side-by-side with evidence
- Feeds findings into your own agent workflows
- Self-updating via agent research loops (like Ralph Loop already does for skillbench)

This is meta: use agents to research agents to improve your agents.

---

## Option 5: Run 5 MAX subscriptions borderline autonomously

Who's tried this:
- **Garry Tan** -- 10-15 parallel Claude Code sprints via Conductor app + gstack. Human orchestrated, not truly autonomous. $200/mo.
- **Codex /goal** -- closest to "set and forget." 14-25 hour runs. Works for well-scoped goals.
- **Cursor background agents** -- 8 parallel, 70-80% completion rate, cloud VMs.
- **Compound Engineering** -- 50+ sub-agents during planning phase.

Nobody has published results from truly proactive/autonomous discovery -- agents finding problems you didn't ask about. Emerging but limited to structured scenarios (bug tracking integration).

**The bottleneck is review, not compute.** Teams running 4-8 worktrees report being "bottlenecked on review, not on Claude."

---

## Cross-cutting insights

1. **The cluster metaphor is right, but the coordination model matters more than the scale.** More agents != better. Structured isolation + typed contracts > swarm intelligence.

2. **Self-improvement is the moat.** Hermes (auto-generating skill docs) and Compound Engineering ("compound" step) both converge on: make each run improve the next run.

3. **Container-level permissions > agent-level permissions.** Scale's RBAC is interesting but may be over-engineered. Just give each agent a sandbox and let it rip.

4. **Human time allocation: 80% steering, 20% reviewing output.** Any tooling that reduces steering overhead (better planning, better context) has outsized impact.

5. **Cost is surprisingly low.** $200/mo covers serious usage. The constraint is human attention, not dollars.
