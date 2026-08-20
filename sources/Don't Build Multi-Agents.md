---
type: source
status: active
updated: 2026-08-20
date: 2025-06-12
aliases: [cognition multi-agent, walden yan, don't build multi-agents, context engineering principles]
source: "raw/Clippings/Don't Build Multi-Agents.md"
url: "https://cognition.com/blog/dont-build-multi-agents"
tags: [agents, orchestration, prior-art, opinion, context-engineering]
---

# Cognition — "Don't Build Multi-Agents"

Blog post by **Walden Yan** at Cognition (makers of Devin), published
**2025-06-12**. Clipped 2026-08-20.

The **primary source** behind a claim already in the wiki at second hand:
[[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] cites this
post as "external corroboration" for its anti-orchestration argument, without
quoting it directly. This page reads it first-hand.

## Two principles of context engineering

1. **Share context, and share full agent traces, not just individual
   messages.** A subagent handed only its own subtask, without the full
   history of how that subtask was decided, will misinterpret it — even a
   perfect summary loses details that turn out to matter.
2. **Actions carry implicit decisions, and conflicting decisions carry bad
   results.** Even with full shared context, two agents acting in parallel
   each make small, unstated judgment calls (a visual style, a naming
   convention, an assumption about scope) that the other can't see coming and
   that don't reconcile automatically.

## The worked example

A "build a Flappy Bird clone" task is split into "build the background" and
"build the bird," run as parallel subagents:

- **Without shared traces**: subagent 1 mistakes the subtask and builds a
  Super Mario-style background; subagent 2's bird doesn't move like a Flappy
  Bird asset. The final agent is left reconciling two miscommunications.
- **With shared traces, still parallel**: both subagents understand the task
  correctly, but produce **incompatible visual styles** — neither one saw the
  other's implicit styling decisions as they were made. This is Principle 2:
  fixing Principle 1 alone doesn't save you.

## Recommended architecture

**Single-threaded linear agent** is the default: continuous context, no
cross-agent decision conflicts, and it "will get you very far." Its known
failure mode is context-window overflow on very long tasks.

For tasks long enough to overflow context, Yan describes (not prescribes as
easy) introducing a **dedicated compression agent** whose job is to
distill history into key decisions and events — *"hard to get right"*, and
Cognition has fine-tuned a smaller model for this in at least one case. Even
this has a ceiling; he explicitly leaves "manage arbitrarily long contexts
better" as an open problem for the reader.

## Real-world examples cited

- **Claude Code's own subagents** (as of June 2025): they never run *in
  parallel* with the main agent, and are scoped to **answering a
  well-defined question, not writing code**. Yan's reading: giving a
  subagent that kind of narrow, non-code-writing scope is precisely what
  avoids the implicit-decision-conflict problem, at the cost of not getting
  true parallelism. The benefit kept: the subagent's investigative work
  doesn't bloat the main agent's context.
- **2024-era "edit apply" models** (used by Devin and others): a large model
  wrote a markdown explanation of a code change, a small model applied it as
  a literal rewrite of the file — more reliable in 2024 than asking a large
  model for a properly formatted diff, but still faulty: the small model
  routinely misread ambiguous instructions. By the time of writing (2025),
  a single model does both steps in one action.
- **Multi-agent-agents-negotiating-like-humans**: the aspirational fix (let
  conflicting agents "talk it out," the way engineers resolve a merge
  conflict) is judged not yet viable — LLMs in 2025 can't sustain the kind of
  long-context, proactive discourse that makes human negotiation work, and
  Yan sees no one seriously working on the underlying cross-agent
  context-passing problem yet.

## Why it matters to [[Agent Flow]]

- **Principle 2, applied literally, is the strongest single argument in the
  wiki against treating Agent Flow's transversal cluster as safely
  parallel.** [[Fluxo Agêntico diagram]]'s own edges show **A10 ↔ A11 ↔ A12**
  as bidirectional, not independent — this is exactly the shape Yan warns
  about, not the map-reduce shape multi-agent systems are good at. See the
  sharper version of this argument, cross-checked against
  [[How we built our multi-agent research system]], on [[Agent Flow]]
  itself.
- **The Claude Code subagent example is a direct, dated data point for
  [[Claude Agent SDK]]**: Cognition's own reasoning for keeping subagents
  non-parallel and question-only matches Luís's instinct
  ([[2026-08-19 1-1 Matheus - Luís]]) that dev-subagent design is
  project-harness scope, not Agent Flow architecture — independently
  arrived at, about a year apart.
- **A7 Discovery generating "a complete PRD" for A8/A9 to build from** is a
  single large upfront artifact handed to downstream agents — closer to
  Yan's "copy the original task as context" attempt than to sharing full
  agent traces. Already flagged against [[Gabriel Packer - DAG-driven agent orchestration]]'s
  narrower, per-ticket practice on [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]];
  this page adds the same objection from its primary source rather than a
  paraphrase of it.

Not folded into any Agent Flow decision — registered as a counterpoint to
weigh, same standing as the Akita post it's cited from.

## Open questions

- Cognition doesn't say what its fine-tuned compression model looks like or
  how well it scales — "hard to get right" is asserted, not demonstrated with
  numbers, unlike the Anthropic and Akita posts which both cite concrete
  measurements.
- Written a year before [[How we built our multi-agent research system]]
  (2025-06-12 vs. 2025-06-13) — almost the same week, and arguably in
  tension: Anthropic shipped a multi-agent system days later reporting a
  90.2% improvement over single-agent. Worth reading side by side rather than
  as agreeing sources; see the comparison on [[Agent Flow]].
