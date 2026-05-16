# Agent Scaling: Actual Data (2026)

> This replaces earlier lazy claims. There IS real data.

---

## The Single Most Important Finding

**The harness matters more than the model.**

- GPT-5.5 in Codex harness: 61.5% functionality. Same model in Cursor harness: **87.2%**. A 25.7-point swing from orchestration alone.
- LangChain rebuilt their harness: rank 30+ -> rank 5 on Terminal-Bench (52.8% -> 66.5%)
- Factory.ai's Droid harness with Opus: 58.8% vs Claude Code's 43.2% with same model

**This is the whole game.** The orchestration layer is the dominant variable, not raw model compute.

---

## Scaling Laws That Exist

### METR Task-Length Scaling Law
- Agent task-length capability **doubles every ~7 months** (176-225 day CI)
- SWE-bench specifically: **doubling under 3 months**
- Claude 3.7 Sonnet (Feb 2025): 50% success at ~60 min tasks
- Claude Opus 4.6: 719 min
- Claude Mythos Preview: 1,044 min
- Near 100% on <4 min tasks; <10% on >4 hour tasks
- Trend robust: even 10x measurement errors only shift predictions ~2 years

### Inference-Time Scaling (OpenHands)
- Claude 3.7 Sonnet, multiple attempts + critic model:
- 1 attempt: 60.6% -> 5 attempts: **66.4%** on SWE-Bench Verified
- **Log-linear improvement with diminishing returns**
- +6 points for 5x the compute

### Recursive Tournament Voting (Apr 2026)
- Claude 4.5 Opus: 70.9% -> **77.6%** on SWE-Bench Verified
- Terminal-Bench: 46.9% -> **59.1%**
- Key insight: scaling long-horizon agents is a representation/selection problem, not just "generate more samples"

### Google/MIT Multi-Agent Scaling (Dec 2025)
- 180 agent configurations, 3 LLM families, 5 architectures, 4 benchmarks
- Multi-agent: **+80.9% on parallelizable tasks, -39-70% on sequential tasks**
- Capability-saturation threshold at ~45% single-agent baseline
- Above that: adding agents yields diminishing or negative returns
- Independent agents amplify errors **17.2x**; centralized coordination: **4.4x**
- Multi-agent burns **~15x the tokens** of single-agent

---

## Production Numbers at Scale

### Anthropic Internal (132 engineers, 500K sessions analyzed)
- **67% increase in merged PRs per engineer per day**
- Human turns per transcript: 6.2 -> 4.1 (33% decrease)
- Max consecutive tool calls: 9.8 -> 21.2 (doubled)
- Success rate on hardest tasks **doubled** Aug-Dec 2025
- Human interventions per session: 5.4 -> 3.3
- 99.9th percentile turn duration: <25 min -> >45 min (agents running longer)
- 59% of work uses Claude (up from 28% YoY)
- Can "fully delegate" only 0-20% of tasks
- 27% of AI-assisted work = tasks that wouldn't have been done otherwise

### Cursor "Self-Driving Codebases" (Feb 2026)
- **~1,000 commits per hour** across 10M tool calls over one week
- "Thousands of agents" simultaneously, peak "several hundred" concurrent
- Single large Linux VM, no human intervention after init
- Built a functional web browser as test project
- "Vast majority of commits almost entirely runnable without human intervention"

### Cursor GPU Kernel Optimization (Apr 2026)
- 235 kernel problems, 27 NVIDIA Blackwell 200 GPUs, 3-week autonomous run
- **63% success rate** (149/235 beat PyTorch baselines)
- **38% geomean speedup** over PyTorch
- 19% of problems achieved >2x improvements
- Reached **86% of human-optimized cuBLAS** on matmul

### Devin / Cognition
- **67% PR merge rate** (up from 34% prior year)
- Hundreds of thousands of merged PRs across thousands of companies
- 14x less time for Java migrations
- 20x faster on security vulns (1.5 min vs 30 min manual)
- Test coverage: 50-60% -> 80-90%
- RV Tech: **52 tickets triaged in <15 minutes** (1 agent per ticket)
- Automotive OEM: **2,000-4,000 engineering hours/month reclaimed** (~$1.7M-$3.5M annual savings)

### DORA 2025
- Individual: +21% tasks completed, +98% more PRs merged
- Epics completed per dev: +66.2%
- But 30% of developers report low trust in AI code
- **35-40% productivity gain on greenfield, 10% or less on complex legacy**

---

## Autonomous / Proactive Agents: What Happened

### SICA (Self-Improving Coding Agent)
- Edited its own source code in a loop
- Climbed **17% -> 53% on SWE-Bench Verified** (3x improvement)
- At iteration 9, autonomously built an AST-based symbol locator -> large accuracy jump
- Only works where outcomes are verifiable (tests, execution)

