---
type: decision
status: active
updated: 2026-08-24
date: 2026-08-24
decided_by: Matheus Silva
source: "chat with Claude, 2026-08-24"
tags: [agents, agent-flow, a10, a14, planning, decision, poc]
aliases: [A10+A14, build A10 and A14 together, M1 scope]
---

# Build A10 Portfolio and A14 PM Agent together, PoC first

**Decision.** msilva builds [[Agent Flow]]'s **A10 Portfolio** and **A14 PM
Agent** as one combined effort, not sequentially — starting with a **PoC**
before committing to production, extending the LangGraph-vs-Claude-Code
prototype Luís already tasked
([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]) to cover both
agents' scope instead of A10 alone. This is the scope of **M1** on the
[[Fluxo Agêntico project instruction|Fluxo Agêntico]] Linear project.

## Why

Resolves the question [[2026-08-24 Start Agent Flow with A10 Portfolio]] left
open: whether A10 and A14 are one build or two. Both were named together from
the start — Gabrielle's, Luís's and Carol's convergence on "the team's own
cross-project pain" is really two faces of the same gap: no backlog-wide
prioritization view (A10) and no proactive status/delivery communication
(A14) — see [[Comparing the first-agent candidates]]'s framing of
"A10+A14" as one pain. Building them together:

- **Doesn't break anarchic-first.** Neither agent depends on the other's
  decisions, or on an agent that doesn't exist yet — per
  [[Fluxo Agêntico project instruction]], A10 *"sugere, nunca executa"*, and
  A14 in this phase just needs something already tracked to report on.
- **Reuses the PoC Luís already assigned**, rather than opening a second
  validation process for a second agent.
- **Applies Carol's own prioritization test reasonably well** — her
  work-effort-savings and quality-improvement variables both favor this pair
  (poupa o recap manual hoje feito na mão; melhora visibilidade sem depender
  de feeling) — see [[2026-08-24 Agent Flow discovery with Carol]] for the
  framework itself.

## What this doesn't settle

- **A14 is scoped down for this phase.** Its full spec — following a request
  through to delivery — presupposes a pipeline (A1/A2/A7/A8) that doesn't
  exist yet ([[Comparing the first-agent candidates]], "Also considered,
  briefly": *"Follows a request through to delivery — presupposes the
  pipeline exists"*). In this phase A14 reports on whatever is already
  tracked (mainly Linear), not on a request it received itself end-to-end.
- **Kept as two specs, one build.** Each agent still gets its own
  actor→input→output frame — inputs/outputs, what it consults/feeds,
  success criteria, limits — per Luís's working method
  ([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]). One
  succeeding while the other doesn't is a real possible PoC outcome, and the
  separate specs are what let that show up instead of being averaged away.
- **Production timeline isn't set.** The PoC is the gate; if it doesn't
  validate the approach, M1 doesn't roll into a production build as scoped
  here.

## What this changes elsewhere

- [[Agent Flow]] — noted as the concrete shape of the next build.
- [[2026-08-24 Start Agent Flow with A10 Portfolio]] — its open "A10+A14, one
  build or two" question is answered here: **one build, two specs.**
