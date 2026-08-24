---
type: synthesis
status: deferred
updated: 2026-08-24
date: 2026-08-17
aliases: [A5 implementation, watcher implementation]
tags: [agents, a5, implementation, grafana, observability, n8n]
---

# How to implement A5 Watcher

> [!danger] Deferred — msilva, 2026-08-24
> [[2026-08-24 Deprioritize A5 Watcher as first-agent candidate]]: not
> active work for now. Plan preserved as-is so it doesn't need rebuilding
> if A5 becomes relevant again later.

Concrete build plan. Design rationale and risks are in
[[Which agent should be built first]]; this page is the *how*.

> [!info] Status: draft, unvalidated
> Written 2026-08-17 from the wiki's record of the [[Airtable Proxy]] repo. Several
> details below are marked **verify** — the schema rule is that code wins over
> docs, and the repo notes date from 2026-08-11 plus the GC-5 work.

> [!warning] This plan is one narrow design, not "the" design — 2026-08-19
> [[2026-08-19 1-1 Matheus - Gabrielle]] lays out two candidate shapes for
> Watch: **Path A**, instrumented per-project, and **Path B**, a consolidator
> of tools already in use (LogRocket, Vercel, and the proxy as one input among
> several rather than its defining scope). **Everything below is Path A
> applied to a single source** — the proxy's own OTel pipeline — and doesn't
> yet reflect the multi-tool framing. It may still be the right first slice
> (the proxy is the source msilva controls and can validate locally), but
> don't read it as covering Watch's full design space. See [[Agent Flow]]'s
> *Proposed shape* section for the reconciliation angle with Luís's
> proxy-scoping objection.

## The pipeline

```
proxy ──OTel──► Prometheus / Loki / Tempo
                        │
                        ├─► Grafana alert rule        (detection: deterministic, free)
                        │        │
                        │        ▼
                        │   notification policy       (grouping, throttling, silences)
                        │        │ webhook
                        │        ▼
                        │   receiver ──► agent ──► Linear issue = Bug (sistema)
                        │                  │
                        └──── query ───────┘         (triage: PromQL + LogQL)

            daily cron ──► agent ──► trend report + heartbeat
```

**Detection is Grafana's job. Judgment is the agent's job.** The split is what
makes this cheap: a threshold breach costs nothing to evaluate, and the LLM is
only invoked when something already looks wrong.

## Where the code lives

**Decided 2026-08-17 (msilva): a monorepo, containing only A5 to begin with.**
Monorepo *layout*, single-agent *contents* — explicitly not building infrastructure
for fourteen agents up front, which would be the *"ticket de foundation cheio de
funções para uso futuro"* that
[[Gabriel Packer - DAG-driven agent orchestration]] warns against.

```
livemode-agents/
├── CLAUDE.md              ← how to work in this repo
├── contracts/
│   └── bug-sistema.md     ← the only shared artifact worth writing now
├── shared/                ← deliberately empty
└── agents/
    └── a5-watcher/
        ├── README.md      ← inputs/outputs · consults/feeds · success criteria · limits
        ├── skill/         ← triage logic, packaged
        ├── receiver/      ← webhook handler + dedup
        └── tests/
```

Four deliberate choices:

- **`agents/a5-watcher/` rather than `src/`.** The naming is the entire cost of
  "starting as a monorepo", and it means the second agent is an addition rather
  than a refactor. Paid once, today, for nothing.
- **`shared/` stays empty until two agents need the same thing.** Extract on the
  second implementation, never on the first — a shared library shaped by a guess
  fits nothing.
- **Per-agent `README.md` is the four-field spec** the instruction requires
  ([[Fluxo Agêntico project instruction]]). Putting it at each agent's root makes
  the repo structure enforce the spec discipline instead of relying on memory —
  and it is what a second person reads first, which is the **maintenance** gap
  identified in [[Agent Flow]].
