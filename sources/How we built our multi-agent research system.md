---
type: source
status: active
updated: 2026-08-20
date: 2025-06-13
aliases: [anthropic multi-agent research, orchestrator-worker, anthropic research system]
source: "raw/Clippings/How we built our multi-agent research system.md"
url: "https://www.anthropic.com/engineering/multi-agent-research-system"
tags: [agents, orchestration, prior-art, anthropic, context-engineering]
---

# Anthropic — "How we built our multi-agent research system"

Published **2025-06-13** by Anthropic. Written by Jeremy Hadfield, Barry
Zhang, Kenneth Lien, Florian Scholz, Jeremy Fox, and Daniel Ford
(Acknowledgements section).

> [!warning] Sourcing note
> `raw/Clippings/How we built our multi-agent research system.md` captured
> only the frontmatter and title — the clipping tool didn't pull the body.
> This page's content comes from a **live WebFetch** of the URL above
> (2026-08-20), not from the raw file. Re-clip the raw file properly if a
> verbatim local copy is ever needed; until then this page is sourced
> directly from the live page, cited as such rather than as `raw/`.

Cited **second-hand** already, before this ingest: [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]]
uses this post's caveats (15x tokens, coding is a poor fit, 80% of gains from
tokens not architecture) as corroboration for its anti-orchestration
argument. Reading the actual post shows that's a **selective read** — see
below.

## What it describes

Anthropic's Research feature: an **orchestrator-worker architecture** where
a lead agent (Claude Opus 4) analyzes the query, plans a research strategy,
and spawns subagents (Claude Sonnet 4) that search **in parallel**. A
`CitationAgent` post-processes findings for attribution; a memory system
persists context as agents approach token limits.

## Headline result

The multi-agent system **outperformed single-agent Claude Opus 4 by 90.2%**
on Anthropic's internal research eval. This is the fact Akita's citation
omits — the post isn't an argument against multi-agent systems, it's a
build report for one that worked, with caveats attached.

## Token economics

- Agents use ~4x more tokens than a chat interface; **multi-agent systems
  use ~15x more tokens than chat**.
- Only worth it "for tasks where the value of the task is high enough to pay
  for the increased performance."
- On the BrowseComp eval, **three factors explain 95% of performance
  variance**: token usage alone accounts for 80%, tool calls and model
  choice the rest. Upgrading the subagent model (Sonnet 3.7 → 4) beat
  *doubling* the token budget on the older model — architecture and model
  quality both matter more than raw spend, but spend still dominates the
  variance.

## The passage that matters most for this wiki

> "Some domains that require all agents to share the same context or involve
> many dependencies between agents are not a good fit for multi-agent
> systems today."

Paired with the positive case — multi-agent is a good fit for "valuable
tasks that involve heavy parallelization, information that exceeds single
context windows, and interfacing with numerous complex tools," and
specifically **breadth-first** research where independent directions don't
need to talk to each other.

Named poor fits: **most coding tasks** ("fewer truly parallelizable tasks
than research"), and — the general case this specializes — anything needing
shared context or heavy inter-agent dependency, because "LLM agents are not
yet great at coordinating and delegating to other agents in real time."

This is the same claim as [[Don't Build Multi-Agents]]'s Principle 1 & 2, in
Anthropic's own words, arrived at independently (both posts within a day of
each other, June 2025) from production experience rather than theory.

## Other engineering findings

- **Prompt engineering for the orchestrator**: explicit effort-scaling rules
  work better than vague instructions — e.g. "simple fact-finding needs 1
  agent, 3-10 tool calls; comparisons need 2-4 subagents, 10-15 calls each."
  Vague delegation ("research the semiconductor shortage") reliably
  underperforms a detailed task spec (objective, output format, tool
  guidance, boundaries).
- **Self-improvement loop**: using Claude itself to diagnose why subagents
  failed and rewrite tool descriptions cut task completion time by 40% in
  one experiment.
- **Evaluation**: started with ~20 real-usage queries rather than waiting for
  a large eval set; LLM-as-judge scored factual accuracy, citation accuracy,
  completeness, source quality, and tool efficiency, but **human testing
  caught what automated grading missed** — testers found agents
  systematically preferred SEO content farms over authoritative sources like
  academic PDFs.
- **Production engineering**: durable execution with checkpoints (agents run
  many turns, need to resume without a full restart), full production
  tracing for debugging non-deterministic behavior, and "rainbow
  deployments" (old and new versions running simultaneously) to avoid
  disrupting in-flight agent runs.
- **Known architectural bottleneck, named as future work**: subagents
  currently execute *synchronously* — the lead agent waits for each to
  finish. Async execution (agents running concurrently, spawning subagents
  as needed) is desired but explicitly flagged as adding complexity in
  "result coordination, state consistency, and error propagation" — i.e. the
  same coordination problem as the "poor fit" passage above, acknowledged as
  unsolved even inside their own working system.

## The sharpest caveat

> "One step failing can cause agents to explore entirely different
> trajectories, leading to unpredictable outcomes."

Stated as the reason "the gap between prototype and production is often
wider than anticipated" for agentic systems — compounding error as a
production risk they hit directly, the same mechanism [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]]
models abstractly with his 0.9^10 math.

## Why it matters to [[Agent Flow]]

Checked against [[Fluxo Agêntico diagram]]'s actual edges, not just the
verbal description, almost nothing in the 14-agent design currently reads as
the "good fit" case this post describes:

- **A10 ↔ A11 ↔ A12 are bidirectional** ("A10 feeds A11, which produces the
  reports A10 surfaces," per [[Agent Flow]]) — exactly "many dependencies
  between agents," not independent parallel lanes.
- **A5 → A3 is a direct feedback loop** ("retroalimenta execução"), not two
  agents working independently.
- **Every agent feeds A6 Curator**, and A6 is meant to be an interface layer
  every agent consults — a shared-context hub, which the passage above names
  as a poor fit, not a mitigation for one.
- **A7 → A8 → A9 is a sequential dependency chain** (PRD → orchestration →
  build), not parallel decomposition — closer to Anthropic's own
  orchestrator-worker shape *if* A8's dispatched work to A9 were genuinely
  independent per unit, but A7's single upfront PRD is a shared-context
  artifact all of it depends on, unlike Anthropic's own subagents which each
  get task-specific delegation, not a shared master document.

The one part of Agent Flow that might genuinely fit the positive case is
**A9/A3 spinning up sub-agents on independent units of work** — if those
units don't share state, that is Anthropic's actual "good fit" shape. That
distinction (independent dispatch vs. a shared plan every dispatched agent
reads first) is the one worth testing before assuming any part of the
architecture clears this bar; nothing in the current spec settles it either
way.

Not folded into any Agent Flow decision — registered as a counterpoint (and,
via the 90.2% figure, a partial complication of the counterpoint) per
msilva's direction, 2026-08-20.

## Open questions

- Published one day after [[Don't Build Multi-Agents]] (2025-06-13 vs.
  2025-06-12) — worth knowing whether either team was aware of the other's
  post, or if this is independent convergence. Not stated in either source.
- Does A9/A3's "creates sub-agents on demand" actually dispatch independent,
  non-communicating units of work, or a shared plan each subagent reads
  first? Nothing in [[Fluxo Agêntico project instruction]] settles this, and
  it's the exact fork this post's "good fit" vs. "poor fit" line runs along.
