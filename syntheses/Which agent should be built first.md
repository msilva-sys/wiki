---
type: synthesis
status: active
updated: 2026-08-18
date: 2026-08-17
aliases: [first agent, A5 first, which agent first]
tags: [agents, planning, a5, a1, a7]
---

# Which agent should be built first?

> [!warning] The selection criterion changed on 2026-08-18 — read this first
> This page was written on 2026-08-17 to answer **"which agent is least likely to
> fail?"** and concluded **A5 Watcher on [[Airtable Proxy]] telemetry**.
>
> On 2026-08-18 msilva reported that **his manager's criterion is utility** — build
> the agent with the most utility first. *(Recorded neutrally as "msilva's manager";
> provenance unconfirmed. It matters: if the criterion came from Gabrielle it is a
> soft withdrawal of her own A5 suggestion, and the top item on the
> confirm-before-2026-08-24 list is answered. If it came from above her, that item
> stays open.)*
>
> **Those are different questions with different answers.** The A5 reasoning below
> is preserved intact — it is still correct, it just answers the older question.
>
> **msilva's own position, stated 2026-08-18: A1 + A2 first.** Recorded as a
> position, not a decision. Nothing has been decided.

Two criteria have now been applied, and they give different answers. **Under
lowest-risk-of-failing (2026-08-17): A5 Watcher, targeting the [[Airtable Proxy]]'s
telemetry** — the reasoning matters more than the answer, because two of the
obvious justifications don't hold up. **Under utility (2026-08-18): the intake
pair, A1 + A2**, which is also msilva's own position.

> [!tip] Looking for the shareable version?
> [[Comparing the first-agent candidates]] is the clean decision doc — motivations,
> pros and cons side by side, written to be handed to someone else. **This page is
> the reasoning record** and remains the authority where the two disagree.

Companion to [[What should the Agent Flow research phase study]], which covers the
wider agenda. This page answers the narrow question.

## Under a utility criterion — the live question (2026-08-18)

The 2026-08-17 analysis optimized for *feasibility and adoption risk*. A utility
test re-ranks everything, and this page's own case against A5 is where it lands:
**least agent-shaped, most cost-exposed, teaches least.** None of that mattered
under "least likely to fail"; all of it matters under "most useful".

### A5's utility ceiling went up; its utility date went out

**New fact, 2026-08-18 (msilva): the plan is to plug Orca and other services into
the [[Airtable Proxy]].** This makes concrete what the proxy page already recorded
as directional — *"a ideia é para ser geral para todos os serviços aqui da
empresa"* ([[Airtable Proxy]]). A confirmation of stated intent, not a new
direction.

- **"Orca or LiveScript?" stops being a choice** and comes off the
  confirm-with-Gabrielle list. One A5 on proxy telemetry eventually covers **every
  service behind the proxy**, including the business-critical ML system whose
  automation is valued at ~10 headcount ([[2026-08-14 Papo de Projetos]]). This is
  the strongest utility case A5 has ever had.
- **Timing is unchanged.** Utility arrives with traffic: GC-5 for [[LiveScript]]
  first, then an Orca migration that has not started, and isn't even mentioned on
  [[Orca Next Version]]'s own roadmap. The ceiling rose; the date moved out.

### A7 cannot be chat-only — raised by msilva 2026-08-18

A7 was the first utility candidate considered, on Packer's evidence that spec
quality bounds agent quality. **msilva's objection: A7 needs more than one way to
gather context, spec and PRD material — chat alone is not enough.**

Correct, and it is the same principle already settled for A5 under *risk 4* below:
**A5 gets direct access to Prometheus, Loki and the observability stack, not just
alert payloads.** A7 is the same shape. The instruction has A7 consulting
A10/A11/A12 to validate — agents that do not exist — so A7 must reach the
underlying sources directly.

> [!tip] Now filed as a concept — [[Agents read primary sources]]
> **Agents read primary sources directly; they do not wait for the agent that was
> meant to digest it.** Three instances (A5, A7, A1). That page also answers why
> context retrieval is **not** a fifteenth agent, and splits A6 into tools +
> curation.

What A7 would need to read, from what the wiki knows exists:

