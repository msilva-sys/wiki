---
type: project
status: active
phase: research
updated: 2026-08-18
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

> [!note] Resolved in part, 2026-08-18 (msilva)
> **[[Orca (CDE)]] and other services are to be plugged into the
> [[Airtable Proxy]]**, so the proxy becomes the shared Airtable data plane for
> the area rather than LiveScript-specific infrastructure. That makes proxy
> telemetry the credible `Bug (sistema)` producer for *several* business-critical
> systems at once, including Orca — and it retires the "Orca or LiveScript?"
> targeting question. Timing is still open — Orca's migration isn't mentioned in
> its own roadmap ([[Orca Next Version]]) — see [[Which agent should be built first]].

> [!danger] Contradicted the same day by Luís — 2026-08-18, unresolved
> Hours after the note above was written, [[2026-08-18 1-1 Matheus - Luís]] has Luís
> arguing the opposite: **decouple the Watcher/A5 from the proxy entirely.**
> *"eu desassociaria ele completamente do proxy [...] imagina que ele não funciona
> com o proxy, não existe, ele ainda é útil."* His reasoning — the proxy will only
> serve LiveScript for a long while, so an agent scoped to it generates little
> value early on: *"ele vai ficar vigiando um proxy que só [...] script usa no
> começo. Pouco valor."*
>
> Neither speaker referenced the other's framing; recorded as a genuine,
> unreconciled tension per the schema, not resolved by picking one. It means the
> proxy-reach argument above — and the detailed A5 design in
> [[Which agent should be built first]] / [[How to implement A5 Watcher]] — may be
> optimizing for a target Luís wants set aside. Next step, per the same
> conversation: msilva runs separate discovery conversations with Gabrielle and
> Carol about what each actually imagines the Watcher doing, before any more design.

A longer-term ambition, from the onboarding: once built for this area, the flow
could be adapted and reused by other areas, helping them produce more structured
projects without routing through the projects team.

## Prior attempt — failed on adoption, not capability

A product agent was built once before, as a **custom GPT** scoped to a single
project and dropped into that project's group chat. Worth reading before
designing the next one:

- **It worked technically.** It investigated reported problems, checked whether
  something had been recently implemented or was sitting in the backlog, and
  answered. *"O funcionamento dele foi OK."*
- **It was never called.** Users had to **@mention it every time**, and they
  didn't — the bot sat on one channel, waiting to be addressed.

  > [!important] Corrected 2026-08-17 by msilva — this is channel fragmentation
  > The failure was previously recorded here as *unwillingness to file*, drawing on
  > Gabrielle's *"elas querem falar que tem um problema"*. That reading is too
  > narrow. **People report a problem through whatever path is at hand** — a DM, a
  > different channel, a meeting, walking over to someone. The bot listened on one
  > surface and only when explicitly invoked, so most reports never reached it.
  >
  > This matters because it changes the fix. Reducing friction on one channel does
  > nothing; **being present on the channels people already use** is the answer —
  > which is what **A1 Receptor Universal** is for.
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

> [!warning] $7/day is a bad case, not the norm — but the comparison is not clean
> Measured consumption for one observed run was **11 centavos**, against ~$7 for the
> earlier period. ~~Same flow, same day. **Cost is intermittent, not inherent**~~ —
> **struck 2026-08-18: Gabriel switched LLM provider (Anthropic → GPT) between the two
> runs**, so the delta cannot be attributed to run-to-run variance. Full retraction on
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]].
>
> **What still stands:** $7 happened, 11 centavos happened, and $7 is not explicable by
> context volume alone for this workload. The **runaway-execution** hypothesis (two
> workflows firing concurrently when one should chain into the next) also still stands —
> msilva and Gabrielle reached it independently — but it now rests on two people's
> judgement rather than on a measurement. Wholesale context loading remains worth
> fixing. **Which of the two dominates is unknown**, and the execution logs are what
> would say.
>
> A **token-consumption dashboard exists** and is obtainable on request — the
> measurement instrument this constraint always needed.

