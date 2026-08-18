---
type: synthesis
status: active
updated: 2026-08-18
date: 2026-08-18
aliases: [first agent comparison, candidate comparison, first agent doc, first agent trade-offs]
tags: [agents, planning, decision, a1, a2, a5, a7, a4]
---

# Comparing the first-agent candidates

**A decision doc for [[Agent Flow]]'s deliverable: "one agent actually built", plus
a proposal for which one and where its first version lives.**

> [!info] Relationship to the other pages — read this to avoid drift
> - **The one-pager** for the meeting itself is
>   [[Meeting prep - first agent decision]].
> - **This page** is the *comparison*: motivations, pros, cons, side by side. Written
>   to be handed to someone else.
> - [[Which agent should be built first]] is the *reasoning record* — how the
>   conclusions were reached, including corrections and reversals. It is the
>   authority where the two disagree.
> - [[What should the Agent Flow research phase study]] is the *status board*.
> - [[Fluxo Agêntico project instruction]] is the *spec*, and beats all three.

## The decision as it actually stands

**Nothing is decided.** Two criteria have been applied and they disagree:

| Criterion | Whose | Answer |
|---|---|---|
| **Lowest risk of failing** | The 2026-08-17 analysis | **A5 Watcher** on [[Airtable Proxy]] telemetry |
| **Most utility** | msilva's manager, 2026-08-18 | **A1 + A2**, the intake pair |

**msilva's own position (2026-08-18): A1 + A2.** The criterion change itself has not
been confirmed with Gabrielle, and if it originated with her it is a soft withdrawal
of her own A5 suggestion.

**Deadline pressure is real**: a deadline-setting meeting with Gabrielle and Luís was
set for the end of the week beginning 2026-08-17, and **Gabrielle is on leave from
2026-08-24 for ~2.5 weeks**. Luís works afternoons only. Whatever is chosen should be
chosen before then, or it slips ~3 weeks.

## What "utility" has to mean here, or the comparison is meaningless

Three readings, and they rank the candidates differently:

1. **Value if it works** — favours A4 and A7 (they replace human judgment work).
2. **Expected value** = value × P(works) × frequency of use — favours A1+A2 and A5.
3. **Value of what it teaches us** — favours A1+A2, because it produces the data
   every other agent's spec is currently guessing at.

This doc uses **(2), with (3) as a tiebreak**, and says so rather than hiding the
choice. If Gabrielle means (1), the answer changes to A7 or A4.

---

## Candidate 1 — A1 + A2, the intake pair

**msilva's position.** Capture from every channel, normalize, classify on
type/scope/complexity/risk, route. **Humans act as the three branches** in v1.

### Motivation

The whole architecture is a funnel, and today there is no funnel — demands arrive by
DM, meeting, chat, or someone walking over. A1+A2 makes intake *legible* before
anything is automated downstream.

### Pros

- **It is the only candidate that produces a working system with no other agent
  present.** Normalize → classify → route to a human is complete on its own.
- **It measures demand shape** — what arrives, in what mix, at what complexity and
  risk, at what volume. Every one of the other twelve specs currently guesses at
  this, *including the project-start rate that would decide whether A7 is worth
  building at all*. **msilva has no metric for it (2026-08-18).** This build creates
  the metric.
- **It attacks the documented cause of the last failure.** The prior custom GPT died
  of **channel fragmentation** — it listened on one surface and only when
  @-mentioned. A1's multi-channel capture is the designed answer.
- **Partial infrastructure already exists**: `Slack form → Airtable base → team
  board`, requests in the *projetos* channel ([[2026-08-14 Recap da Semana]]).
- **The business has independently asked for this shape.** In the 2026-08-17 weekly,
  the matriz/external-events problem produced the proposal *"a gente pode até criar
  um agente para tá ali no meio, ver o que que ele mandou pra gente, a gente já
  interpretar ali e validar ou não e jogar na matriz"* — interpret, validate, write.
  **That is A1 + A2 in miniature, arriving from a real problem rather than from the
  architecture diagram** ([[2026-08-17 Weekly - Projetos e Tarefas]]). It is the only
  candidate with demand pull behind it.
- **It carries the document's only performance target** (`< 10 s`), so success is
  measurable rather than argued.

### Cons

- **It is a deliberate departure from *anarchic-first*.** The spec is explicit —
  *"sem dependência cruzada"*, limbs before spine. Starting at the gateway is
  integrated-first thinking. **This must be raised with Gabrielle as a disagreement,
  not arrived at sideways.**
