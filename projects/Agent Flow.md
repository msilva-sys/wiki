---
type: project
status: active
updated: 2026-08-17
aliases: [fluxo, fluxo de agentes, agent architecture, the agent project]
tags: [agents, llm, automation, onboarding]
---

# Agent Flow

A proposed multi-agent architecture to automate how msilva's area receives,
classifies, and executes demands. msilva's **second onboarding track**, running
in parallel with the [[Airtable Proxy]] but with no delivery deadline —
[[2026-08-10 Onboarding runs proxy and agent flow in parallel]].

> [!info] Status: design only
> Described as *"um desenho inicial"* — an initial drawing. Validated
> conceptually, but **no decision on where it would live or how it would be
> built**. Design documentation and recorded design conversations exist and were
> to be shared with msilva.

## The three fronts it automates

The architecture mirrors how the area actually works today:

1. **Project creation** — new projects, discovery through delivery.
2. **Enablers** (*habilitadores*) — a teaching role; sitting with people to
   unblock them and help them level up.
3. **Quick execution** — pointed demands on things already built: production
   breakage, small fixes, a newly requested feature.

## Proposed shape

**Receptor agents** accept incoming demands from any of the three fronts, then a
**classification flow** routes each demand to a matching pipeline:

| Route | Pipeline |
|---|---|
| New project | Discovery / product → orchestrator → developer, the developer working with subagents |
| Enablement | A "tier"-style agent that helps a person get unstuck |
| Quick execution | Maintenance and bugfixes on existing projects |

**Transversal agents** sit across all routes, feeding and being fed by the
others:

- **Portfolio** — suggests prioritization, including what the other agents
  should be working on first.
- **Product** — are shipped products actually being used? What insight comes out
  of usage, and what fixes or new demands does it imply?
- **Data governance** — deduplication: making sure the same thing isn't built
  twice, and that concepts, ideas, and patterns get reused.
- **Delivery tracking** — follows a product from arrival to delivery, taking
  that off a person's hands manually.
- **Monitoring** — watches everything and raises alerts. Explicitly tied to the
  telemetry the [[Airtable Proxy]] produces.
- **Institutional memory** — a central store of everything known about the
  area's projects. Described as intranet-like.

A longer-term ambition: once built for this area, the flow could be adapted and
reused by other areas, helping them produce more structured projects without
routing through the projects team.

## Prior attempt — failed on adoption, not capability

A product agent was built once before, as a **custom GPT** scoped to a single
project and dropped into that project's group chat. Worth reading before
designing the next one:

- **It worked technically.** It investigated reported problems, checked whether
  something had been recently implemented or was sitting in the backlog, and
  answered. *"O funcionamento dele foi OK."*
- **It failed on engagement.** Users had to **@mention it every time**, and they
  didn't. Gabrielle's observation is the sharpest lesson in the transcript:
  people with a problem are not motivated to explain their problem carefully —
  *"elas querem falar que tem um problema"*. They want to report it, not file it.
- **It read as a chatbot, not a colleague** — occasionally strange replies, and
  Gabrielle kept having to step in and complete its answers.
- **No agent-to-agent interaction.** It could not call another agent, which is
  the premise the whole architecture rests on.

It was abandoned. The flows *"não necessariamente eram conversacionais entre si"*.

## msilva's deliverable

1. An understanding of what each proposed agent is for.
2. **One agent actually built.**
3. A proposal for which one, and where its first version lives.

Gabrielle's suggestion: start with the **monitoring/watch agent**, scoped to a
single project rather than the whole portfolio — [[LiveScript]] or "Orca" were
named, as the systems under most constant use. Scoping it to one project is how
the agent picks up real context and gets working in practice.

## Open questions

- Which agent to build first, and where does it live?
- What makes an agent get used, given the @mention failure above? Nothing in the
  design addresses adoption.
- How do agents actually talk to each other? The unsolved problem that killed
  the prior attempt.
- Is **Orca** a distinct system? (unverified — named once, transcription
  uncertain)