| Source | What it gives A7 | Status |
|---|---|---|
| The **PRD corpus** ([[Proxy Environments]]) | House format, worked examples, outcomes for shipped ones | Exists, flagged |
| The **`Brain` repository** | Institutional memory — already does A6's job ([[2026-08-14 Papo de Projetos]]) | Exists |
| **Linear** (+ legacy `AIRTABLEGC`) | What is built, in flight, or already requested; Packer's ticket template as target shape | Exists, mid-migration |
| The **documentation Hub** | Area documentation | Exists, detail thin |
| **Repos** of the five systems | Real architecture and affected files, both required by Packer's template | Exists |
| **Proxy telemetry** | Usage evidence — A11's input without A11 | Post-GC-5 |
| **Meeting transcripts** | Where discovery context is actually said out loud | Exists, ad hoc |

**Consequence: A7 done properly pulls A6 forward.** Every source there is A6
Curator's job, and A6 is phase 2 by design. A7-with-real-context is not a
closed-scope agent — it is A7 plus a retrieval layer.

> [!important] Correction to this page's own reasoning
> A7's independence was previously argued from its input being a conversation. That
> is too clean. **A7's dependency is not on other agents but on data access** — and
> *"sem dependência cruzada"* governs agents, not sources. Anarchic-first says
> nothing about the second kind.

**The important second consequence: the substrate is shared.** A1 is spec'd to
enrich from A6 and A13; A5 needs Prometheus and Loki; A7 needs the seven sources
above. **The first real engineering problem in this architecture is context
retrieval over existing knowledge, and no agent among the fourteen owns it as a
deliverable.**

### msilva's position — A1 + A2 first

Stated 2026-08-18. **A position, not a decision.**

The pairing matters, because this page's rejection of A1 (below) evaluated **A1
alone**, and its second objection was that *"with no A2 and no branches, its output
is a normalized log."* **A1 + A2 defeats that objection by construction**:
normalize, classify on type/scope/complexity/risk, route. With **humans as the
three branches**, that is a working system on day one. AI-First puts humans at
approval gates; phase 1 with humans as executors behind agentic intake is a
legitimate v1, not a compromise.

**The argument not previously made anywhere:** A1 + A2 is the only build that
produces **the demand dataset needed to spec the other twelve agents** — what
arrives, in what mix, at what complexity and risk, at what volume. Every agent
spec in [[Fluxo Agêntico project instruction]] currently guesses at demand shape,
including the project-start rate that would decide whether A7 is worth building at
all. A1 + A2 measures it. Under a utility criterion, **instrumenting the problem
beats solving one slice of it.**

**On adoption risk — real, but smaller than this page assumed, if sequenced.** Two
channels carry no adoption risk at all:

- **`Bug (sistema)`** — machine-fed by construction
- **The existing `Slack form → Airtable base → team board` path** in the *projetos*
  channel ([[2026-08-14 Recap da Semana]]) — real traffic, existing habit

So **A1 v1 subsumes channels that already carry traffic**, inheriting a habit
rather than asking for a new one. Passive listening and new surfaces become a
measurable expansion instead of an upfront bet — which turns the invocation
problem from a gamble into a sequencing decision.

**Two costs that remain, to be owned explicitly:**

1. **It is a departure from anarchic-first.** Starting at the gateway is
   integrated-first thinking, and the spec is explicit against it. Per this page's
   own rule for A1, raise it with Gabrielle **as a disagreement**, not sideways —
   and she is on leave from 2026-08-24.
2. **A2's output is an interface commitment.** It is the contract every branch
   agent will eventually consume, and per Packer a bad spec propagates.
   *Mitigation*: design it for a **human** consumer first — a triaged queue someone
   works from — and treat the inter-agent format as a later refactor against real
   examples.

### Where the analysis lands under utility

Framed as **"instrument the intake first"** rather than "build the gateway first":
the deliverable is working triage **plus** the demand dataset every other spec
needs. A5 becomes the natural second — by then GC-5 has landed, Orca may be behind
the proxy, and `Bug (sistema)` has both a producer and a consumer.

