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

## Constraints that surfaced later

**Token cost is a first-class design constraint, not an afterthought.** A
colleague's flow burned roughly **$7 in a single day of testing**. Gabrielle's
framing: a flow running once a month in production at that cost is fine; the same
spend every day through a long experimentation phase is not obviously
sustainable. Optimizing token consumption — context, memory, how much the agent
is handed — is an explicit goal
([[2026-08-14 1-1 Matheus - Gabrielle]]).

**Sharing is the underlying problem.** Work built in Claude can't easily be
handed to a team — at best a shared workspace, or packaging it as a skill to
distribute. n8n covers scheduled and event-triggered runs that Claude alone
doesn't, which is why colleagues build in one and move to the other. **A
platform where agent flows can live and be maintained collaboratively is the gap
this project exists to fill.**

**Scope, as msilva described it to the team**: automate *"desde os PRDs […] até a
entrega"* — from PRDs through delivery ([[2026-08-14 Recap da Semana]]). Narrower
and more concrete than the full architecture diagram, and it makes the PRD corpus
flagged in [[Proxy Environments]] directly relevant input.

Work starts **2026-08-17**, splitting time with the [[Airtable Proxy]].

## msilva's deliverable

1. An understanding of what each proposed agent is for.
2. **One agent actually built.**
3. A proposal for which one, and where its first version lives.

Gabrielle's suggestion: start with the **monitoring/watch agent**, scoped to a
single project rather than the whole portfolio — [[LiveScript]] or Orca were
named, as the systems under most constant use. Scoping it to one project is how
the agent picks up real context and gets working in practice.

> [!tip] Orca is the stronger candidate
> [[2026-08-14 Papo de Projetos]] describes Orca as a **machine-learning system,
> in production, that the team using it cannot work without** — its automation is
> valued at roughly ten headcount, and *"isso não pode estar fora do ar."* A
> business-critical system that must not go down makes the value of a monitoring
> agent self-evident, which is exactly what the prior attempt failed to achieve.
> [[LiveScript]] is the system msilva knows better; Orca is the one where an
> alert would obviously matter.

## Prior art to read first

Livemode already has more in this space than the architecture diagram suggests:

- **The `Brain` repository and Hub** — a company-wide Markdown-in-GitHub
  knowledge base with per-project architecture, decisions, and README pages.
  It substantially overlaps the proposed institutional-memory agent, and it
  already exists. See [[2026-08-14 Papo de Projetos]].
- **A shared AI architecture for Livemode**, named as a long-term goal by the
  area lead, with no detail given.
- **A Claude Code training programme** being built for all areas.
- **TES**, an AI-orchestration vendor under trial
  ([[2026-08-14 Recap da Semana]]).

None of these were mentioned when the project was handed over. Understanding how
Agent Flow relates to them — complement, replacement, or duplicate — is worth
doing before designing, particularly given that one of the transversal agents is
explicitly about **not building the same thing twice**.

## Design approach — undecided

msilva's own framing of the fork, from [[2026-08-14 1-1 Matheus - Gabrielle]].
Nothing is settled; these are the axes he named as needing study:

- **Deterministic vs. autonomous.** Either the flow is largely deterministic, or
  the agents are simply *"mostrar as ferramentas pros agentes […] para ele
  resolver as coisas"* — handed the tools and left to work it out. The prior
  attempt's failure was partly that its flows *"não necessariamente eram
  conversacionais entre si"*, which argues for thinking about this explicitly
  rather than defaulting.
- **Graphs.** Luís suggested graph-based approaches — recorded as *"o Luiz já deu
  umas ideias de questão de gráfis"*. (unverified: transcription is garbled;
  confirm whether he meant state graphs / a specific framework)
- **Orchestration shape** — how the orchestrating agent works, unaddressed.

msilva has done related work before, with people at his previous employer, so
this isn't a standing start.

Gabrielle's framing of ownership: *"é teu filho e teu projeto"*, with Luís as
consultant rather than validator.

## Open questions

- Deterministic, tool-exposure, or a mix? See above — the first real design call.
- Which agent to build first, and where does it live?
- What makes an agent get used, given the @mention failure above? Nothing in the
  design addresses adoption.
- How do agents actually talk to each other? The unsolved problem that killed
  the prior attempt.
- ~~Is **Orca** a distinct system?~~ **Resolved 2026-08-17.** Orca is real, and
  is cited as *the* example of a project essential to Livemode's operations
  ([[2026-08-14 Papo de Projetos]]). That makes it a credible first target for
  the monitoring agent. Note a separate, unrelated use of the word: an
  orchestration tool an outside author used in a model Luís shared
  ([[2026-08-14 1-1 Matheus - Gabrielle]]) — don't conflate them.
