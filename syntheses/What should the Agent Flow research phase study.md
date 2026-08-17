---
type: synthesis
status: active
updated: 2026-08-17
date: 2026-08-17
aliases: [agent flow research, research agenda]
tags: [agents, research, planning]
---

# What should the Agent Flow research phase study?

> [!important] Rewritten 2026-08-17 after reading the project instruction
> The first version of this page was derived from the diagram and the transcripts.
> [[Fluxo Agêntico project instruction]] then arrived and **answered most of it**,
> including the problem this page led with. Corrections are kept visible below
> rather than quietly deleted.

## Correction: the sequencing problem doesn't exist

This page previously argued that A5 Watcher couldn't be built first because its
consumers (A3, the intelligences) don't exist, and that A6 Curator was a
precondition for everything. It concluded the first real decision was
*leaf-that-proves-plumbing versus hub-everything-needs*.

**The instruction dismisses that framing outright:**

> "A estratégia de construção é anárquica primeiro, integrada depois. Cada agente
> principal será construído de forma independente, validado isoladamente."

Phase 1 is explicitly agents **in production individually, without integration**,
and the stated rationale is fast iteration **without cross-dependency**
(*"sem dependência cruzada"*). A6 only begins capturing from everything in Phase 2.

So building a consumer-less A5 isn't a problem to solve — it's the method. The
reasoning was sound given the diagram alone; the diagram just wasn't the whole
design.

## What's now settled

| Question | Answer |
|---|---|
| Build order | Anarchic first — any agent, independently, closed scope, isolated validation |
| Deterministic vs autonomous | **Autonomous.** AI-First: *"a IA é o meio de execução"*; humans are strategic approvers and quality refiners, not operators |
| What feeds `Bug (sistema)` | A1 ingests *"alertas de sistemas"* as a first-class channel alongside Slack, Monday, email, forms, webhooks |
| Where it lives today | A **claude.ai Project**, with the instruction and diagram attached, and an unconfigured recurring-task facility |
| Working method | **One conversation per agent or per connection** |
| Agent spec template | Inputs/outputs · what it consults and feeds · success criteria · **limits (what it does not do)** |

That last row is effectively the required shape of the "understanding of each
agent" deliverable. It's a template, not a guess.

> [!tip] Worked through in full on 2026-08-17
> [[Which agent should be built first]] carries the complete argument — including
> the honest case *against* A5, why A1 is the better second build, and the
> proxy-telemetry target with its timing dependency on GC-5. The summary below
> stands; that page supersedes it on detail.

## A5 or A7? They answer different questions

Both are defensible and the wiki argued each separately without reconciling them.
Stating it plainly:

| | **A5 Watcher** | **A7 Discovery** |
|---|---|---|
| Claim | The safest **first build** | Where the **leverage** is |
| Why | Best-specified agent in the instruction; closed scope; needs no human initiative, so it sidesteps the adoption failure that killed the prior attempt | *"Colocar mais agentes não corrige uma especificação ruim."* Spec quality bounds everything downstream; A9's output is only as good as A7's PRD |
| Risk if wrong | Builds something useful but peripheral | Hard to validate alone — a PRD generator with no A8/A9 has no feedback loop |
| Evidence | [[Fluxo Agêntico project instruction]], Gabrielle's verbal suggestion | [[Gabriel Packer - DAG-driven agent orchestration]] |

**These aren't competing answers to one question.** Under anarchic-first, the first
build is a *learning* exercise — it needs a closed scope and a real user, which
favours A5. Leverage is a *sequencing* argument about where the programme's value
concentrates, which favours A7 and stays true whenever it gets built.

**Recommendation: A5 first, with A7's spec discipline applied to it.** Packer's
ticket template is a specification standard, not only an A7 feature — writing A5's
own spec to that bar (inputs/outputs, success criteria, explicit limits, rollout,
metrics) tests the discipline on something small before it has to carry a project.

## Track 1 — Choose the first agent and specify it (do this first)

Anarchic-first turns the question into: **which single agent has the most closed
scope and can reach production alone?**

