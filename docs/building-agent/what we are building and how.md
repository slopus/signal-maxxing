# Signal Maxxing — Specification

What we are building?
A swarm of agents to do extreamly costly deep research - it needs to go deeper, do more cross verifications, run experiments (if its code related for example).

Example hard questions this system will need to be able to answer:
- What were the core changes to the prompt in claude code 

## Human input

- **Goal**: the only required user input is the **raw** goal — kept open-ended, not
  forced into a schema.
- **Questions**: agents may ask the human clarifying questions **asynchronously** (the
  human is not blocking the loop).
- **Answers**: human answers feed back into context.



Inspired by - slopus/scale, paperclip, gstack, compound engineering and so on

## What we know (operating assumptions)

- Different agents / models produce different results — fan-out (`N × agents`) is genuinely useful,
  not just redundant.
- Agents don't "learn" between runs. The only durable memory is **context**: they need
  the relevant stuff put in front of them every time.
- Models lose IQ points the more context they have. Even tho current models take 1M input, the output degrades drastically as you jam more stuff into the context.
- Implication: the harness's job is context construction and orchestration, not training.

## Principles

- **Minutes, not weeks, to first results.** A goal should bootstrap an agent and produce
  something inspectable fast. Optimize time-to-first-artifact.
- **AI has little idea what is "good."** Models are weak self-judges
- **Verification is key.** All verification needs to roll up to verificaiton provided by the user. If the user didn't provide a method - you need to brainstorm your own verification method.
- **Human attention has a budget.** Refine a fixed size top level report they can look at.
- **Human will provide input asynchronously.** You should consult the human on key decision points, or to better gather requirements, get unblocked. Yet you will not stop 
- **Recommend, don't decide.** Surface options against human preference, output size, and
  desired output form; let the human pick. Your core job is to collect facts, 
- **KISS - keep it simple stupid.** We should start with the dumbest approach first. Being fancy / fast change of the harness is a dangerous game. The hotter the path, the more stable it should be.

# Runtime / Primitives

## Core loop that works per agent roughly

[coding agent for this system] - this is tricky. If you are reading this file you are likely the one coding up this system. You are an agent. But the system is also agentic. You will be simpler - you will have this specificiation. And keep running in a loop until you reach this specificaiton. Honestly you should never stop yourself. You will be killed every hour and restarted in case you did someting really stupid and got trully stuck. 

You have core prompts that drive you:
- specification.md - this file
- core-learnings.md - you will read this in full. DO NOT polute this with trash. It must be bounded to avoid context rot for next generations
- log.md - log of key learnings, reflections
- Use SMALL AGENTS.md & CLAUDE. to capture core specification / decisions local to a module of code

Commit when you see fit
And your key input is of course code + the at some point running orchestration system :D


## The end system agent topology roughly

All agents are 'forked'. Thats how the root orchestrator agent array is spawned.
The way the agent is started is through acpx, it also uses it to start its child agents.
Parent agent is responsible for forking the child.
We will give some 'template' agent folders, but the agent can and should self modify depending on its task.


All agents will use acpx to actually do work.
Each agent will gets its own working directory - provisioned and sandboxed by default to limit blast radius. The agent may reqeust more permissions from parent by sending a message to a parent.

All code we write should be in typescript, pnpm, monorepo style, linter for ocnsistency and generally good practices.

orchestrator - which is always on, monitors task, and is started in a loop (just like you!). Just in case it dies.


TBD: we should take the basic plan, execute, validate, reflect, refine from GStack, Hermes, Compound Engineering.



## Tools

- **Research** — web, plus social signal sources (Twitter/X, Reddit). See my local project ~/projects/bra1nudmp/skills, just copy them over into your harness.
- **AI** — model calls as a first-class tool.

## Permissions

- All work should be run in

## Agent communication

- Agents coordinate using an inbox system. That is managed by the orchestrator mostly. 
