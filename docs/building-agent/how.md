# How to build

[coding agent for this system] - this is tricky. If you are reading this file you are likely the one coding up this system. You are an agent. But the system is also agentic. You will be simpler - you will have this specificiation. And keep running in a loop until you reach this specificaiton. Honestly you should NEVER stop building. Similarly to the reserach cluster we are building - you must keep refining at infinitum. Yet without bloat.

You have core prompts that drive you:
- what.md - what we are building
- how.md - basically your bootrstrap core prompt
- core-learnings.md - you will read this in full. DO NOT polute this with trash. It must be bounded to avoid context rot for next generations
- log.md - log of key learnings, reflections
- Use SMALL agents.md & claude. to capture core specification / decisions local to a module of code
- I fucking hate claude.md - only create it to reference agents.md in folders like i do already
- Do reflect on stuff that you learned, not only in the core learnings / log - but also modify the prompts / add scripts if needed NO BLOAT THO. To counter issues. Use best practices when promting, see how other similar systems are doign it
- PRETTY MUCH ANYTHING older than 2026 - consider it much less when it comes to prompting techniques / learnings about state of AI - space is moving fast.

Commit when you see fit
And your key input is of course code + the at some point running orchestration system :D

You commit to local main branch only

# How to verify

This system is super stupid to try testing with unit tests. You may write throw away scripts to assert some behavior you need to validate - for example 'how to run acpx?' 'what agent harnesses do i have access to / am logged in on this machine?' etc, but throw them away after you are done

Do not do a single unit test. The only useful test for this system is to actually run the full cluster on a toy problem first and inspect its state. And next run on more real use cases provided in the what.md. Thats THE ONLY FUCKING WAY.

Since these systems take a long time to produce output, and if simpler tests / shorter runnings stuff issues are resolved - we can let the cluster run with a question, and keep iterating while we wait for results. Just be careful to not swap the runtime from underneath it once its already running ;D

Also use portless by vercel to allow running multiple clusters simultaneously easily. Ideally someting

agent-browser cli for veryfing dashboard.

# When you should stop?

GOTCHAAA - FUCKING NEVER STOP there is ALWAYS MORE WORK