**A5 Watcher is the strongest candidate, and the instruction makes it more so** —
it is the most concretely specified agent in the document:

- Fixed cadences: **system health every 5 min, usage patterns hourly, general
  report every 24h.**
- Two clear modes: **incident detection** (systems down, recurring errors, failing
  workflows) and **opportunity detection** (unused features, frequent manual
  processes, cross-area redundancy).
- One output, and while A3 doesn't exist it can go to a human.

Combined with Gabrielle's verbal suggestion to scope it to one project, and Orca
being business-critical ([[2026-08-14 Papo de Projetos]]), this is close to
decided. What remains is genuine specification work, not research:

- What "system health" means concretely for the chosen target, and what data
  source answers it every 5 minutes.
- Where alerts land while A3 is absent.
- **Its limits** — the instruction demands these explicitly. A watcher that
  detects "opportunities" could sprawl without them.

## Track 2 — Scheduling and runtime (newly the real unknown)

A5 needs to run **every 5 minutes, unattended**. That is now the sharpest open
question, and it's the same wall colleagues already hit:

- The Claude Project has a **Programado** (recurring tasks) section, currently
  unconfigured. Whether it can drive a 5-minute cadence is unverified and worth
  testing early — it would be the cheapest possible answer.
- **n8n covers scheduled and event-triggered runs**, which is precisely why
  colleagues build in Claude and migrate to n8n
  ([[2026-08-14 1-1 Matheus - Gabrielle]]). It's also in A9's sanctioned stack.
- The **sharing problem** is unresolved and is a runtime question, not a
  packaging one: work built in Claude can't easily be handed to a team. An agent
  only msilva can run or maintain repeats the prior failure in a new form.

**Token cost binds here**, not in the paradigm choice. A 5-minute health check is
~288 invocations a day. Against a recorded $7-per-day experimentation burn that
caused unease, cadence and context size are the cost levers — so "how much does
each cycle need to read" is a design constraint on A5 specifically.

> [!tip] Field evidence, 2026-08-17
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] turns the
> abstract token worry into a mechanism. That $7/day flow runs **on n8n**, and its
> probable causes are **wholesale context loading** (feeding an agent an entire
> 816-row table it only needs slices of) and **a loop repeating operations**.
> The working half of Gabriel's system is the half packaged as a **skill that
> fetches narrowly**; the broken half is the unpackaged one.
>
> Three transferable conclusions for A5:
> - **n8n is already the de facto runtime** for agent flows here — that shortens
>   this track considerably.
> - **The shared n8n licence is an operational constraint.** Finance shares the
>   instance; one person's executions crowd others' logs out of view, and a stuck
>   in-progress lock blocked debugging entirely. Any agent deployed there inherits
>   that.
> - **Narrow fetching is a cost lever**, decided per agent at design time.
>
> **Revised the same day, after part 2 of that session:** measured consumption for
> one clean run was **11 centavos** against ~670 earlier — so **$7/day is the bad
> case, not the norm**, and the likelier primary cause is a **runaway execution**
> (two workflows firing concurrently instead of chaining) rather than context
> volume. For a 5-minute cadence this reordering matters: **a broken trigger chain
> on a schedule compounds**, where an oversized prompt merely costs a fixed
> premium. A **token-consumption dashboard exists** and can be requested — get it
> before, not after, building anything that runs unattended.

## Track 2b — Prior art now in hand

[[Gabriel Packer - DAG-driven agent orchestration]] removes much of the guesswork
from the projects branch. It is a production-tested A7→A8→A9, and the transferable
parts are specific:

- **An explicit dependency graph, never inferred** — parallelism comes from the
  DAG, not from how many agents you start.
- **Waves off a fresh `origin/main`**, so no agent builds on unmerged code.
- **A ticket template detailed enough to be a prompt** — this is A7's output
  specification, already worked out.
- **Everything packaged as skills**, which is now the third independent
  convergence on packaging.
- **A human gate at merge**, matching the AI-First philosophy exactly.