> [!important] Two facts from 2026-08-17 that both push the same way
> From [[2026-08-17 Weekly - Projetos e Tarefas]], ingested 2026-08-18:
>
> **1. Orca has nothing deployed.** The developer is still on documentation and the
> wordmap. So "A5 second, once Orca is behind the proxy" is further out than
> *unscheduled* — the system it would watch is not running. This **widens the gap
> between A1+A2 and A5** rather than narrowing it. (This is [[Orca Next Version]]'s
> next-version roadmap, not the live system — [[2026-08-14 Papo de Projetos]]
> recording Orca as in production and indispensable describes the *existing*
> system and both are true at once; resolved in full at [[Orca (CDE)]].)
>
> **2. A1 and A2 have a known defect waiting for them.** The team's AI status
> readout misreports progress because it **ignores subtask progress** — 6% against a
> real 20–25% ([[AI status reporting on Linear]]). Anything reading that board for
> state inherits the ambiguity. This does **not** weaken the A1+A2 case; it is a
> design constraint discovered for free, before writing any code, from watching a
> running system fail. Worth more than it costs.
>
> **3. A candidate that didn't exist when this was written.** The **matriz
> external-events gateway** is A1 + A2 in miniature with a real requester and a real
> deadline — see the use-case section in [[Agent Flow]]. It is not on the candidate
> list here and probably should be, though it carries a non-technical blocker: nobody
> knows who approves the requests.

**Still not decided.** The A5 recommendation below is not formally superseded, and
the criterion change has not been confirmed with Gabrielle.

---

**The sections below answer criterion 1 — lowest risk of failing — and are
preserved intact as written on 2026-08-17.**

## Criterion 1 — lowest risk of failing (2026-08-17)

Written 2026-08-17, preserved intact. Still correct; it answers the older
question.

## The argument that actually carries

**A5 is the only agent that needs no human initiative.**

The prior attempt failed for a documented reason: **it was never called.** The bot
sat on one Slack channel waiting to be `@`-mentioned, and people report problems
through whatever path is at hand — a DM, another channel, a meeting, walking over
([[Agent Flow]], corrected by msilva 2026-08-17). **Channel fragmentation**, not
unwillingness.

A1 answers that half by design — it captures from Slack, Monday, email, forms,
webhooks and system alerts. **What it does not answer is invocation**: if each of
those channels still requires a form, a webhook or a mention, the same failure is
rebuilt across six surfaces instead of one.

**A5 is the only agent immune to both halves.** Its input is a system alert: one
channel, no fragmentation, no invocation, no human in the loop at all. It produces
value whether or not anyone changes their behaviour — so it *sidesteps* the
recorded failure rather than betting against it.

Everything else below is supporting.

## Supporting arguments

- **It is the only agent specified well enough to build.** Fixed cadences
  (5 min / 1 h / 24 h), two defined modes, one defined output
  ([[Fluxo Agêntico project instruction]]). Most of the other thirteen get a
  sentence.
- **It is genuinely closed-scope**, which anarchic-first requires. A7 produces a
  PRD with no consumer and no way to learn if it was good; A3 needs demands to
  arrive; A4 needs someone to teach. A5 needs only a system to watch.
- **Failure is cheap.** A wrong A5 is still a monitoring tool. A wrong A7 is a
  document generator nobody reads.

## The honest case against — recorded so it isn't rediscovered

- **A5 is the least agent-shaped of the fourteen.** Continuous monitoring on fixed
  cadences is what monitoring systems do; Grafana already does it for the proxy,
  including a 5-minute window. Built naively, A5 proves an LLM can do what
  infrastructure already does, more expensively.
- **It is the most cost-exposed agent.** ~288 invocations/day, where every other
  agent is event-triggered — and the failure mode diagnosed in
  [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] (a runaway
  loop repeating operations) is worst precisely on a schedule.
- **It teaches least.** The hard problems — agent-to-agent communication, spec
  quality, orchestration — are untouched by it.
- **"Gabrielle suggested it" is thin.** Said on 2026-08-10, before the diagram was
  in hand, before the instruction was read closely, and before the Packer
  material. Never re-confirmed.

**Two conditions neutralise the first two objections**: specify A5 to Packer's
standard (explicit limits, success criteria, metrics), and **do not rebuild
alerting** — leave the 5-minute checks to Grafana and put the agent on the
interpretive pass nobody currently makes.

## Why not A1, the strongest alternative

