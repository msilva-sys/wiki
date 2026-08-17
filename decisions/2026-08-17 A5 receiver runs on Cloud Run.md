---
type: decision
status: active
updated: 2026-08-17
date: 2026-08-17
decided_by: msilva
source: working session 2026-08-17
tags: [agents, a5, infrastructure, cloud-run, n8n]
---

# A5's receiver runs on Cloud Run

**Decision.** A5 Watcher's webhook receiver is a **plain container exposing one
HTTP route, deployed to Cloud Run with `min-instances=0`**. This resolves the
"n8n or Cloud Run" question left open by [[How to implement A5 Watcher]].

Settled after evaluating three alternatives — n8n, Cloudflare Workers, and
co-location with the observability stack. All three are recorded below, because at
least one of them should be reopened later.

## Why

- **Isolation.** The n8n instance is a shared licence with finance, and a stuck
  lock with no execution logs is the documented reason Gabriel's flow could not be
  debugged ([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]).
  A monitoring agent another team can silence is a poor monitoring agent — this is
  risk #2 in [[Which agent should be built first]], and n8n is where it is most
  likely to bite.
- **Local testability.** A container joins the `grafana/otel-lgtm` compose stack,
  so Grafana reaches it by service name and the whole path — rule fires → policy
  groups → webhook → triage → Linear — runs on the laptop with no tunnel. This is
  what makes *"most of A5 is buildable before GC-5"* true of the incident path and
  not just the daily pass.
- **Idle costs nothing.** At `min-instances=0`, Cloud Run bills CPU and memory only
  while a request is in flight. Zero traffic is zero compute charge; the only
  residual is image storage.
  Note this is the **opposite** setting from the [[Airtable Proxy]], which wants
  `min-instances=1` — the proxy sits on a user's critical path where a cold start is
  visible latency, whereas nothing waits on A5. Not a contradiction; different
  services with different callers.
- **It defers the hosting question**, which is the point. A container runs in the
  compose stack today, on Cloud Run tomorrow, and beside a self-hosted Grafana if
  that is what gets decided — same image, same code. The hosting decision is
  downstream of an **open** question (see below), so the choice that doesn't force
  it is the correct one.

Python over Go, despite the [[Airtable Proxy]] being Go: the receiver lives in the
agents monorepo, not the proxy repo, so there is no co-location pull — and A9's
controlled stack names Python and Node, not Go
([[Fluxo Agêntico project instruction]] — [[Agent Flow]] flags that omission as
open). Framework choice within Python is **not** load-bearing; it is one route.

## Scope and limits

- **`min-instances=0`, CPU throttling left ON.** Enabling `--no-cpu-throttling`
  ("CPU always allocated") switches billing to the instance's whole lifetime and
  reverses the cost basis of this decision. Do not enable it.
- **Therefore triage cannot run after returning 200.** CPU is throttled once the
  response is sent, so post-response background work stalls — FastAPI's
  `BackgroundTasks` is a trap here. Hence the queue in *Consequences*.
- This decides the **receiver's runtime**, not where A5 runs in production if the
  observability stack turns out to be self-hosted. Co-location is still better in
  that branch.

## Consequences

- **A Cloud Tasks queue** between the webhook route and the triage worker: it
  absorbs the fast-200 requirement *and* is where the rate ceiling lives as
  configuration (`--max-dispatches-per-second`, `--max-concurrent-dispatches`,
  `--max-attempts`). Not speculative scaffolding — it is the cost control from
  Stage 1b, expressed as queue config.
- **Cloud Scheduler** triggers the daily analytical pass.
- **Auth is asymmetric.** Scheduler and Tasks mint OIDC tokens, so those routes get
  real IAM. **Grafana cannot mint GCP OIDC tokens**, so the webhook route needs a
  shared bearer secret and the service is publicly reachable with one route guarded
  by a string comparison. A real trade, made deliberately.
  The secret's **name** and where to obtain it belong in
  [[Proxy Environments]]; the value never enters this vault.
- **The daily invocation ceiling needs durable shared state.** Cloud Run scales
  horizontally, so an in-process counter neither works nor survives redeploys. Open
  — see below.
- A5's deploy target is now the same platform as the proxy, which keeps secrets and
  deploys in one cloud.

## Alternatives rejected, and what would reopen them

**n8n.** Free webhook node and scheduler, and it is the area's de facto runtime and
what A9's sanctioned stack names. Rejected on isolation, above. *Reopens* if A5
gets an n8n instance msilva controls — but note that operating a second n8n is more
ops than a Cloud Run service, so this is unlikely to become attractive.

**Cloudflare Workers.** The better *fit* for this design's shape, and this is worth
recording precisely because it will be re-proposed:

| Problem | Cloud Run | Workers |
|---|---|---|
| Respond 200 fast, triage after | Cloud Tasks queue | `ctx.waitUntil()` |
| Daily pass | Cloud Scheduler | Cron Triggers, built in |
| Daily ceiling needs durable state | Firestore, or unsolved | Durable Object |

Three GCP services collapse into one Worker with two entrypoints. Rejected on two
constraints, both of which are really the *same* open question:

1. **Reach.** Workers run on the public internet. A private Prometheus/Loki needs
   Cloudflare Tunnel plus Access — legitimate, but it means running `cloudflared`
   beside the stack, and once a daemon runs there, running the receiver there is
   simpler. Skipping query access instead would make A5 triage from the alert
   payload alone, which **reverses risk #4** in
   [[Which agent should be built first]] — A5 becomes a notification formatter.
2. **The skill.** If triage is the Claude Agent SDK loading a skill directory, that
   wants a filesystem and subprocesses, which the Workers runtime does not provide.
   A prompt bundled at build time recovers most of the *instructions-in-files*
   benefit ([[Packaging as skills]]) but not all of it.

*Reopens* if the production backend lands on **Grafana Cloud**, whose Prometheus
and Loki query APIs are public HTTPS with token auth. In that branch Workers are
the better choice and this decision should be revisited.

(unverified: exact Workers CPU-time limits and current Agent SDK support for that
runtime were reasoned from the runtime model, not tested.)

**Co-location beside the observability stack.** *Deferred, not rejected* — and the
best option in one branch. If Grafana, Prometheus and Loki are self-hosted in GCP,
a receiver in the same network is reached at `http://a5-receiver:8080`, which
**deletes the public surface, the bearer token and the auth asymmetry entirely**,
and makes local and production wiring identical. Blocked only by not knowing yet.

## Open questions

- **The production telemetry backend is still undecided** — Cloud Monitoring vs
  Datadog vs New Relic ([[Airtable Proxy]], design §14). `otel-lgtm` is local dev
  only. Every branch above hangs off this, and if the answer is not Grafana, the
  webhook contact-point configuration is throwaway while the receiver survives.
  **Mitigation, adopted:** parse the vendor payload into an internal alert shape at
  the edge, one adapter function, so a backend switch touches one file.
- **Where the daily ceiling's counter lives.** Firestore is the cheapest durable
  option; the alternative is shipping v1 on the queue's rate cap alone and saying
  so rather than implying a ceiling exists.
- **Whether triage is Agent SDK + skill directory, or a single Messages API call.**
  Bears on portability if Workers are revisited.