[[Gabriel Packer - solo founder AI workflow (part 1)]] (2026-03-26) shows the same
system four months earlier, before dispatch was automated. **The delta is the
finding**: he automated the coordination and left every quality gate, the spec
discipline, and the human merge review untouched. If Agent Flow needs a migration
order rather than a target state, that's it — automate dispatch first, judgement
never.

Part 1 also contributes:

- **Instructions in files, not memory** — *"memory compacts and agents forget
  context mid-task; files persist and every agent reads the same source of
  truth."* A concrete design input for **A6 Curator**.
- **Two levels of parallelism**: across tickets via the DAG, and *within* a ticket
  by having implementation and test agents agree function contracts first.
- **The loop closes on observability** — AppSignal and PostHog, with adoption data
  feeding the next round. That's **A11 Product Intelligence** done by a human.
- **Linear ships its own agent**, which Packer judged similar to his first stage
  but *"talvez menos flexível."* Worth checking, given the Linear migration.

Caveats before borrowing wholesale: he is a **solo founder on one codebase**; his
decomposition is **one agent per ticket** where A9 is specified to create
sub-agents on demand; and **the tooling is unstable** — he swapped Conductor for
Orca inside four months, which argues against committing Livemode's flow to any
one orchestrator.

## Track 3 — Adoption: invocation, not friction

**Reframed 2026-08-17 by msilva.** The previous attempt died because **it was never
called** — the bot sat on one Slack channel waiting to be `@`-mentioned, while
people report problems through whatever path is at hand: a DM, another channel, a
meeting, walking over. **Channel fragmentation**, not unwillingness to file.

That splits the adoption problem in two, and the design handles them unequally:

| Half | Addressed? |
|---|---|
| **Fragmentation** — reports arrive by many paths | **Yes.** A1 captures from Slack, Monday, email, forms, webhooks, system alerts |
| **Invocation** — the agent must still be addressed | **No.** If each channel needs a form, a webhook or a mention, the same failure is rebuilt six times over |

So the live question is: **does A1 listen passively, or does it wait to be
addressed?** Passive listening is a materially different system — classifying a
firehose, with cost, noise and privacy consequences nobody has discussed. Explicit
invocation is cheap and repeats the recorded failure. Nothing in the instruction
picks a side.

**Worth noting in A5's favour**: its input is a system alert — **one channel, no
fragmentation, no invocation, no human in the loop at all.** It is the only agent
immune to *both* halves of the problem. Choosing A5 first sidesteps the failure
mode rather than betting against it, which is a stronger argument for it than "it
is well specified."

## Track 4 — Two documented inconsistencies to confirm

Cheap to resolve, both with Gabrielle before **2026-08-24**:

- **Monday is listed as an intake channel**, but tracking is migrating to Linear
  ([[2026-08-14 Migrate project management from Jira to Linear]]). Is the
  instruction stale here?
- **A9's controlled stack omits Go** (React, Python, Node.js, known APIs, N8n,
  Vercel/Replit) — yet the [[Airtable Proxy]] is Go. Does the constraint apply
  only to new systems?

## The calendar

**Gabrielle is on leave from the week of 2026-08-24** for ~2.5 weeks. **Luís is
the primary technical contact** and works afternoons only. A deadline-setting
meeting with both was set for the end of the week beginning 2026-08-17.

1. **Before 2026-08-24** — confirm the first agent choice with Gabrielle, plus the
   Track 4 inconsistencies and where the still-unshared design docs are.
2. ~~**From Luís** — his graph suggestion and the model he shared.~~ **Obtained
   2026-08-17**: [[Gabriel Packer - DAG-driven agent orchestration]]. "Gráfis"
   meant a **dependency graph driving execution waves**. Still worth asking what
   he thought transferred, but the artifact no longer blocks anything.
3. **During the leave** — Tracks 1 and 2 are specification and experiment, and
   need neither of them.

## Open questions

- Which agent does msilva actually build? Not stated in writing anywhere.
- What counts as "production" for a Phase 1 agent with no integration?
- Is there a token budget, or only the unease recorded in the 1:1?
- Does the homologation flow from [[2026-08-14 Papo de Projetos]] gate this work?