### Meta REA (Ranking Engineer Agent)
- Runs autonomously for days-to-weeks (hibernate-and-wake)
- Generates hypotheses, launches ML training jobs, debugs failures, iterates
- **2x model accuracy** across six production ads ranking models
- 3 engineers doing what historically required 12

### NeuBird Falcon (SRE)
- **230,000 alerts resolved autonomously**
- Saved 12,000 engineer hours, $1.8M
- Alert noise reduced 78%, RCA accuracy 94%
- Detects degradation 30-60 min before failure

### Tusk AI (Bug Hunting)
- Ran Claude Code 24/7 across 12 libraries
- 100+ hypotheses tested, **23 real bugs found in 2 weeks**
- Most outputs were false positives -- deterministic verification (e2e tests in 3 modes) was essential

### The Inception Loop (Tars)
- Level 3 autonomous agent, **30 days straight**
- Managed its own CI/CD, identified feature gaps, created branches, ran tests, submitted PRs
- Self-restarted via PM2, 12-hour maintenance cycles, 2AM cloud backups
- Ran without human intervention for a month

### Other Production Systems
- **GitHub Copilot Autofix**: 8M pull requests auto-reviewed, 2/3 of vulns fixed with little/no editing
- **Snyk Agent Fix**: 80% accuracy on vuln remediation, ~7 hours manual work -> seconds, MTTR -84%
- **CodeRabbit**: 300K PRs, 49.2% precision, 53.5% recall
- **Datadog Bits AI Dev**: watches prod 24/7, triages errors, generates fixes, opens PRs, iterates on CI failures

---

## The Shape of the Curve

```
Quality
  ^
  |        _______________
  |       /
  |      /
  |     /    <- log-linear, diminishing returns
  |    /
  |   /
  |  /
  | /
  |/
  +-------------------------> Compute / Agents
  1    3    5    8    15   100+
```

- **1 -> 5 agents**: meaningful gains (+6-12 points on benchmarks)
- **5 -> 15 agents**: gains only on embarrassingly parallel tasks
- **15+ agents**: negative returns on sequential work, 17x error amplification
- **The harness is worth more than 5x model scaling**
- **Self-improving loops (SICA, Hermes) break the curve** -- each iteration makes the next one better

---

## Sources

- [METR Task-Length Scaling](https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/)
- [OpenHands Inference-Time Scaling](https://openhands.dev/blog/sota-on-swe-bench-verified-with-inference-time-scaling-and-critic-model)
- [Scaling Test-Time Compute for Agentic Coding](https://arxiv.org/abs/2604.16529)
- [Google/MIT Scaling Agent Systems](https://arxiv.org/abs/2512.08296)
- [Anthropic: How AI is Transforming Work at Anthropic](https://www.anthropic.com/research/how-ai-is-transforming-work)
- [Anthropic: Measuring Agent Autonomy](https://www.anthropic.com/research/measuring-agent-autonomy)
- [Cursor Self-Driving Codebases](https://www.cursor.com/blog/self-driving-codebases)
- [Cursor GPU Kernel Optimization](https://www.cursor.com/blog/gpu-kernels)
- [Devin 2025 Performance Review](https://cognition.ai/blog/devin-annual-performance-review-2025)
- [DORA 2025 Report](https://dora.dev/dora-report-2025/)
- [Agent Harnesses Beat Model Upgrades](https://www.mindstudio.ai/blog/agent-harnesses-beat-model-upgrades-5-benchmarks)
- [Endor Labs Harness Comparison](https://www.endorlabs.com/learn/gpt-5-5-sets-a-new-code-security-record-with-cursor-not-codex-in-agent-security-league)
- [SICA Paper](https://arxiv.org/html/2504.15228v2)
- [Meta REA](https://engineering.fb.com/2026/03/17/developer-tools/ranking-engineer-agent-rea-autonomous-ai-system-accelerating-meta-ads-ranking-innovation/)
- [NeuBird Falcon](https://venturebeat.com/security/ai-agents-that-automatically-prevent-detect-and-fix-software-issues-are-here)
- [Tusk AI 23 Bugs](https://www.usetusk.ai/resources/we-fixed-23-bugs-in-2-weeks-by-getting-ai-to-break-things-while-we-slept)
- [The Inception Loop](https://earezki.com/ai-news/2026-03-19-the-inception-loop-a-month-in-the-life-of-a-self-improving-ai-sidekick/)
- [AI21: 200,000 SWE-bench Runs](https://www.ai21.com/blog/scaling-agentic-evaluation-swe-bench/)
