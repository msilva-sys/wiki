---
type: synthesis
status: draft
updated: 2026-08-17
date: 2026-08-17
aliases: [A5 implementation, watcher implementation]
tags: [agents, a5, implementation, grafana, observability, n8n]
---

# How to implement A5 Watcher

Concrete build plan. Design rationale and risks are in
[[Which agent should be built first]]; this page is the *how*.

> [!info] Status: draft, unvalidated
> Written 2026-08-17 from the wiki's record of the [[Airtable Proxy]] repo. Several
> details below are marked **verify** — the schema rule is that code wins over
> docs, and the repo notes date from 2026-08-11 plus the GC-5 work.

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

Two viable homes, and this is an open decision:

| | **n8n** | **Cloud Run service** |
|---|---|---|
| Webhook receiver | Free, built in | Build it (small Go/Node service) |
| Scheduling | Built in | Cloud Scheduler |
| Isolation | **Shared licence with finance** — crowded logs, and a stuck lock blocked debugging entirely ([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]) | **Isolated.** msilva controls it |
| Fits existing infra | The area's de facto runtime | The proxy already deploys to Cloud Run |
| Effort | Low | Higher |

**The n8n isolation problem is not hypothetical** — it is the documented reason
Gabriel's flow could not be debugged. A monitoring agent that can be silenced by
another team's stuck execution is a poor monitoring agent. Worth weighing against
n8n being what the area actually uses, and what A9's sanctioned stack names.

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

- **n8n or Cloud Run** for the receiver.
- **Which alerts** ship in the first version. Fewer is better; start with 429
  sustained and proxy liveness.
- Does filing into **Linear** have Gabrielle's support mid-migration?
- Are alert rules welcome **in the proxy repo**, or does that couple two projects
  more than Luís wants?