- **Invocation risk survives.** Fragmentation is addressed; *being called* is not. If
  every channel needs a form, a webhook or a mention, the old failure is rebuilt six
  times over. Passive listening is a materially different system — classifying a
  firehose, with cost, noise and privacy consequences nobody has discussed.
- **A2's output is an interface commitment** — the contract every branch agent will
  eventually consume. Per Packer, a bad spec propagates and more agents don't fix it.
- **Degraded by design in v1**: A1 is spec'd to enrich from **A6** and **A13**,
  neither of which exists.
- **Two agents is more scope than "one agent built"**, the stated deliverable.

### What makes the cons manageable

- **Sequence the channels.** Start with the two that carry *no* adoption risk:
  `Bug (sistema)` (machine-fed) and the existing Slack-form path (real traffic,
  existing habit). Passive listening becomes a measured expansion, not an upfront
  bet.
- **Design A2's contract for a human consumer first** — a triaged queue someone works
  from — and treat the inter-agent format as a later refactor against real examples.

---

## Candidate 2 — A5 Watcher

Monitor continuously; detect incidents and opportunities; feed A3. **The best-specified
agent in the document** and the one Gabrielle verbally suggested on 2026-08-10.

### Motivation

Something in production breaks and nobody notices until a person complains. A5 is
also the only agent that needs **no human initiative at all** — its input is a system
alert.

### Pros

- **Immune to both halves of the adoption problem.** One channel, no fragmentation,
  no invocation, no human in the loop. It produces value whether or not anyone changes
  their behaviour — it *sidesteps* the recorded failure instead of betting against it.
- **Genuinely closed-scope**, which is what *anarchic-first* asks for.
- **Best specified**: defined cadences, two modes, one output.
- **Failure is cheap** — a wrong A5 is still a monitoring tool.
- **Design work is already done**: event-driven via Grafana webhook (not polling),
  hybrid with a daily analytical pass doubling as a dead-man's switch, receiver on
  Cloud Run, monorepo layout, alert rules as code, explicit limits. See
  [[How to implement A5 Watcher]] and
  [[2026-08-17 A5 receiver runs on Cloud Run]].
- **Its reach just grew a lot.** Orca and other services are to be plugged into the
  [[Airtable Proxy]] (msilva, 2026-08-18), so **one A5 eventually covers every service
  behind the proxy** — including a business-critical ML system whose automation is
  valued at ~10 headcount ([[2026-08-14 Papo de Projetos]]). This retires the
  "Orca or LiveScript?" targeting question.

### Cons

- **Its utility date is unknown, and that is the decisive problem.** Utility arrives
  with traffic: GC-5 must land for [[LiveScript]], then Orca must migrate. **msilva
  does not know whether the Orca migration is sequenced or merely intended
  (2026-08-18)** — so **no date can be put on this.**
- **Orca is not observable yet, which is worse than "unscheduled".** As of the
  2026-08-17 weekly **nothing is deployed** — the developer is still on documentation
  and the wordmap, with deliveries planned for 2026-08-21, 24 and 28 and the project
  targeted to close 2026-08-28 ([[2026-08-17 Weekly - Projetos e Tarefas]]).

  > [!warning] Unresolved contradiction about Orca — do not paper over it
  > [[2026-08-14 Papo de Projetos]] records Orca as **in production and
  > indispensable**, its automation worth ~10 headcount. The 2026-08-17 weekly says
  > **nothing is deployed.** These may describe different scopes — a live system plus
  > a current build phase — but nobody has said so. **Resolve this before using Orca
  > as A5's value story**, because the whole argument rests on it.
- **Least agent-shaped of the fourteen.** Grafana already does scheduled threshold
  checks. Built naively, A5 proves an LLM can do what infrastructure already does,
  more expensively.
- **Only half its spec maps.** Incident detection fits; **opportunity detection does
  not** — the proxy sees *technical* inefficiency, while the instruction means
  product/process opportunity (*"features sem uso, processos manuais frequentes"*).
- **It teaches least.** Agent-to-agent communication, spec quality and orchestration
  — the hard problems — are all untouched.
- **Cost inverts**: event-driven spend is proportional to incident volume, so A5 costs
  most exactly when the system is degraded and someone is firefighting.