> [!important] Scope of this section — read with the utility analysis above
> This evaluates **A1 alone**, under the lowest-risk criterion. Objection 2 below
> (*"A1 alone produces nothing usable"*) is **answered by pairing A1 with A2**,
> which is msilva's 2026-08-18 position — see *msilva's position — A1 + A2 first*.
> Objections 1 and 4 are softened but not removed; **objection 3, invocation,
> survives** and is the real cost of starting here.

Raised 2026-08-17. Real merits: A1 is where integration value concentrates, its
spec carries the document's only performance target (**< 10 s**), and an intake
pipeline partly exists already — Slack form → Airtable → team board
([[2026-08-14 Recap da Semana]]).

Four problems:

1. **A1 is the most-connected agent; A5 the least.** The spec has A1 enriching
   from **A6** and **A13**, neither of which exists. Building it first means
   validating a deliberately degraded A1.
2. **A1 alone produces nothing usable.** With no A2 and no branches, its output is
   a normalized log. A5 alone produces alerts a human acts on.
3. **A1 is maximally exposed to the documented failure.** Its whole job is
   receiving what people send; the last attempt died because they didn't.
4. **Its contract can't be designed before its consumers exist.** A1's normalized
   format is a data contract for agents not yet built — and per Packer, a bad spec
   propagates.

> [!important] Choosing A1 first is an argument against anarchic-first
> The strategy's claim is that the spine isn't needed before the limbs —
> *"sem dependência cruzada."* Starting at the gateway is integrated-first
> thinking. That may be right, but it's a **disagreement with the spec** and
> should be raised with Gabrielle explicitly rather than arrived at sideways.

**Where the two converge**: A1's `Bug (sistema)` channel is machine-fed, so it
carries no adoption risk — and its obvious producer is A5. Build A5 first and A1's
machine path comes with it; A1's human channels become a better-informed second
build, with a real consumer to design the contract against.

## The target: the proxy's telemetry

The [[Airtable Proxy]] emits, per request, status code, `retry-after`,
`x-airtable-request-id`, plus `baseId`/`tableId`/`operation` and an `otelhttp`
span — over OTLP to Loki/Prometheus/Tempo. That gives A5 error rates, latency,
429s and availability, sliced by base, table and operation.

> [!warning] The two modes map unevenly
> **Incident detection** fits cleanly. **Opportunity detection does not.** The
> anti-patterns dashboard (full-scan, over-fetch, N+1, cache candidates) is
> *technical* opportunity — inefficient queries. The instruction means *product
> and process* opportunity: *"features sem uso, processos manuais frequentes,
> redundâncias entre áreas."* The proxy can see neither.
>
> State this in A5's spec rather than quietly redefining the agent.

### The timing dependency — narrower than it first appears

**No *production* traffic flows through the proxy yet.** LiveScript still talks to
Airtable directly; the `X-App-Id` header must ship before `AIRTABLE_ENDPOINT_URL`
flips, or every call 401s
([[How LiveScript sends the proxy X-App-Id header]]).

> [!important] But this blocks far less than it sounds like
> Corrected 2026-08-17 — this section previously said A5 *"would be watching an
> empty pipe"*, which reads as "blocked" and is wrong.
>
> The repo ships a local **`grafana/otel-lgtm`** stack, so the proxy can be run
> against a sandbox base with traffic generated on purpose — including deliberately
> bad patterns. **Real metrics, real alerts, real pipeline, today.**
>
> **Only threshold *tuning* needs production traffic.** Alert rules, notification
> policy, the receiver, dedup, the skill and the daily pass are all buildable and
> testable now. See [[How to implement A5 Watcher]].

Production traffic still matters for the *end state*, and the two tracks converge
usefully:

```
GC-5 wiring lands → LiveScript traffic flows through the proxy
   → telemetry becomes real → thresholds can finally be tuned
   → A5 emits Bug (sistema) → the machine-fed channel has its producer
```

So the sequencing is: **build and validate A5 locally now; tune it when the traffic
arrives.** Not "wait for GC-5."

### To verify in the repo, not the wiki

