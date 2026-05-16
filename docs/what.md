# Signal Maxxing — Specification

What we are building?
A swarm of agents to do extreamly costly deep research - it needs to go deeper, do more cross verifications, run experiments (if its code related for example).

"Magic is when you did an unreasonable ammount of work behind the scenes"

Example hard questions this system will need to be able to answer (AND what you should be testing on)
- "What were the core changes to the prompt in claude code in its history?"
- "Create an architecture diagram of top agentic harnesses, use vision models - agent-browser to verify MANY of times that the shit looks good and is not sloppy. KISS, honestly maybe use local tldraw / excalidraw or something so it looks good out of the box?"
- "What are the most likely to succeed founders from the previous 2 batches of YC?"

Inspired by - slopus/scale, paperclip, gstack, compound engineering, llm-wiki

## Human input

- **Goal**: the only required user input is the **raw** goal — kept open-ended, not
  forced into a schema.
- **Questions**: agents may ask the human clarifying questions **asynchronously** (the
  human is not blocking the loop). Even without steering the system is able to make progress while accumuating again - make sure to not over-bloat the questions!


## What we know (operating assumptions)

- Different agents / models produce different results — fan-out (`N × agents x model type`) is genuinely useful, not just redundant. Use the most expensive model by default for everything - opus 4.7, gpt 5.5, gemini 3 pro
- Agents don't "learn" between runs. The only durable memory is **context**: they need
  the relevant stuff put in front of them every time.
- Models lose IQ points the more context they have. Even tho current models take 1M input, the output degrades drastically as you jam more stuff into the context. Always seek to keep code and prompts short and simple.
- Implication: the harness's job is context construction and orchestration, not training.

## Principles

- **Minutes, not weeks, to first results.** A goal should bootstrap an agent and produce
  something inspectable fast. Optimize time-to-first-artifact.
- **AI has little idea what is "good."** Models are weak self-judges
- **Verification is key.** All verification needs to roll up to verificaiton provided by the user. If the user didn't provide a method - you need to brainstorm your own verification method.
- **Human attention has a budget.** Refine a fixed size top level report they can look at.
- **Human will provide input asynchronously.** You should consult the human on key decision points, or to better gather requirements, get unblocked. Yet you will not stop 
- **Recommend, don't decide.** Surface options against human preference, output size, and
  desired output form; let the human pick. Your core job is to collect facts, think creatively, with more information make simple decisions like 
- **KISS - keep it simple stupid.** We should start with the dumbest approach first. Being fancy / fast change of the harness is a dangerous game. The hotter the path, the more stable it should be.

## Prompting 


TBD: we should see  the basic plan, execute, validate, reflect, refine from GStack, Hermes, Compound Engineering. for inspiration. DO NOT complicate out system too much. ONLY whats needed. JUST as refernece.

# Runtime / Primitives

- Use the native acpx basic controls to allow the agent to read / write from its folder only
- Allow web search / twitter / reddit - those are core research tools.
- Do allow agents to clone code within their dirs - they may need it for inspection

## The end system agent topology roughly

All agents are 'forked'. Thats how the root orchestrator agent array is spawned.
The way the agent is started is through acpx, it also uses it to start its child agents.
Parent agent is responsible for forking the child.
We will give some 'template' agent folders, but the agent can and should self modify depending on its task.

the template lives in this repository, and is copied into the top level orchestrator run


All agents will use acpx to actually do work.
Each agent will gets its own working directory - provisioned and sandboxed by default to limit blast radius. The agent may reqeust more permissions from parent by sending a message to a parent.

All code we write should be in typescript, pnpm, monorepo style, linter for ocnsistency and generally good practices.

cli to start the cluster.
dashboard web app.

orchestrator - which is always on (it should be run in a loop, and be killed and resumed from same session every hour just in case it gets stuck. if it exists earlier - just restart it with a 1 second delay), monitors task, manages to topology and the inboxes of all agents. It injects a way to read current topology using an mcp server it manages.
orchestrator is a combination of code that starts the mcp server for message bus management
ACTUALLY the bus is just going to be a tree of files

I think we can use some file locking mechanism to avoid race conditions. Literally agents can create a <filename>.lock - and write time, who locked it before updating. Probably can have it be part of the cli or write a separate helper cli to atomically swap a file :D yea code is better for this.

researcher template
will have the research skills from bra1ndump repo

reflector template
see how hermes, and others did the 'dream' reflect adjustement loop. Do a similar thing. The orchestrator will start these.

## Inspectability

The orchestrator will probably use raw existing agent tools to inspect state of subagents - we know the layout so we can do that well.

Lets also create some tooling that when you start the orchestator it also exposes a SIMPLE graph - no fancy arrows and shit. Lets use this to build the dashboard for observability of the cluster.
https://webtui.ironclad.sh/ 

dashboard is read only. So see the tree of agents + inboxes + link to open agent's folder in local vscode

## Tools

- **Research** — web, plus social signal sources (Twitter/X, Reddit). See my local project ~/projects/bra1nudmp/skills, just copy them over into your harness.
- **AI** — model calls as a first-class tool.

## Permissions

- All work should be run in

## Agent communication

- Agents coordinate using an inbox system. That is managed by the orchestrator mostly. 