- **It inherits A13's job without A13.** Filing one issue per alert for a recurring
  condition is duplicate-generation by construction.
- **Alert fatigue has higher stakes than last time** — it files into **Linear**, the
  board the team plans real work on, mid-migration while that board's credibility is
  still being established.
- **Thresholds cannot be tuned** without real traffic. The first version is
  mis-tuned by construction; *the tuning loop is the project*.

---

## Candidate 3 — A7 Discovery

Autonomous discovery producing a full PRD: user stories, technical requirements,
proposed architecture, effort estimate, risk analysis, MVP-vs-complete options.

### Motivation

**Spec quality bounds agent quality** — *"colocar mais agentes não corrige uma
especificação ruim"* ([[Gabriel Packer - DAG-driven agent orchestration]]). On that
evidence A7, not A9, is the highest-leverage agent in the architecture. It also matches
msilva's own framing to the team: *"desde os PRDs […] até a entrega."*

### Pros

- **Highest ceiling of any candidate** — it bounds the quality of everything
  downstream.
- **Its consumer is a human**, so it needs no A8 or A9 to be useful.
- **It is evaluable.** A **PRD corpus** exists ([[Proxy Environments]]), so it can be
  backtested against PRDs whose projects already shipped.
- **Invocation is not a failure mode here** — a human who wants a PRD arrives
  motivated. That is exactly what the prior attempt lacked.
- **A worked target spec already exists**: Packer's ticket template — scope and
  explicit out-of-scope, acceptance criteria, test scenarios, affected files,
  `blockedBy` edges, rollout and kill switch, success metrics.

### Cons

- **Its utility cannot be evaluated at all right now.** Value = value-per-PRD ×
  project-start rate, and **msilva has no metric for the rate (2026-08-18).** If the
  area starts one project a month, a perfect A7 fires twelve times a year.
- **It cannot be chat-only** (msilva, 2026-08-18) — and this is the big one. A PRD
  built from whatever the requester remembers to say in one sitting is the
  spec-quality problem moved up a level, not solved. It needs the PRD corpus,
  `Brain`, Linear, the documentation Hub, the five systems' repos and meeting
  transcripts.
- **Therefore it pulls A6 forward.** Every one of those sources is A6 Curator's job,
  and A6 is phase 2 by design. A7-done-properly is A7 **plus a retrieval layer** —
  the heaviest first build of any candidate. See [[Agents read primary sources]].
- **Quality is hard to judge and slow to learn from.** A bad PRD looks fine until the
  project built from it fails, months later.

---

## Candidate 4 — A4 Teacher

Teach areas to build for themselves: diagnose maturity L0–L3, generate personalized
tutorials, track execution, record progression.

**Motivation**: enablement is one of the three fronts and it is *pure human toil* —
enablers sit with people one at a time. It is also the front with no other automation
proposed.

**Pros**: highest human-time saving per success; the front nothing else covers;
msilva has already done one enablement consultation
([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]) so there is
first-hand material.

**Cons**: **the vaguest spec of the four** (what *is* an L0–L3 diagnosis?); maximally
adoption-exposed — it requires someone to choose to be taught; quality is
subjective and slow to measure; and the **Claude Code training programme already
being built for all areas** may overlap or duplicate it outright.

---

## Also considered, briefly

| Agent | Why not first |
|---|---|
| **A3 Executor** | Needs demands to arrive. With no A1/A2 there is no inflow, and it is spec'd to receive A5 feedback that does not exist |
| **A13 Deduplication** | Nothing in the flow to deduplicate yet. **But**: A5's required dedup logic and A13's purpose are the same thing, so A13 is a strong *second* build, or logic living inside A5. Open with Gabrielle |
| **A14 PM Agent** | Follows a request through to delivery — presupposes the pipeline exists |
| **A10 / A11 / A12** | Transversal by design; A10 *"sugere, nunca executa"*. A11 needs usage telemetry that arrives with GC-5 |
| **A6 Curator** | Phase 2 by design, and the **`Brain` repository already does its job** — read that before proposing anything here |

---

## Side by side

Qualitative on purpose; two cells are honestly unmeasurable today.