`hasFilter`, `hasFieldProjection`, `recordCount` and `bytes` are recorded as
coming **only from LiveScript's SDK wrapper**, with moving that extraction into
the proxy still open work. So some anti-pattern panels may be fed app-side. Check
what the proxy emits *today* versus what the design doc promises — code wins over
docs.

## How A5 watches: events, not polling

Worked through 2026-08-17. **A5 does not poll.** Grafana alert rules already
evaluate on a schedule and fire — that is what an alerting engine is — so A5
becomes the **webhook receiver**. This also matches the spec: A1's listed channels
include *"webhooks"* and *"alertas de sistemas"*.

```
proxy → OTel → Prometheus / Loki → Grafana alert rule
                                        ↓ webhook
                            grouping + throttling (Grafana policy)
                                        ↓
                                  n8n webhook node
                                        ↓
                          agent: triage, dedup, judgment
                                        ↓
                              Linear issue = Bug (sistema)
```

**The proxy is not modified.** It sits on every app's critical path and its
telemetry is deliberately non-blocking — a buffered channel that drops rather than
stalls. Firing webhooks from the proxy would trade a design property that was got
right for something Grafana already does.

### Why this beats polling

- **Cost collapses** — from ~288 invocations/day to *(real incidents)* + one daily
  pass. This was the strongest objection to A5 and it largely disappears.
- **Detection stays deterministic and free.** "Is the 429 rate above zero" is a
  PromQL threshold: exact, no tokens, no hallucination. The LLM is spent on
  judgment instead — is this actionable, what's the likely cause, does anyone need
  to know.

### Hybrid, because only one mode is event-shaped

**Settled 2026-08-17 (msilva).**

| Mode | Trigger | Why |
|---|---|---|
| **Incident detection** | Event-driven, via Grafana webhook | Threshold-shaped: systems down, error spikes, failing workflows |
| **Opportunity detection** | Periodic — the instruction's **24h report** | Trend-shaped. There is no moment when *"this query has been over-fetching for two weeks"* becomes true |

The 5-minute cadence disappears entirely: **Grafana owns it.**

## Risks this design introduces

Event-driven fixes cost and cadence and introduces harder problems. All are
manageable; none are in A5's spec today.

### 1. Cost inverts — most expensive when things are worst

Polling's unglamorous virtue is a predictable bill uncorrelated with system health.
Event-driven spend is proportional to incident volume, so **A5 costs most exactly
when the system is degraded and someone is firefighting.**

