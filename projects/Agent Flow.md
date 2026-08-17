---
type: project
status: active
phase: research
updated: 2026-08-17
aliases: [fluxo, fluxo de agentes, agent architecture, the agent project]
tags: [agents, llm, automation, onboarding, research]
---

# Agent Flow

> [!abstract] Currently in the **research phase** (as of 2026-08-17)
> Authoritative spec: [[Fluxo Agêntico project instruction]] (per-agent detail for
> all 14). Architecture: [[Fluxo Agêntico diagram]]. Agenda and open questions:
> [[What should the Agent Flow research phase study]].

## Philosophy and build strategy

From [[Fluxo Agêntico project instruction]] — this is the spec, and it overrides
the verbal descriptions the rest of this page was originally built from.

**AI-First.** *"A IA é o meio de execução, não apenas um assistente."* Autonomous
agents write code, build automations, deploy, monitor, and teach. **Humans are
strategic approvers and quality refiners**, not operators. This settles the
deterministic-versus-autonomous question below in favour of autonomy with human
approval gates.

**Anarchic first, integrated second.** *"A estratégia de construção é anárquica
primeiro, integrada depois."*

- **Phase 1** — each agent built independently, closed scope, validated in
  isolation, **running in production individually even without integration**.
  Explicit goal: no cross-dependency.
- **Phase 2** — wire them per the architecture; A6 begins capturing from
  everything; the transversal intelligences start running in parallel.

**This means an agent with no consumers is buildable by design.** Any concern that
A5 needs A3, or that A6 must come first, is answered by the strategy rather than
by sequencing.

**Working method**: one conversation per agent or per connection. Each agent is
specified as **inputs/outputs · what it consults and feeds · success criteria ·
limits (what it does not do)**. That four-part frame is the shape the
"understanding of each agent" deliverable should take.

A proposed multi-agent architecture to automate how msilva's area receives,
classifies, and executes demands. msilva's **second onboarding track**, running
in parallel with the [[Airtable Proxy]] but with no delivery deadline —
[[2026-08-10 Onboarding runs proxy and agent flow in parallel]].

> [!info] Status: design only, but the design is now in hand
> Described as *"um desenho inicial"* — an initial drawing. Validated
> conceptually, but **no decision on where it would live or how it would be
> built**. The diagram itself was received on 2026-08-17 and is transcribed at
> [[Fluxo Agêntico diagram]]; it is a **revision**, with agents renumbered at
> least once. The accompanying design documentation and recorded design
> conversations have still not been received.

## The three fronts it automates

The architecture mirrors how the area actually works today:

1. **Project creation** — new projects, discovery through delivery.
2. **Enablers** (*habilitadores*) — a teaching role; sitting with people to
   unblock them and help them level up.
3. **Quick execution** — pointed demands on things already built: production
   breakage, small fixes, a newly requested feature.

## Proposed shape — 14 agents

Rewritten 2026-08-17 from the design diagram itself
([[Fluxo Agêntico diagram]]), which is more specific than the verbal description
this section previously relied on. Full agent table on that page.

**Four entry channels** — `Bug (sistema)` · `Bug (manual)` · `Tarefa` ·
`Consultoria` — converge on **A1 Receptor Universal** (filters, formats,
classifies), which hands off to **A2 Classificador & Decisor** for routing:

| Route | Pipeline |
|---|---|
| **Projetos** | A7 Discovery (**generates a complete PRD**) → A8 Orchestrator → A9 Developer, which creates sub-agents on demand |
| **Enablement** | A4 Teacher — teaches areas. A single agent, not a pipeline |
| **Operacional** | A3 Executor — orchestrates fast demands, also creates sub-agents on demand |

**Five transversal intelligences**: A10 Portfolio (prioritization), A11 Product
(usage analysis), A12 Data Gov (data quality), A13 Deduplication (**detects and
blocks**), A14 PM Agent (request through to delivery).

