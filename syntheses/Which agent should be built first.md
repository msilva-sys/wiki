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

## To confirm with Gabrielle before 2026-08-24

- Is A5 still her recommendation, given the diagram, the instruction and the
  Packer material — none of which she'd seen when she suggested it?
- **Orca or LiveScript?** Orca has the stronger value story (business-critical ML,
  ~10 headcount) but **unknown observability and unknown access**. The proxy route
  reaches LiveScript's traffic and is fully under msilva's control.
- Does she accept the narrower reading of opportunity detection above?