- **The repo gets its own `CLAUDE.md`.** Instructions in files, not memory —
  the lesson from
  [[Gabriel Packer - solo founder AI workflow (part 1)]]: *"memory compacts and
  agents forget context mid-task; files persist and every agent reads the same
  source of truth."*

### `contracts/bug-sistema.md` — write this first

The payload A5 emits and A1 will eventually consume. **The only artifact with two
consumers before either exists.** Writing it now makes Phase 2 integration a wiring
exercise rather than an archaeology exercise, and it is the concrete form of
Packer's *"ter um bom contrato de API"*. Everything else in `shared/` can wait.

### A5 spans two repos, on purpose

**Alert rules stay in the [[Airtable Proxy]] repo**, not here. They query metric
names the proxy emits, so co-locating means a rename and its rule update land in
the same commit; split them and a rename silently breaks detection — the worst
failure mode available to a monitoring system.

The cost is that A5 is not contained in one place. **`agents/a5-watcher/README.md`
must say so explicitly**, with a pointer to the rules. A judgment call; reasonable
people split the other way, since one could argue A5 owns its own detection.

> [!question] Open — where does this repo sit?
> A fourth repository alongside `Brain`, the proxy, and [[LiveScript]]. Does the
> **homologation flow** from [[2026-08-14 Papo de Projetos]] have an opinion? And
> if **A6 Curator** is institutional memory as files, does its memory live in this
> repo, or does A6 point at this vault — which is already a working prototype of
> that pattern, and is not in a repo with the agents.

## Build order

Stage 1 and 2 are **not blocked by GC-5** — see *Testing without traffic* below.

| Stage | What | Blocked by |
|---|---|---|
| **1** | Alert rules + notification policy, provisioned as code | — |
| **2** | Receiver → agent → Linear, with dedup | — |
| **3** | Daily analytical pass (also the heartbeat) | — |
| **4** | Threshold tuning against real traffic | **GC-5** |

## Stage 1 — Alert rules, as code

Grafana supports provisioning alert rules and notification policies from YAML.
**Put them in the proxy repo** alongside the dashboards: the rules depend on metric
names the proxy emits, so they should version with the code that emits them.
Renaming a metric and breaking an alert silently is otherwise a matter of time.

Candidate rules, from signals the wiki records as already emitted:

| Rule | Signal | Note |
|---|---|---|
| **429s** | `airtable_429_count_total` | Rule exists. **Re-tune** — `increase(...[5m]) > 0` fires on any single 429, and 429s are bursty by design. Use a rate over a window plus a `for:` duration so it fires on *sustained* limiting, not one blip |
| **Error rate** | status captured in `ModifyResponse` | 5xx from Airtable vs errors originating in the proxy — separate rules, different owners |
| **Latency** | `otelhttp` span duration | p95/p99 against a baseline, per operation |
| **Rate-limit headroom** | request rate per `baseId` | Approaching the 5 req/s ceiling — see [[Airtable Rate Limits]]. This is the *predictive* one, and the most valuable |
| **Proxy liveness** | absence of metrics, or `/healthz` | A `for:`-guarded no-data alert. **Without this, a dead proxy is silence** |

**verify**: exact metric names and label sets against `internal/proxy/telemetry.go`.
The wiki records a naming convention distinction (metrics vs logs/spans) that has
not been re-checked — confirm before writing PromQL against it.

## Stage 1b — Notification policy: the cost control

This is where the event-storm risk from
[[Which agent should be built first]] is contained, and it is configuration, not
code:

- **Group by** `alertname` + `baseId` + `tableId` + `operation`. One incident
  produces one notification, not one per request.
- **`group_wait`** — hold briefly before the first notification so a burst
  coalesces.
- **`repeat_interval`** — long. A condition that persists should not re-notify
  every few minutes; that is how the agent's bill and the team's patience both go.
- **Silences** for known-degraded windows.

