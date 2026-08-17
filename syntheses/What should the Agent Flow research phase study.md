---
type: synthesis
status: active
updated: 2026-08-17
date: 2026-08-17
aliases: [agent flow research, research agenda]
tags: [agents, research, planning]
---

# What should the Agent Flow research phase study?

Derived entirely from what the wiki already holds — [[Fluxo Agêntico diagram]],
[[2026-08-10 Onboarding Técnico - Matheus]], [[2026-08-14 1-1 Matheus - Gabrielle]],
[[2026-08-14 Papo de Projetos]]. No new external research yet; this is the agenda,
not the answers.

## The deliverable, restated

Two things, from the onboarding and the 1:1:

1. An understanding of what each of the 14 agents is for.
2. **One agent actually built**, plus a proposal for which one and where it lives.

**(1) is already largely done** — [[Fluxo Agêntico diagram]] covers all 14 roles.
The research phase is therefore mostly in service of (2): choosing correctly and
building something that survives contact with users.

## The sequencing problem nobody has named yet

Gabrielle's suggestion is to build **A5 Watcher** first, scoped to one project.
Reading that against the diagram raises two structural problems:

**A5's consumers don't exist.** The diagram gives A5 two outputs: a feedback loop
into **A3 Executor** (*"retroalimenta execução"*) and dotted context lines into the
transversal intelligences. Build A5 alone and it has nowhere to send anything. So
the first real design question isn't "how do I monitor" — it's **what a standalone
A5 does with its output when A3 doesn't exist.** Plausible answers: it alerts a
human directly (a Slack channel), or it writes into A6, or it degenerates into the
alerting the [[Airtable Proxy]] dashboards already do.

**Almost everything depends on A6 Curator.** A6 is the hub every other agent
connects to. Any agent built before it either invents its own memory or has none.
That makes A6 less "one of the 14" and more a **precondition** — which is
awkward, because A6 is also the agent that most overlaps something that already
exists (`Brain`).

**Consequence for the research phase**: the choice isn't A5-vs-something-else. It's
whether the first build is *a leaf that proves the plumbing* (A5, needing a
defined output sink) or *the hub everything needs* (A6, needing a `Brain`
decision first). That's the question worth resolving before writing code.

## Track 1 — Resolve A6 against `Brain` (blocking)

**Why first**: it's the architectural centre, it gates the other 13, and the
answer is a conversation, not an experiment.

- Read the **`Brain` repository** — msilva has GitHub access
  ([[2026-08-14 Papo de Projetos]]). What's actually in it, how it's structured,
  how the Hub renders it, how stale it is.
- Establish whether A6 **is** `Brain` (an agent maintaining an existing repo),
  **wraps** it (a retrieval layer over it), or **replaces** it.
- Note the precedent staring at us: **this wiki is an LLM-maintained knowledge
  base with a schema, an index, and a log.** If A6 is "an agent that maintains
  institutional memory in Markdown under governance," it already has a working
  prototype in `C:\Users\msilva\Documents\work`. That's an argument worth making
  from evidence rather than assertion.

## Track 2 — The deterministic/autonomous decision

The fork msilva named in the 1:1: a largely deterministic flow, versus exposing
tools and letting agents resolve things. Luís suggested **graph-based**
approaches *(unverified: transcription garbled — confirm what he meant)*.

What makes this decidable rather than a matter of taste, given the recorded
constraints:

| Constraint | Pressure it applies |
|---|---|
| **Token cost** — $7/day in one colleague's testing; *"se for gastar 7 todo dia […] talvez a gente não sabe se a gente tá confortável"* | Toward deterministic. Every autonomous hop costs tokens and varies |
| **A13 blocks** — a gate with authority to stop work | Toward deterministic. A blocking gate needs predictable, auditable behaviour |
| **Prior attempt died partly because agents couldn't call each other** | Toward explicit orchestration — a graph — over implicit tool-calling |
| **A3 and A9 both "create sub-agents on demand"** | Toward autonomy, at least inside those two nodes |

The last row matters: the diagram is **not uniform**. Two nodes are explicitly
dynamic while A13's blocking role demands determinism. So the honest question is
*which parts are deterministic*, not which paradigm wins. Worth studying
orchestration frameworks with that hybrid in mind, and worth getting the model
Luís shared, which reportedly addresses exactly this.

## Track 3 — Adoption, which killed the last attempt

The most under-researched area, and the one with the clearest recorded failure
data. From [[Agent Flow]]: the prior product agent worked technically but died
because users had to `@`-mention it and didn't, and because *"as pessoas quando
tem um problema, elas estão um pouco engajadas em explicar o seu problema"* —
people want to report a problem, not file one.

Nothing in the 14-agent diagram addresses this. A1 Receptor Universal assumes
demands arrive; it doesn't explain why anyone would send them.

Study questions:

- What makes the four entry channels **zero-friction**? `Bug (sistema)` is
  automatic by construction — the other three depend on human behaviour that has
  already failed once.
- Is there a design where the agent **observes** rather than waits to be
  addressed? This is why `Bug (sistema)` and A5 Watcher are interesting beyond
  their stated roles: they're the only paths that need no human initiative.
- The **sharing problem** from the 1:1: work built in Claude can't easily be
  handed to a team, n8n covers scheduling and event triggers that Claude alone
  doesn't, and colleagues therefore build in one and migrate to the other. **A
  platform where flows live and are maintained collaboratively is the gap this
  project exists to fill** — so "where does it run" is an adoption question, not
  just an infrastructure one.

## Track 4 — What feeds `Bug (sistema)`

The cheapest concrete win available, and the only identified seam between
msilva's two projects.

The [[Airtable Proxy]] already emits telemetry, has dashboards for anti-patterns,
and fires `airtable_429_alert`. A first confirmed over-fetch has been found in
[[LiveScript]]'s events panel. If proxy alerts become `Bug (sistema)` events, the
channel has a real producer and A5 has something to watch — using work that
already exists rather than new infrastructure.

Worth testing whether this is what was intended, or coincidence.

## Sequencing, and the calendar

**Gabrielle is on leave from the week of 2026-08-24** for ~2.5 weeks, returning
the 10th. **Luís is the primary technical contact** and is **part-time,
afternoons** ([[2026-08-14 Papo de Projetos]]). A deadline-setting meeting with
both was set for the end of the week beginning 2026-08-17.

That gives a clear ordering:

1. **Before 2026-08-24** — ask Gabrielle the things only she can answer: what
   feeds `Bug (sistema)`, why the agents were renumbered, whether A6 is meant to
   be `Brain`, and where the still-unshared design documentation is.
2. **Also before the deadline meeting** — get Luís's graph suggestion and the
   model he shared, since Track 2 can't resolve without it and he's the one
   present after Gabrielle leaves.
3. **During the leave** — Tracks 1 and 3 are reading and analysis; they don't
   need either of them.

## Open questions this agenda can't resolve

- Which is the first build: a leaf that proves plumbing, or the hub everything
  needs?
- Is there an existing budget or token-cost ceiling, or just the unease recorded
  in the 1:1?
- Does the homologation flow from [[2026-08-14 Papo de Projetos]] apply to this
  project? It validates architecture, tooling, security, and data governance
  before implementation — which would make it a gate on this work.
- Who owns A6 if `Brain` already has an owner?