> [!note] The instrument has two halves, and both had to be switched on — 2026-08-18
> From [[2026-08-18 1-1 Matheus - Gabrielle]]:
>
> - **Execution history was off by default.** The team decided to save both failed
>   *and* production runs — [[2026-08-18 Save n8n execution logs for audit]]. The
>   generalizable point for this project: on the platform the agents would actually run
>   on, **observability is opt-in, and the default is off.** The [[Airtable Proxy]] is
>   observability-first by design; its runtime is not.
> - **Company-wide visibility exists but is coarse.** The **"Tech" account can see
>   every flow in the company**, though per-user filtering is limited enough that
>   isolating one person's runs means manually scanning update history. A second
>   account, **"Humans"**, was described as having a larger token allowance and an
>   upgrade path. *(unverified: whether these are n8n accounts or the Claude org
>   account — the notes conflate them. The open question on the 1:1 page decides where
>   the token instrument actually lives, and it should be asked before 2026-08-24.)*
>
> Consequence for **A5 Watcher**, whose whole cost story is throttling
> ([[How to implement A5 Watcher]]): **you cannot throttle what you cannot see**, and
> the seeing was a setting nobody had turned on.

> [!danger] What the retraction means for this project — 2026-08-18
> Mechanics in the struck callout above. The design consequence: **the token constraint
> is real, but the wiki does not know whether wholesale context or runaway execution
> dominates.** Do not design around either answer until the execution logs produce one
> ([[2026-08-18 Save n8n execution logs for audit]]).

> [!note] Leadership has authorized higher spend during stabilization — 2026-08-18
> Gabrielle's guidance to Gabriel: *"talvez é só alinhar que durante esse período de
> teste você vai gastar um pouco mais e que, tipo, a gente tá OK com isso, sua liderança
> tá OK com isso, até você estabilizar realmente o fluxo."*
>
> She also corrected the budget mechanism he was worried about: there is no hard ~$20
> ceiling. IT tops up a **company-wide workspace weekly** and each person consumes
> against their own key — *"não é necessariamente um budget […] que tem que ser recarga
> pelo cartão."*
>
> **This softens the constraint at the top of this section.** The 1:1 framing was that
> daily spend at experiment scale *"is not obviously sustainable"*. The manager's actual
> position is that experimentation cost is expected and accepted **until a flow
> stabilizes**. Optimizing tokens remains an explicit goal; it is not a gate on
> building. Aligning expectations up front is the recommended move — which is also
> Luís's answer on Ultra Code, from the other end.

The cost lever is therefore twofold: **narrow fetching** (a design decision per
agent) and **a correct trigger chain** (an orchestration problem). For A5 Watcher
at a 5-minute cadence, the second matters more — a runaway loop on a schedule
compounds in a way a one-off experiment doesn't.

> [!note] A third data point, 2026-08-17
> Luís's Ultra Code run on [[Farol]] was **deliberately** 4–5 hours of unattended
> parallel agent work, and he reported the outcome as a clear win with no mention of
> cost. So the team's live position is not "minimize tokens" — it is that a long
> expensive run is fine **when the output justifies it**, and the open question he
> posed is precisely *when that is*: *"em que momento que a gente usa isso e em que
> momento que a gente trabalha do jeito que a gente vem trabalhando."* That is the
> same question as this constraint, asked from the other end. Worth resolving with
> him rather than optimizing in isolation.

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
> - **Isolation.** A shared n8n licence means a stuck lock blocks everyone, and the
>   execution list is searchable by name only and sorted by recency — so one person's
>   runs get pushed out of view. *(Narrowed 2026-08-18: the flow was untraceable mainly
>   because **successful production executions weren't being saved at all** — a setting,
>   not the shared tenancy. See [[2026-08-18 Save n8n execution logs for audit]].)*
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

**Deliverables 1 and 3 are drafted**: [[Comparing the first-agent candidates]]
carries the motivations, pros and cons for each candidate and a recommendation
(A1 + A2), plus the eight things needing a decision before 2026-08-24.

Gabrielle's suggestion: start with the **monitoring/watch agent**, scoped to a
single project rather than the whole portfolio — [[LiveScript]] or
[[Orca (CDE)]] were named, as the systems under most constant use. Scoping it
to one project is how the agent picks up real context and gets working in
practice.

> [!note] A5's shape, settled 2026-08-17
> **A5 does not poll.** Grafana alert rules fire webhooks; A5 receives them. The
> 5-minute cadence belongs to Grafana. **Hybrid**: event-driven for incidents,
> a daily analytical pass for opportunities — which also serves as a dead-man's
> switch, since event-driven monitoring otherwise fails silently. A5 gets direct
> access to Prometheus, Loki and the observability stack, not just alert payloads.
> **The proxy is not modified.** Full design and the risks it introduces:
> [[Which agent should be built first]].