The existing rule makes this concrete: `increase(airtable_429_count_total[5m]) > 0`
fires on **any** 429, and 429s are bursty by nature — that is the premise of the
whole project. During the incident this system exists to catch, that rule fires
continuously. An event storm is the **distributed cousin of the runaway loop** in
[[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]: same bill,
harder to spot, because every individual invocation is legitimate.

**Settled:** grouping and throttling in Grafana's notification policy, applied
**before** the agent, plus a daily invocation ceiling that fails closed.

### 2. Silence is indistinguishable from health

The one to worry about most. Polling fails loudly — the report stops arriving.
Event-driven fails **silently**: no events looks exactly like nothing is wrong.

There is a documented instance of this failure class. Gabriel's flow was stuck on
*"já existe um fechamento em andamento"* with **no execution logs**, on a shared
n8n licence where his runs were crowded out. If A5 lives on that instance, the same
stuck-lock-plus-no-logs failure silences the monitoring agent while everything
downstream looks calm. **A monitoring system that can die quietly manufactures
false confidence.**

**Settled:** the hybrid design resolves this — **the daily pass doubles as a
dead-man's switch.** No daily report means A5 is dead. Reason enough to keep the
periodic pass permanently, not just until the event path works.

### 3. A5 without A13 generates exactly what A13 exists to block

A13 Deduplication's entire job is detecting and blocking duplicated work **before
it enters the flow**. It does not exist. A5 filing an issue per alert for a
recurring condition is duplicate-generation by construction — one under-filtered
query produces the same finding every day, forever.

So A5 inherits A13's responsibility without A13: has this been filed, is it still
open, is this the same root cause as yesterday. **None of that is in A5's spec, and
all of it is required for the output to be usable.**

> [!question] Open direction — build A13 too?
> msilva raised on 2026-08-17 that A13 could be implemented. **Recorded as a
> direction, not a decision.**
>
> The observation that makes it interesting: **A5's required dedup logic and A13's
> purpose are the same thing.** Building A5's deduplication properly may simply be
> the seed of A13 — which would make A13 the natural second build and give two
> independently useful agents, exactly what anarchic-first asks for.
>
> Needs deciding: is A13 a **separate second agent**, or **logic living inside
> A5**? That changes the scope of the first build.

### 4. The intelligence migrates into Grafana

If threshold rules decide what fires, **A5's quality is bounded by static
thresholds a human wrote** — and the instruction asks it to detect *"erros
recorrentes"* and patterns, which is precisely what thresholds cannot do. Pushed
too far, A5 becomes a notification formatter with an LLM in it.

**Settled:** A5 gets **direct access to Prometheus, Loki and the wider
observability stack** — not just alert payloads. Events tell it *when*; query
access lets it establish *what*. The daily pass must be genuinely analytical.

### 5. Alert fatigue has higher stakes than last time

The prior agent lived in a group chat, where noise is ignorable at almost no cost.
A5 files into **Linear** — the board the team plans real work on. The failure mode
isn't "people ignore it", it's "people ask you to turn it off". And this lands
during the Jira→Linear migration, while the board's credibility is still being
established.

### 6. None of this can be tuned yet

Thresholds are tuned against real traffic distributions, and **the proxy carries
none** until GC-5 lands. Any rule written now is a guess, and the first version of
A5 will be mis-tuned by construction. **The tuning loop is the project** — worth
setting that expectation with Gabrielle rather than promising a working agent on a
date.

### 7. A new egress path

Given Loki access, A5 can read log lines. The proxy's invariant is that the PAT is
never logged and response headers pass an allowlist, so the discipline exists — but
an agent quoting log context into an issue tracker is an egress path nobody has
reviewed.

**Settled — an explicit limit:** A5 **never quotes payloads or headers** into an
issue. IDs and metrics only.

> [!tip] Build plan
> [[How to implement A5 Watcher]] carries the concrete steps — alert rules as code
> in the proxy repo, notification policy as the cost control, Linear as the dedup
> state store, and the local `otel-lgtm` stack as the way to build and test most of
> it **before** GC-5 lands.

## A5's limits, as they now stand

Per the spec template's fourth field — what it does **not** do:

- Files and reports; **never fixes**, never touches production.
- Never quotes payloads or headers.
- Stays silent unless it can say **why** something matters.
- Operates on one target's telemetry, not "everything".

## To confirm with Gabrielle before 2026-08-24

Revised 2026-08-18 after the criterion change.

- **Does she accept a utility criterion over a lowest-risk one at all?** This is now
  the question the others hang off. If it came from her, the A5 suggestion is
  withdrawn and the item below is closed.
- **Does she back A1 + A2 first**, accepting that it is a deliberate departure from
  *anarchic-first*? Raise it as a disagreement with the spec, not sideways.
- ~~Is A5 still her recommendation~~ — **superseded in part.** Still worth asking
  whether the diagram, the instruction and the Packer material change her view, but
  the criterion change matters more.
- ~~**Orca or LiveScript?**~~ **Resolved 2026-08-18** — Orca and other services are
  to be plugged into the [[Airtable Proxy]], so it is both, through one chokepoint.
  What replaces it: **is Orca's migration sequenced or directional**, and roughly
  when? **msilva does not know (2026-08-18)** — it isn't on [[Orca Next Version]]'s
  own roadmap — so put no date on "A5 second".
- **How often does the area start a new project?** **msilva has no metric
  (2026-08-18)**, so A7 cannot be evaluated on utility at all — itself an argument
  for A1 + A2, the build that would produce the number.
- ~~**Who owns context retrieval?**~~ **Answered 2026-08-18 — not an agent**; see
  [[Agents read primary sources]]. What to raise instead: **does she accept the A6
  split** into retrieval (tools, now) and curation (an agent, phase 2)? It
  reinterprets the architecture's centrepiece.
- Does she accept the narrower reading of opportunity detection above?
- **Is A13 in scope as a second agent, or is deduplication just logic inside A5?**
  See the open direction under risk 3.
- Does A5 filing into **Linear** have her support, given the migration is still
  bedding in? It puts agent output on the team's live board.