> [!important] Three corrections to what this page previously said
> The verbal description in [[2026-08-10 Onboarding Técnico - Matheus]] flattened
> distinctions the diagram makes:
>
> 1. **A6 Curator is the architectural centre, not one transversal agent among
>    six.** It occupies its own layer — `MEMÓRIA & APRENDIZADO` — is drawn
>    emphasized, and every other agent connects to it. This page previously
>    listed institutional memory as a peer of portfolio and product. It isn't.
> 2. **A5 Watcher is not transversal either.** It's a separate concern that
>    **feeds back directly into A3 Executor** (*"retroalimenta execução"*) as
>    well as into the intelligences. Monitoring drives execution here, it doesn't
>    just report.
> 3. **A13 Deduplication blocks.** *"Detecta e bloqueia"* — a gate with authority
>    to stop work, feeding context back to A1. Previously recorded as merely
>    ensuring things aren't built twice.

**`Bug (sistema)` is a machine-generated channel**, distinct from `Bug (manual)`.
Nothing states what emits it — but the [[Airtable Proxy]]'s telemetry and A5
Watcher are the two obvious candidates, which would make it the concrete
integration point between msilva's two projects.

A longer-term ambition, from the onboarding: once built for this area, the flow
could be adapted and reused by other areas, helping them produce more structured
projects without routing through the projects team.

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

That flow was diagnosed on 2026-08-17 —
[[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]].

> [!warning] Corrected 2026-08-17 — $7/day is the bad case, not the norm
> Measured consumption for one observed run was **11 centavos**, against ~670 for
> the earlier period. Same flow, same day. **Cost is intermittent, not inherent** —
> which points at a **runaway execution** (two workflows firing concurrently when
> one should chain into the next) rather than at context volume as the primary
> cause. Wholesale context loading is still worth fixing; it just isn't the main
> culprit.
>
> A **token-consumption dashboard exists** and is obtainable on request — the
> measurement instrument this constraint always needed.

The cost lever is therefore twofold: **narrow fetching** (a design decision per
agent) and **a correct trigger chain** (an orchestration problem). For A5 Watcher
at a 5-minute cadence, the second matters more — a runaway loop on a schedule
compounds in a way a one-off experiment doesn't.

**Sharing is the underlying problem.** Work built in Claude can't easily be
handed to a team — at best a shared workspace, or packaging it as a skill to
distribute. n8n covers scheduled and event-triggered runs that Claude alone
doesn't, which is why colleagues build in one and move to the other. **A
platform where agent flows can live and be maintained collaboratively is the gap
this project exists to fill.**

> [!important] Corrected 2026-08-17 — sharing is narrower than recorded
> Observed working in practice
> ([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]): a
> colleague **had already sent his Claude skill to his team**, the **n8n instance
> is shared team-wide**, and extending delivery to the team is just repointing the
> output channel.
>
> So the gap is **not** "work can't be handed over" — skills plus a shared n8n
> already do that inside a team. The real gaps are narrower and worth restating:
> - **Maintenance**, not distribution. The team has the skill; whether anyone but
>   the author can debug it is untested.
> - **Isolation.** A shared n8n licence means executions crowd each other's logs,
>   and a stuck lock blocks everyone — which is what made the flow untraceable.
> - **Cross-area** sharing, as opposed to within one team, remains unexamined.

> [!tip] This is already researched — three sources converge on packaging
> Linked 2026-08-17 during a lint; these existed but nothing pointed at them.
>
> - [[Sharing the accounting automation with the team]] — distribution options for
>   getting one person's workflow to a whole team, worked through for the
>   accounting case on 2026-08-14.
> - [[Sharing via Projects - the accounting project]] — Projects as a sharing
>   mechanism and what belongs in one. The Fluxo Agêntico project **is** a shared
>   Project ([[Fluxo Agêntico project instruction]]), so this describes the
>   container the work already lives in.
> - [[Claude capabilities map - accounting data scope]] — capabilities by layer,
>   cheapest-to-adopt first.
>
> Field evidence points the same way: in
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] the component
> that **works** is the one packaged as a skill, and the one that fails is
> unpackaged. **Packaging is emerging as both the cost lever and the sharing
> mechanism** — the same answer to two problems, which is worth testing rather
> than assuming.

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