> [!tip] Orca is the stronger candidate
> [[2026-08-14 Papo de Projetos]] describes Orca as a **machine-learning system,
> in production, that the team using it cannot work without** — its automation is
> valued at roughly ten headcount, and *"isso não pode estar fora do ar."* A
> business-critical system that must not go down makes the value of a monitoring
> agent self-evident, which is exactly what the prior attempt failed to achieve.
> [[LiveScript]] is the system msilva knows better; [[Orca (CDE)]] is the one
> where an alert would obviously matter. Full profile, including why "in
> production" and "nothing deployed" (2026-08-17) both hold at once:
> [[Orca (CDE)]].

## Prior art — a working implementation of the projects branch

[[Gabriel Packer - DAG-driven agent orchestration]] (2026-07-23) is the model Luís
pointed at, and it runs A7 → A8 → A9 in production for one author: an orchestrator
that writes no code builds a dependency graph from Linear tickets, releases work
in **waves**, starts one implementation agent per ticket, monitors PRs and CI, and
opens the next wave as blockers merge. A **human quality gate at merge** — which
matches the AI-First stance of humans as strategic approvers.

Its predecessor, [[Gabriel Packer - solo founder AI workflow (part 1)]]
(2026-03-26), shows the same system before that automation — every ticket
dispatched by hand. **The delta between the two posts is the most useful thing
here: he automated the coordination and left the judgement alone.** The quality
gates, spec discipline, and human merge review are identical in both. That is a
migration path, not just an end state, and it says which piece to automate first.

Three things to take from them before designing anything:

- **The ticket is the prompt.** His ticket template — scope and explicit
  out-of-scope, acceptance criteria, test scenarios, affected files, `blockedBy`
  edges, rollout and kill switch, success metrics — is effectively a worked
  specification for what **A7 Discovery** must generate.
- **Spec quality bounds agent quality**: *"colocar mais agentes não corrige uma
  especificação ruim."* On this evidence A7 is the highest-leverage agent in the
  architecture, not A9.
- **Agent instructions belong in files, not memory.** From a practitioner replying
  to part 1: *"memory compacts and agents forget context mid-task. files persist
  across sessions and every agent reads the same source of truth."* A direct
  design input for **A6 Curator** — durable shared files rather than per-agent
  context.

Also worth noting from part 1: **QA is a first-class stage there** (a `qa-tester`
agent driving Chrome via DevTools) and **the loop closes on observability**
(AppSignal for traces, PostHog for adoption, adoption feeding the next round).
Nothing among the 14 covers automated QA — possibly a gap, possibly deliberate.

## Other prior art

Livemode already has more in this space than the architecture diagram suggests:

- **[[AI status reporting on Linear]] — an agent already running, weekly.** The
  strongest item on this list, added 2026-08-18. An AI reads the area's Linear board
  and produces the status readout the team walks at the top of
  [[2026-08-17 Weekly - Projetos e Tarefas]]. **Corrected 2026-08-18: not the only one** —
  Claude already authors the area's Linear backlogs (see above), and that one works. This
  remains the most *instructive* case, because it failed in public: four real insights
  and two real failures in one sitting. See that page for what each implies here.
- **Ultra Code — the tech lead runs unattended multi-hour agent workflows.** Luís
  tested Claude Code's ultracode on [[Farol]]: it plans a workflow, parallelizes, and
  runs *"4, 5 horas"* unattended. One run took Uber, OnFly and Expresso to
  near-integrated, with the leftovers being genuine inconsistencies in the vendors'
  APIs rather than agent errors. He wants to present it and agree when it's the right
  mode. This is the AI-First premise demonstrated inside the team by the person who
  would validate the architecture — and a 4–5 hour unattended run is the sharp end of
  the **token-cost constraint** below.
- **A shared AI architecture for Livemode**, named as a long-term goal by the
  area lead, with no detail given.
- **A Claude Code training programme** being built for all areas.
- **TES**, an AI-orchestration vendor under trial
  ([[2026-08-14 Recap da Semana]]). Trial is live as of 2026-08-17: presented the
  prior week, a WhatsApp group formed with **Bia, Arthur and Kauan**, first feedback
  already relayed, formal feedback to the vendor targeted for the week of 2026-08-24.
  **msilva is not in that group** — worth fixing, since TES is the item on this list
  most likely to overlap or pre-empt his project.