| | A1 + A2 | A5 | A7 | A4 |
|---|---|---|---|---|
| **Value if it works** | Medium-high | Medium-high | **Highest** | High |
| **P(works)** | Medium-high | **High** | Medium | Low-medium |
| **Frequency of use** | **High** | High | **Unmeasurable** | Medium |
| **Usable with no other agent** | **Yes** | **Yes** | Yes | Yes |
| **Adoption / invocation risk** | **High** | **None** | Low | **Highest** |
| **Spec quality today** | Good | **Best** | Good | Weakest |
| **Fits *anarchic-first*** | **No** | **Yes** | Partly | Yes |
| **Blocked on anything external** | No | **Yes — GC-5, then Orca; no date** | No (needs retrieval built) | No |
| **What it teaches** | Demand shape; the A2 contract | Least | Spec discipline; retrieval | Enablement patterns |
| **Time to first real value** | Weeks | **Unknown** | Weeks-to-months | Months |

---

## Facts that apply whichever is chosen

- **Context retrieval is not a fifteenth agent.** Build a shared `tools/` layer in
  the monorepo — one tool per source. A retrieval *agent* would create the cross-agent
  dependency phase 1 forbids, for A1, A5 and A7 at once. Precedent: Packer's system is
  four skills, `orca-linear` among them. See [[Agents read primary sources]].
- **Read `Brain` first.** It reportedly already does A6's job and **nobody has looked
  at it.** It may already be the retrieval layer, and building a parallel one is
  exactly what A13 exists to block.
- **Token cost is a design constraint, but the scary number was a bad case.** ~$7 in a
  day of testing was traced to a probable **runaway execution**, not context volume —
  a measured normal run was **11 centavos**. A **token-consumption dashboard exists**
  and is obtainable on request. The levers are **narrow fetching** (per-agent design)
  and **a correct trigger chain** (orchestration).
- **Two in-house agent precedents now exist**, both from 2026-08-17 and both better
  evidence than any design document: the **AI status readout** on Linear (four real
  insights, two real failure modes) and **Luís's unattended ultracode run** on Farol,
  which took Uber, OnFly and Expresso close to integrated in one 4–5 hour pass. The
  second is a working demonstration of the AI-First premise by the tech lead — and
  also exactly the shape of the token-cost worry. See
  [[2026-08-17 Weekly - Projetos e Tarefas]].
- **Packaging is both the cost lever and the sharing mechanism** —
  [[Packaging as skills]]. In the one natural experiment available, the component
  packaged as a skill works and the unpackaged one burns context.
- **Humans stay at the gates.** AI-First puts humans as *strategic approvers*, and
  Packer keeps a human quality gate at merge. **A12 and A13 must be deterministic
  because they block.** What remains open is *which* steps are gated.
- **Where it lives**: today a claude.ai Project; a **monorepo** is decided for the
  build, and the A5 receiver would run on **Cloud Run**, `min-instances=0`.
- **Don't duplicate what exists.** A **shared AI architecture for Livemode** is a
  stated long-term goal, a **Claude Code training programme** is being built for all
  areas, and **TES** (an AI-orchestration vendor) is under trial. None were mentioned
  at handover.
- **`Brain`, the homologation flow, and A9's stack constraint are all unresolved**:
  the homologation flow validates architecture, tooling, security and governance
  *before* implementation, and may gate this work. A9's controlled stack omits **Go**.

## Recommendation

**A1 + A2**, framed as *"instrument the intake first"* rather than *"build the gateway
first"* — the deliverable being working triage **plus** the demand dataset every other
spec needs.

The reasoning is comparative, not absolute: **A5's utility has no date** (blocked on
GC-5 then an unscheduled Orca migration) and **A7's utility cannot be measured**
(no project-start rate) — and A1+A2 is the build that produces that missing number.
Under the expected-value reading of utility it wins; under "value if it works" A7
wins instead.

**Sequence**: A1+A2 on machine and existing-traffic channels → **A5** once GC-5 has
landed (dedup logic doubling as the seed of A13) → reassess A7 with a real
project-start rate in hand.

## Decide these before 2026-08-24

1. **Which criterion** — utility or lowest-risk? And which reading of utility?
2. **Does Gabrielle accept A1+A2**, knowing it departs from *anarchic-first*?
3. **Is Orca's proxy migration scheduled?** Decides whether A5 can be dated at all.
4. **How often does the area start a new project?** Decides whether A7 is worth building.
5. **Does she accept the A6 split** — retrieval as tools now, curation as an agent in
   phase 2?
6. **Is A13 a second agent, or logic inside A5?**
7. **Does agent output filing into Linear have her support**, mid-migration?
8. **Does the homologation flow gate this work?**
