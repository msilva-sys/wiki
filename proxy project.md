# Airtable Proxy — Project Notes (reconciled with the repo)

> **Updated 2026-08-11** after reading the actual repo
> (`livemode-airtable-proxy-main`). The earlier version of this note was a
> research plan written *before* seeing the code, and several of its core
> assumptions were wrong. This version reflects what the repo and design doc
> actually say. Corrections are called out inline.
>
> Source of truth in the repo:
> - `airtable-proxy-design.md` — full design + roadmap
> - `doc/STATUS_proxy_airtable.md` — status as of 2026-06-30
> - `README.md` — how to run v1 locally
> - `internal/proxy/proxy.go` — the actual proxy core

---

## TL;DR — where the project actually is

- **It is not greenfield.** v1 is built (8 commits) and its acceptance test
  passes. There is a working Go proxy, two Grafana dashboards, and a 429 alert.
- **v1 is observability-only.** No caching, no rate-limiting, no enforcement —
  *on purpose*. The strategy is **measure first, then decide what to fix.**
- **My job right now is not to build caching or a rate limiter.** Those are
  deferred (roadmap phases 5–6). The live work is enriching the telemetry.

---

## Corrected mental model (things I had wrong)

1. **Language is Go, not Node.** The proxy is Go's `net/http/httputil.ReverseProxy`.
   Ignore any "in-memory Node cache / Redis-vs-Node" framing — not relevant.

2. **No DNS cutover.** I assumed clients would transparently hit
   `api.airtable.com` and DNS would silently redirect to the proxy. The real
   onboarding (design §11) is **explicit per-app reconfiguration**:
   - `airtable` SDK: set `endpointUrl: <proxy-url>`
   - raw REST: swap the base-URL constant
   - add headers `X-App-Id` + `X-Api-Key`
   - drop the PAT from the app env
   So there is no "intercept at the DNS layer" step to plan.

3. **Observability-first, not caching-first.** The whole premise is: instrument
   every Airtable call, *then* let the dashboards tell us whether 429s come from
   duplicate reads (→ cache) or sustained volume (→ rate limit). We don't guess
   up front.

4. **Token-terminating auth (decided).** The proxy holds the Airtable PAT
   (Secret Manager) and injects `Authorization: Bearer {PAT}` on every request.
   Apps authenticate to the *proxy* with their own `X-Api-Key`. Apps never see
   the PAT. (Design §4.) — This answers my old "pass through or translate the
   key?" question.

5. **Instrumentation is decided: OpenTelemetry / OTLP.** Local dev uses a single
   `grafana/otel-lgtm` container (Loki + Prometheus + Tempo + Grafana).
   Production points the same OTLP exporter at Cloud Monitoring / Datadog / New
   Relic — proxy code unchanged. **No BigQuery** (Log Analytics is the optional
   upgrade if ad-hoc SQL is ever needed). — Answers my old "BigQuery? Datadog?"
   question.

6. **Deployment target is decided: Cloud Run, `min=1 max>1` for HA.** The proxy
   is on every app's critical path, so a single instance is unacceptable. v1
   holds no shared state, so multiple instances need no coordination. (Design §3.)

---

## The caching landmine (remember this)

Airtable record payloads embed attachment URLs (`v5.airtableusercontent.com`)
that **expire in ~2 hours** — which is exactly why the app currently uses
`no-store`. Therefore any future cache must apply **only to
metadata / schema / field-options, never to record data**. (Design §7.)

The clearest cache win already spotted — metadata refetched on every request —
is actually an **app-side fix** (`config.service.ts`, `narrator.service.ts`),
not a proxy feature. Good to know before proposing "just add a cache."

---

## What's already built (v1 — done)

- **`internal/proxy/proxy.go`** — `ReverseProxy` with `Director` (injects PAT) +
  `ModifyResponse` (captures status/`retry-after`/`x-airtable-request-id`).
  Telemetry pushed onto a buffered async channel (cap 1024) so the hot path adds
  zero latency. Non-blocking send: drops rather than stalls if the channel fills.
- **`internal/proxy/telemetry.go`** — parses `baseId`/`tableId`/`operation` from
  the request path.
