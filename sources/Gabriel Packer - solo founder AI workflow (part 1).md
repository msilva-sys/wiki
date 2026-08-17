---
type: source
status: active
updated: 2026-08-17
date: 2026-03-26
aliases: [gkpacker part 1, conductor workflow, solo founder workflow]
source: "raw/Clippings/Post by @gkpacker on X.md"
url: "https://x.com/gkpacker/status/2037137933384188037"
tags: [agents, orchestration, worktrees, qa, observability, prior-art]
---

# Gabriel Packer — solo founder AI workflow (part 1)

Published **2026-03-26**, four months before
[[Gabriel Packer - DAG-driven agent orchestration]] (2026-07-23). Same author,
same system, earlier stage. Clipped 2026-08-17.

**The two posts read as before-and-after**, and the difference is the interesting
part: here he **starts each ticket by hand**; part 2 automates exactly that. His
closing line names it as the next step — *"automate the tasks in linear to be
implemented automatically, where I wouldn't need to keep starting each one
manually."*

## The pipeline

1. **Claude Code session** with a **`product-manager`** and a
   **`design-specialist`** subagent — discuss implementation detail, scope, and
   presentation.
2. **The PM creates the project and tasks in Linear**, deliberately shaped for
   **maximum parallelism**, with dependencies and priority so the starting point
   is unambiguous.
3. **Conductor**, integrated with Linear, takes over. It works in **worktrees**
   *"so it doesn't mess things up."*
4. He opens as many branches in parallel as the project allows.
5. `CLAUDE.md` points at an **`elixir-agent`** and a **`test-specialist`**, which
   **agree function contracts** and then work in parallel.
6. One **server per worktree**. After implementation a **`qa-tester` agent** tests
   **in Chrome via DevTools**; on error, loop.
7. He reviews, applies changes, opens the PR, CI runs; anything wrong goes back to
   the start.
8. After CI and QA pass, **manual test** — optionally against **staging** pushed
   from the worktree terminal.
9. Validated → merge → **CD to prod** → manual test again → **watch metrics, logs
   and traces in AppSignal**; if wrong, loop.
10. **Track adoption in PostHog** → new ideas → begin again.

## Why he built it this way

> "Man does not live by shipfast alone, this was the flow I arrived at to
> prioritize reliability, prevent slop, improve quality, ensure everything is
> tested, reduce bug and support tickets."

**The goal is reliability, not speed.** Almost every stage is a gate: QA agent, CI,
manual test, staging, post-deploy verification, metrics. He reports running **up to
8 tickets in parallel** while only reviewing and validating at the end.

On the design agent, unprompted: it isn't there to replace a designer — *"meu
designer tá ocupado e só quero otimizar tomada de decisão."* A stand-in for
availability, not for the role.

## Two things from the replies

**Linear has its own agent.** Asked whether he'd tried it, Packer said not yet, and
that from what he'd seen it does roughly what he does with Claude Code in the first
stage — *"talvez menos flexível."* Worth knowing, since Livemode is migrating to
Linear ([[2026-08-14 Migrate project management from Jira to Linear]]).

**Instructions belong in files, not memory.** From Seb Galindo, running a similar
setup:

> "write agent instructions to files, not memory. memory compacts and agents
> forget context mid-task. files persist across sessions and every agent reads the
> same source of truth."

That is an outside practitioner arriving at the premise this wiki is built on, and
it is a direct design input for **A6 Curator**: shared institutional memory as
durable files that every agent reads, rather than per-agent context.

## What it adds beyond part 2

**Two levels of parallelism, not one.** Part 2 covers parallelism across tickets
via the dependency graph. This post shows parallelism *inside* a ticket: the
implementation and test agents **agree function contracts up front** and then work
simultaneously. Different mechanism, different level.

**Role-specialized agents.** `product-manager`, `design-specialist`,
`elixir-agent`, `test-specialist`, `qa-tester` — a fixed cast by role, closer to
[[Agent Flow]]'s named A1–A14 than to A9's "create sub-agents on demand."

**The loop closes on observability.** AppSignal for metrics, logs and traces;
PostHog for adoption; and adoption data feeds the next round of ideas. That is
**A11 Product Intelligence** in miniature — the instruction specifies A11 as
analysing DAU/WAU/MAU and feature usage to detect what's unused or misused
([[Fluxo Agêntico project instruction]]). Here it's a person reading dashboards,
but the loop is the same one.

**The tooling is unstable.** Conductor in March, **Orca** in July. Four months, one
swap. Worth weighing before committing Livemode's flow to any single orchestrator.

## Why it matters to [[Agent Flow]]

The **evolution between the two posts is the lesson**. Part 1 is a
quality-gate-heavy pipeline where a human dispatches each unit of work. Part 2
removes only that dispatch step — the gates, the specs, and the human merge review
all stay.

**He automated the coordination, not the judgement.** That is the same division
[[Fluxo Agêntico project instruction]] states as AI-First: agents execute, humans
approve. Here it's shown as a migration path rather than an end state, which is
more useful — it says which piece to automate first, and which to leave alone.

## Open questions

- What made him leave Conductor for Orca?
- Does the Linear agent overlap A7 Discovery or A8 Orchestrator enough to matter,
  now that Livemode is moving to Linear?
- His `qa-tester` drives Chrome via DevTools. Nothing in Agent Flow's 14 covers
  automated QA — is that a gap in the architecture, or deliberately out of scope?