**Then a ceiling that fails closed**: a counter in the receiver that stops invoking
the agent past N per day and files one "A5 throttled" issue instead. Grafana
grouping reduces volume; the ceiling bounds it.

## Stage 2 — Receiver and agent

**Decided 2026-08-17 (msilva): a plain container on Cloud Run, `min-instances=0`.**
Full rationale and the three rejected alternatives —
[[2026-08-17 A5 receiver runs on Cloud Run]]. In short: n8n loses on isolation
(a shared licence with finance, and the documented reason Gabriel's flow could not
be debugged), and a container is the choice that **defers** the hosting question,
which is downstream of the still-open production telemetry backend.

Three consequences that shape the code:

- **Idle costs nothing, and one setting reverses that.** Leave CPU throttling ON;
  `--no-cpu-throttling` bills the instance's whole lifetime.
- **Therefore triage cannot run after returning 200** — CPU is throttled once the
  response is sent, so `BackgroundTasks` stalls. A **Cloud Tasks queue** sits
  between the webhook route and the triage worker, and is where the ceiling from
  Stage 1b lives as config: `--max-dispatches-per-second`,
  `--max-concurrent-dispatches`, `--max-attempts`.
- **Auth is asymmetric.** Cloud Scheduler and Cloud Tasks mint OIDC tokens and get
  real IAM. Grafana cannot, so the webhook route needs a shared bearer secret and
  the service is publicly reachable with one route guarded by a string comparison.

### Parse at the edge

Convert the vendor payload into an internal alert shape in **one adapter function**,
and let triage, dedup and the issue builder work on that shape only. The production
backend is undecided (Cloud Monitoring / Datadog / New Relic — [[Airtable Proxy]]
§14), and their alert webhooks carry the same *concepts* in different JSON. This is
a parse boundary the receiver needs regardless — distinct from the `shared/`-stays-
empty rule, which is about not guessing at structure shared between agents that
don't exist yet.

### What the receiver must handle

- **`status: "resolved"`.** Grafana sends these when a condition clears, provided
  the contact point keeps `disableResolveMessage: false`. Without handling them A5
  files bugs and never learns they cleared — the Linear board fills with stale open
  issues, which is exactly the alert-fatigue failure mode in risk #5.
- **Fingerprint from `groupLabels`, not the per-alert `fingerprint`.** `groupLabels`
  is precisely what the notification policy grouped on, so dedup and grouping
  cannot drift apart. Grafana's own `fingerprint` is over the full label set and is
  too granular for issue-level dedup. (**verify** the field names against the
  running Grafana version.)
- **`truncatedAlerts > 0`** is itself a storm signal — surface it as *"and N more"*
  rather than implying A5 saw everything.
- **Ignore payloads without the routing label.** Grafana's contact-point *Test*
  button sends a synthetic payload with `alertname: TestAlert` and no `a5` label, so
  gating on that label makes test notifications exercise auth and parsing without
  filing an issue.

## Stage 2b — Deduplication, without building A13

A5 must answer: *has this been filed, is it still open, is it the same root cause?*
Otherwise a recurring condition files the same issue daily — precisely what A13
exists to block.

**Use Linear as the state store.** No new infrastructure:

1. Compute a **fingerprint** from the alert's grouping labels — e.g.
   `a5:429:{baseId}:{tableId}:{operation}`.
2. Search Linear for an open issue carrying that fingerprint (a label, or a fenced
   marker in the description).
3. **Found and open** → add a comment with the new occurrence; do not create.
4. **Found and closed recently** → new issue, linked to the previous one.
   Recurrence after a fix is itself a signal.
5. **Not found** → create.

That logic *is* A13's core, scoped to one producer. If A13 later becomes its own
agent, this is what gets extracted.

## Stage 3 — The daily pass

A cron-triggered run over the accumulated window. Two jobs:

- **Trend analysis** — the opportunity mode. Query Prometheus and Loki directly for
  over-fetch, N+1 patterns, unused-index-style waste, headroom drift. This is the
  work the anti-patterns dashboard currently requires a human to do, and it is
  where an agent earns its place.
- **Heartbeat.** Its arrival is proof A5 is alive. **Its absence is the only signal
  that event-driven monitoring has died silently.** Do not drop this stage once the
  incident path works.

## The agent's tools

Both are plain HTTP; no SDK required:

- **Prometheus** — `GET /api/v1/query`, `/api/v1/query_range` (PromQL)
- **Loki** — `GET /loki/api/v1/query_range` (LogQL)
- **Tempo** — trace lookup by `x-airtable-request-id`, for correlating a specific
  failing request

Give it **read-only** credentials. Its limits say it never fixes; the credentials
should make that structurally true rather than merely instructed.

## Package the triage logic as a skill

Consistent with the packaging thread converging from
[[Sharing the accounting automation with the team]],
[[Gabriel Packer - DAG-driven agent orchestration]], and Gabriel's working audit
component. A skill is testable in isolation, shareable, and keeps the context it
loads narrow — the cost lever.

Rough contract: **in** — alert payload plus query access; **out** — a verdict
(`actionable` / `noise` / `needs-human`), a one-line reason, a fingerprint, and a
proposed issue body.

## Testing without production traffic

**This is the part that unblocks most of the work.** The repo already ships a
`grafana/otel-lgtm` docker-compose stack (Loki + Prometheus + Tempo + Grafana) and
`scripts/acceptance.sh`.

So, locally and today:

1. Run the proxy against a sandbox base and generate traffic — including
   deliberately bad patterns: unfiltered full-table reads, N+1 loops, and enough
   volume to trip 429s.
2. Watch real alerts fire against real metrics.
3. Build and exercise the whole receiver → agent → Linear path.
4. Use Grafana's test-notification feature for storm behaviour and throttling.

**Only threshold *tuning* needs production traffic.** Everything else — rules,
policy, receiver, dedup, the skill, the daily pass — can be built and validated
before GC-5 lands. The dependency is narrower than it first appeared.

## Success criteria

Per the spec template's third field, and aimed squarely at the fatigue risk:

- **Action rate** — proportion of filed issues a human actually acts on. The
  primary metric; if it falls, A5 is becoming noise.
- **Zero duplicates** per fingerprint while an issue stays open.
- **Invocations/day** under the ceiling, including during an induced storm.
- **Detection latency** — alert fired to issue filed.
- **Liveness** — the daily report arrives every day.

## Open decisions

- ~~n8n or Cloud Run for the receiver~~ — **settled 2026-08-17**, see
  [[2026-08-17 A5 receiver runs on Cloud Run]].
- **Where the daily ceiling's counter lives.** Cloud Run scales horizontally, so an
  in-process counter neither works nor survives a redeploy. Firestore is the
  cheapest durable option; the alternative is shipping v1 on the queue's rate cap
  alone and **saying so** rather than implying a ceiling exists.
- **Which alerts** ship in the first version. Fewer is better; start with 429
  sustained and proxy liveness.
- Does filing into **Linear** have Gabrielle's support mid-migration?
- Are alert rules welcome **in the proxy repo**, or does that couple two projects
  more than Luís wants?
- **Does Watch stay proxy-only (Path A/this plan), or become a multi-tool
  consolidator (Path B: proxy + LogRocket + Vercel)?** New 2026-08-19 — see the
  warning at the top of this page and [[Agent Flow]]. Unresolved, and upstream
  of most of the decisions above: a consolidator's pipeline, tools, and
  triage-skill inputs look different from this proxy-only one. **msilva's own
  follow-up, added afterward: maybe both** — Path B's aggregation plus Path
  A's in-service integration where a service's scope justifies it, via a
  shared SDK for the in-service half rather than bespoke setup per project.
  Also unresolved; see [[Agent Flow]].