- **`internal/otelsetup/otel.go`** — 3 OTel providers (trace/metric/log) → OTLP/gRPC.
- **`cmd/proxy/main.go`** — `/healthz`, graceful shutdown, `otelhttp` span per request.
- **Dashboards** — "Airtable Proxy — Overview" (6 panels) + "Airtable Usage &
  Anti-patterns" (16 panels: full-scan, over-fetch, N+1, cache candidates,
  rate-limit headroom vs 5 req/s, …).
- **Alert** — `airtable_429_alert`: fires when `increase(airtable_429_count_total[5m]) > 0`.
- **`scripts/acceptance.sh`** — validates the 4 design §9 done-criteria.

**Security invariants (do not break):** PAT / Authorization is *never* logged;
response headers pass through an allowlist only.

---

## What's actually open next (the real work)

From `STATUS_proxy_airtable.md`, immediate items — all about *enriching
telemetry*, not enforcement:

- [ ] **Extract usage signals in the proxy itself:** `hasFilter`,
      `hasFieldProjection`, `recordCount`, `bytes`. Today these come only from
      the roteiros app's SDK wrapper — `proxy.go`'s `emit()` logs
      status/latency/operation but not these. Moving them into the proxy makes
      anti-pattern detection work for *any* app, not just roteiros.
- [ ] **Normalize `app.route`** into logs/spans (from Next.js
      `getRequestContext()`) for proxy↔app correlation, then add a "Top app
      routes by Airtable calls" panel.
- [ ] Clean the SDK-serialized body (`{id, fields}` only; drop `_table`/`_base`).
- [ ] Commit roteiros observability work on `feat/airtable-observability-local`
      (not `main`, no push).

**Deferred (do not start unprompted):** multi-app API-key auth (phase 3),
Pulumi IaC (phase 4), rate-limiting (phase 5), metadata cache (phase 6).

---

## Corrected learning roadmap

**Still relevant:**
- Airtable API fundamentals + the **5 req/s per base** limit (429 → ~30s
  lockout, `Retry-After` header present).
- HTTP caching basics — but framed around the metadata-only constraint above.
- Reverse-proxy patterns — specifically Go's `httputil.ReverseProxy`
  (`Director` / `ModifyResponse` / `ErrorHandler`), not Express middleware.

**Newly important (weren't on the original list):**
- **Go** — enough to read/modify `internal/proxy/*`. This is the real gap.
- **OpenTelemetry / OTLP** — the metric/log/trace SDK the whole thing runs on.
- **Grafana + LGTM stack** (Loki / Prometheus / Tempo) — how the dashboards are
  queried (LogQL, PromQL, TraceQL).

**De-prioritized (deferred phases):**
- Token-bucket rate limiting — real, but phase 5, and harder than the original
  note assumed (multi-instance → needs Memorystore or a per-instance budget).
- Request queueing — not planned; the design explicitly avoids a queue.

---

## Questions still genuinely open (worth asking Gabrielle / team)

Most of my original "questions to ask" are already answered by the design doc.
These remain open (design §14):

- **Telemetry backend for prod** — Cloud Monitoring vs Datadog vs New Relic?
- **Retain queryable raw records for usage analysis?** (Log Analytics / BigQuery
  / SaaS query language) — cheap to decide now, hard to backfill later.
- **Caching freshness tolerance** per table/operation — still the right question,
  but scoped to *metadata only* given the attachment-URL constraint.
- Pulumi language (Go / TS / Python); notification channel (Slack / PagerDuty /
  email); where the app API-key map lives first; one base or all bases in v1.

**To verify (couldn't check myself):** the repo is on a network share with no
visible `.git`, so I can't confirm the "8 commits / acceptance passed" claims in
the status doc. Worth confirming the actual git state matches the doc.

---

## Things to actually do

- [ ] Get a **dev** Airtable PAT + a test base id → run `docker compose up --build`
      and watch the dashboards populate (README "Run it").
- [ ] Learn just enough Go to read `internal/proxy/proxy.go` comfortably.
- [ ] Decide with the team on the immediate telemetry-enrichment work above.
- [ ] Ask Gabrielle the still-open §14 questions (not the ones already answered).
