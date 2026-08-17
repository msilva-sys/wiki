---
type: synthesis
status: active
updated: 2026-08-17
date: 2026-08-17
aliases: [first agent, A5 first, which agent first]
tags: [agents, planning, a5, a1, a7]
---

# Which agent should be built first?

Worked through on 2026-08-17. **Conclusion: A5 Watcher, targeting the
[[Airtable Proxy]]'s telemetry** — but the reasoning matters more than the answer,
because two of the obvious justifications don't hold up.

Companion to [[What should the Agent Flow research phase study]], which covers the
wider agenda. This page answers the narrow question.

## The argument that actually carries

**A5 is the only agent that needs no human initiative.**

The prior attempt failed for a documented reason: users had to `@`-mention it and
didn't, because *"as pessoas quando têm um problema […] querem falar que têm um
problema"* — they want to report, not file ([[Agent Flow]]). **Nothing in the
14-agent design addresses this.**

A1, A3, A4 and A7 all wait to be addressed. A5 observes. It is the only agent that
produces value whether or not anyone changes their behaviour — so it *sidesteps*
the one recorded failure mode instead of betting against it.

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

### The timing dependency — and why it helps

**The proxy carries no production traffic yet.** LiveScript still talks to Airtable
directly; the `X-App-Id` header must ship before `AIRTABLE_ENDPOINT_URL` flips or
every call 401s ([[How LiveScript sends the proxy X-App-Id header]]). Until GC-5
lands, A5 would be watching an empty pipe.

That makes the two tracks sequential in a useful way:

```
GC-5 wiring lands → LiveScript traffic flows through the proxy
   → telemetry becomes real → A5 has something to watch
   → A5 emits Bug (sistema) → the machine-fed channel has its producer
```

So the research phase has concrete work while the proxy finishes: **specify A5
against telemetry being built now, so the two arrive together.**

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

## A5's limits, as they now stand

Per the spec template's fourth field — what it does **not** do:

- Files and reports; **never fixes**, never touches production.
- Never quotes payloads or headers.
- Stays silent unless it can say **why** something matters.
- Operates on one target's telemetry, not "everything".

## To confirm with Gabrielle before 2026-08-24

- Is A5 still her recommendation, given the diagram, the instruction and the
  Packer material — none of which she'd seen when she suggested it?
- **Orca or LiveScript?** Orca has the stronger value story (business-critical ML,
  ~10 headcount) but **unknown observability and unknown access**. The proxy route
  reaches LiveScript's traffic and is fully under msilva's control.
- Does she accept the narrower reading of opportunity detection above?
- **Is A13 in scope as a second agent, or is deduplication just logic inside A5?**
  See the open direction under risk 3.
- Does A5 filing into **Linear** have her support, given the migration is still
  bedding in? It puts agent output on the team's live board.