None of these were mentioned when the project was handed over. Understanding how
Agent Flow relates to them — complement, replacement, or duplicate — is worth
doing before designing, particularly given that one of the transversal agents is
explicitly about **not building the same thing twice**.

## Claude already authors this area's Linear backlogs (2026-08-18)

The single most consequential fact from [[2026-08-18 1-1 Matheus - Gabrielle]], and it
arrived as an aside describing routine practice:

> *"eu primeiro pego […] alguma documentação ali do projeto em si, mas de produto,
> PRDs, etc. E aí eu chego no [Claude] com ele aqui. E aí depois que eu faço isso, eu
> peço para ele jogar tudo lá pro [Linear]. E aí ele […] cria tanto a descrição do
> projeto […] ele próprio já cria aqui os milestones e aí […] dentro dos milestones vai
> subir [issues]. Enfim, ele cria as tarefas sozinho."*

Documentation and PRDs in; project description, milestones and issues written into
Linear, using Luís's templates, unattended. Plus: Luís authors issue context through
Claude and leaves review comments for Gabrielle from inside it, and Linear itself has a
**native description-improving agent** (*"o agentezinho[que] trata a descrição para dar
uma melhorada"*).

> [!important] This corrects the wiki's central claim about local evidence
> [[AI status reporting on Linear]] was filed as *the only agent-like system actually
> running in the area*. **It is not.** Backlog authoring is agent-driven, in daily use
> by the manager, and — unlike the status readout — uncontroversial.
>
> The correction cuts both ways. The area has **more** agent adoption than the wiki
> credited, which strengthens the *"adopts this kind of thing"* read. But it also means
> the one system this project had been reasoning from was the **atypical** case: the one
> that failed in public. A working example was sitting in plain sight and nobody
> described it, because it is just how Gabrielle makes tickets.

**What it means for A2.** A2 Classificador's job includes producing well-formed tickets,
and the wiki has treated its output shape as an open design question resting on Packer's
external template. **An agent writing structured Linear issues from context is already
solved here.** So A2's hard part is not authoring — it is the *classification and
routing* upstream of it, and the intake breadth of A1. That narrows the first build
usefully; see [[Which agent should be built first]].

**And it is directly operational today.** msilva must migrate the proxy backlog, has the
design docs, and now has the established method for doing it. See [[Airtable Proxy]].

## Infrastructure that already exists in Linear (2026-08-18)

From [[2026-08-18 1-1 Matheus - Gabrielle]], written up at
[[Linear Project Structure]]. Three pieces of this architecture may not need
building, and one design input finally has a local instance.

**A delivery channel exists.** Linear projects can be wired to **specific Slack
channels**, pushing progress and task movements automatically. Available, not
configured. So an agent that writes a Linear issue gets Slack delivery for free —
relevant to **A2** (whose output is a ticket) and to
[[How to implement A5 Watcher]], where notification is otherwise treated as
something to build. **Reporting is not the hard part; deciding what is worth
reporting is.**

**An approval gate exists, with a return path.** Work is validated by someone from
product, who tests it and can send it back through Linear's comment system —
[[2026-08-18 Product feedback in Linear, code review in Git]]. The architecture's
*humans as strategic approvers* premise
([[Fluxo Agêntico project instruction]]) therefore has a concrete place to live, and
a split: **product/business-rule approval in Linear, technical in Git.** An agent
submitting work has to know which gate it is aiming at — different reviewers,
different rejection criteria.

**An access-control boundary exists.** Freelancer projects are isolated in separate
**Linear teams** — *"para ele não ter acesso a todos os nossos outros projetos."* Teams
are the workspace/permission unit, and it is what would scope any agent given Linear
credentials. Note that **initiatives cut across teams** (*"a iniciativa ela olha pro todo
projeto"*), so an initiative-level view is not constrained by the team boundary — worth
knowing before assuming team isolation contains anything reading at initiative level.

**A4 Teacher's operating pattern has been stated by the person who owns the role.**
Gabrielle on the Gabriel consultation: *"A ideia é[:] tu ajudou ele destravar e ele vai
andar sozinho. […] se ele travar em algum momento, ele procura a gente de novo e a gente
ajuda[,] ajuda pontual."* Unblock → step back → help on demand. Not continuous teaching,
and not ownership transfer. That is a tighter spec for A4 than the instruction's
*"diagnose maturity, generate tutorials, follow execution in real time"*, and it is
cheaper: the success criterion is **the person proceeding without you**.

**The ticket template is no longer only external prior art.** This project's design
has leaned on Packer's ticket template as the target output shape for A2
([[Gabriel Packer - DAG-driven agent orchestration]] — *the ticket is the prompt*).
**Luís has authored in-house Markdown templates for bugs, spikes and epics**, plus a
structural one covering how to divide work across milestones, epics, issues and
subissues. Gabrielle has her own and rates it poorly (*"o meu eu não achei que tá tão
bom"*).

But: **Luís is already rewriting his, because he dislikes how they turned out** —
*"ele tava ajustando um pouco porque ele não tava gostando muito dos templates de como
tavam."* So "read the in-house templates" is only half the move. **The valuable artifact
is Luís's diagnosis of what's wrong with them**, and Gabrielle explicitly assigned
asking him: *"Vai também depois perguntar a ele o que que ele achou que tá ficando
ruim."* A template author's account of why his own template fails in use is better input
to A2's output contract than the template itself.

> [!success] Agent Flow gets its own initiative — Gabrielle, 2026-08-18
> *"talvez quando você for fazer o de agente do fluxo agêntico, talvez você iria criar
> uma iniciativa e aí talvez cada parte ali seria um projeto, enfim, acho que talvez
> depois você entender melhor como ele dividir."*
>
> Hedged three times over with *talvez*, and she defers the division to msilva — but it
> is her answer: **its own initiative, each part a project.** That maps cleanly onto the
> build strategy. *Anarchic first* means each agent is independently buildable and
> independently in production, which is exactly what a project is here — a segment of
> value delivery that stands alone. **One project per agent**, with the four-part spec
> (inputs/outputs · consults/feeds · success criteria · limits) as the project
> description.
>
> Practical: creating the initiative makes the research **visible to the weekly
> readout**, which is otherwise the reason work on this track looks like nothing is
> happening while msilva is heads-down on the proxy.

## A use case that arrived from the business (2026-08-17)

Recorded because it is the **first time anyone at Livemode proposed building an
agent for a problem they actually had**, rather than as part of this architecture.
Full detail in [[2026-08-17 Weekly - Projetos e Tarefas]]; the shape:

Osmar built a Vercel app centralizing *reportagem*'s requests to create **external
events**. The second leg has to create them in the **matriz**, which is sensitive
data nobody wants to grant him write access to. The proposal, unprompted: *"a gente
pode até criar um agente para tá ali no meio, ver o que que ele mandou pra gente, a
gente já interpretar ali e validar ou não e jogar na matriz."* Alongside it, a
second requirement — the flow should persist in an Airtable base rather than only in
Vercel, *"porque senão você perde a história e você não sabe quem é [da] automação"*
— i.e. log who requested and who cancelled.

**This is A1 + A2 in miniature**: receive an unstructured request, interpret it,
classify it as valid or not, route it to an action, and keep an audit trail. It
carries three properties the paper candidates in
[[Comparing the first-agent candidates]] don't:

- **A named requester and a real deadline pressure**, from outside the team.
- **A concrete definition of success** — Osmar stops asking, Nina and Diego stop
  hand-creating, nothing malformed reaches the matriz.
- **A write path into Airtable**, which puts it in the same territory as the
  [[Airtable Proxy]] and the tables pinned in [[Proxy Environments]].

It also carries an unresolved blocker that is not technical: **nobody knows who is
supposed to approve these** — *"mas quem deveria [a]provar isso? Porque no final é
uma gravação."* An approval agent with no defined approver is not buildable, which
makes this a question to answer before treating it as a candidate.

Not currently on the candidate list, and **not proposed as a replacement for
A1 + A2** — but it may be the concrete first instance of them, which is a different
and better thing than a synthetic pilot. Worth raising with Gabrielle before
2026-08-24.

## Design approach

msilva's own framing of the fork, from [[2026-08-14 1-1 Matheus - Gabrielle]],
with what has since been settled marked as such.

- ~~**Deterministic vs. autonomous.**~~ **Settled by the spec.**
  [[Fluxo Agêntico project instruction]] is explicit — AI-First, *"a IA é o meio de
  execução"*, agents execute autonomously and humans approve. The 1:1 framing
  (*"mostrar as ferramentas pros agentes […] para ele resolver as coisas"*)
  predates that document.
  **What remains open is where the gates sit, not whether to be autonomous.** Two
  constraints bound it: **A12 and A13 must be deterministic** because they block,
  and Packer keeps a **human quality gate at merge**
  ([[Gabriel Packer - DAG-driven agent orchestration]]). So the live question is
  *which steps are gated*, not which paradigm wins.
- **Graphs — resolved 2026-08-17.** Luís's *"questão de gráfis"* meant a
  **dependency graph (DAG) driving execution order**, per the model he pointed at:
  [[Gabriel Packer - DAG-driven agent orchestration]]. Not a graph database, not a
  general framework — tickets carry explicit `blockedBy` edges, and parallelism
  comes from the graph rather than from how many agents you start. Concrete and
  already working in production for one author.
- **Orchestration shape** — how the orchestrating agent works, unaddressed.

msilva has done related work before, with people at his previous employer, so
this isn't a standing start.

Gabrielle's framing of ownership: *"é teu filho e teu projeto"*, with Luís as
consultant rather than validator.

## Open questions

- **What, concretely, does the Watcher/A5 agent do — and does it need the proxy at
  all?** New 2026-08-18, from [[2026-08-18 1-1 Matheus - Luís]]. Luís's read of
  msilva's own description was *"muito genérico"*, and he wants **separate**
  ~30-minute discovery conversations with Gabrielle and Carol — deliberately apart,
  so neither's answer shapes the other's — before more design happens. This also
  carries the unresolved Gabrielle-vs-Luís tension above.
- **Where do the human gates sit?** Autonomy itself is settled; which steps are
  gated is not. See *Design approach* above.
- **Which agent to build first?** **The selection criterion changed on 2026-08-18** —
  msilva's manager wants the agent with the **most utility** first, where the
  2026-08-17 analysis had optimized for lowest risk of failing. Both readings are
  worked through in [[Which agent should be built first]]. Under lowest-risk the
  answer was **A5 Watcher on proxy telemetry**; under utility it is the intake pair,
  **A1 + A2**, which is also **msilva's own position (2026-08-18)**. *Position, not
  decision — nothing is settled, and the criterion itself needs confirming with
  Gabrielle before 2026-08-24.*
- ~~**Who owns context retrieval?**~~ **Answered 2026-08-18 — it is not an agent.**
  Raised from msilva's point that **A7 cannot be chat-only**, then asked whether it
  needed a fifteenth agent. It does not: a retrieval *agent* would create the
  cross-agent dependency phase 1 forbids, duplicate A6, and be the intermediary that
  [[Agents read primary sources]] exists to avoid. Build a shared `tools/` layer in
  the monorepo instead. **Corollary: A6 is two jobs** — retrieval (tools, now) and
  curation (an agent, phase 2) — which is a reinterpretation of the architecture's
  centrepiece and so a spec disagreement to raise with Gabrielle explicitly.
- **How often does the area start a new project?** **msilva has no metric (2026-08-18).**
  So A7 cannot be evaluated on utility at all — which is itself an argument for
  A1 + A2, the build that would produce the number.
- **Does A1 listen passively, or must it still be addressed?** The sharpened
  version of the adoption question. A1's multi-channel capture answers the
  *fragmentation* half of the prior failure — but if every channel still needs a
  form, a webhook, or a mention, the same failure is rebuilt across six surfaces
  instead of one. **Passive listening is a different proposition**: classifying a
  firehose, with cost, noise and privacy consequences nobody has discussed.
- How do agents actually talk to each other? The unsolved problem that killed
  the prior attempt.
- ~~Is **Orca** a distinct system?~~ **Resolved 2026-08-17, confirmed 2026-08-18.**
  Livemode's Orca is a business-critical **machine-learning** system — see
  [[Orca (CDE)]], written after msilva gained access to its planning repo — which
  makes it a credible first target for the monitoring agent. The unrelated
  **Orca** in [[Gabriel Packer - DAG-driven agent orchestration]] is a tool that
  creates git worktrees and runs agent terminals. Same word, no relation.
