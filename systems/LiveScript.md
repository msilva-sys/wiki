---
type: system
status: active
updated: 2026-08-17
aliases: [live script, livescript, roteiros, roteiros app, live stre]
tags: [airtable, livescript, realtime, frontend]
---

# LiveScript

Livemode's collaborative script-editing application (*roteiro* = script). **The
heaviest user of the Airtable API at Livemode**, and the reason the
[[Airtable Proxy]] exists.

> [!note] Name reconciliation — settled
> The transcripts say "LiveScript"; the codebase notes say "roteiros app"
> (`config.service.ts`, `narrator.service.ts`). Same system, now confirmed three
> ways: msilva said so directly on 2026-08-17; he said it on the record on
> 2026-08-14 — *"o live script, que é o de roteiros"*
> ([[2026-08-14 Recap da Semana]]); and [[Proxy Environments]] shows the Firebase
> project as `livemode-roteiros-dev` alongside a LogRocket app named
> `livemode-livescript/livescript`.

## Why it strains Airtable

Real-time collaborative editing on a backend that is an API with request limits,
not a database. When one person edits a line, everyone else must see the edit
and the lock immediately. During the **World Cup**, a day of high concurrent
usage exceeded what the Airtable API could absorb —
[[2026-08-10 Onboarding Técnico - Matheus]].

**The failure mode was data loss.** The app didn't simply slow down or go
offline: requests failed and the work people had already done was lost —
*"muitas das requisições que as pessoas faziam davam erro e aí perdia o que foi
feito"* ([[2026-08-14 1-1 Matheus - Gabrielle]]). For a live script being edited
during a broadcast, that is the worst available failure.

**A concrete instance is already identified**: a query in the events panel
returning an entire table when it had no need to. First confirmed sighting of the
over-fetch anti-pattern the [[Airtable Proxy]] dashboards were built to detect.

## Concurrency workarounds (*travas*) — product debt

These exist to reduce API pressure, **not** because the product wants them.
Gabrielle described the ideal as users creating, duplicating, and reordering
freely. Removing them is a downstream benefit of the proxy work.

| Lock | Current behaviour | Ideal |
|---|---|---|
| Row editing | The **entire row** locks, even when two people need different fields | Field-level, or no lock |
| Row creation | **One person at a time** can create | Anyone, concurrently |
| Reordering | Constrained | Free movement of lines |

The frontend is deliberately shaped around these limits and restricts the user
as a result: *"o front ele em algumas [funcionalidades] realmente limita o
usuário."*

## Roadmap involvement

Two initiatives were merged into one programme — [[Airtable Proxy]] plus
**LiveScript stabilization**. Phase 2 of that merged effort: data that doesn't
need to live in Airtable moves into a **database belonging to LiveScript alone**.
Nothing was said about which data, which engine, or when.

## Relationship to the proxy

- LiveScript is the primary source of the traffic the proxy is instrumented to
  measure.
- Its SDK wrapper is currently the **only** source of the `hasFilter`,
  `hasFieldProjection`, `recordCount` and `bytes` signals — the open work in
  [[Airtable Proxy]] is to move that extraction into the proxy so it works for
  every app.
- Named as a candidate target for the first monitoring agent — [[Agent Flow]].

## Wiring to the proxy (auth) — GC-5

Repointing LiveScript at the [[Airtable Proxy]] is **not** a pure config change.
Investigated 2026-08-17 against the app's `airtable` SDK (v0.12.2) —
[[How LiveScript sends the proxy X-App-Id header]] has the full evidence:

- `AIRTABLE_ENDPOINT_URL` repoints traffic (the SDK reads it natively), but the
  proxy now requires an `X-App-Id: livescript` header, and the SDK **cannot carry
  it via `customHeaders`** as shipped — every op the app uses goes through the
  SDK's deprecated `runAction`, which ignores custom headers.
- **Shipped 2026-08-17, reverted 2026-08-18** (commits `754896b` / `d565c26` on
  `feature/airtable-proxy-observability`, dropped via `git reset --hard
  c9cc711`): a `pnpm patch` on `airtable@0.12.2` made `runAction` honour
  `customHeaders`, with the monitored base setting `X-App-Id`, plus centralized
  header injection on the REST path. Reverted so alternatives could be brought
  to Luís first, per
  [[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
  `X-App-Id` is **not currently sent** by either transport. See
  [[How LiveScript sends the proxy X-App-Id header]] for the live options
  comparison.
- The SDK always sends `Authorization: Bearer`, so the app keeps a **dummy PAT**;
  the proxy overwrites it. `X-Api-Key` is deferred — `X-App-Id` only for now.
- **Rollout order matters:** the header shipped before `AIRTABLE_ENDPOINT_URL`
  flips (it's inert against Airtable), so it can't 401 anything until the flip.

## Next.js caching — intentional on the events page, not a bug

Raised 2026-08-18 ([[2026-08-18 1-1 Matheus - Luís]]): msilva found the **events
page** caches browser requests heavily enough that a proxy-side change causing a
401 didn't surface for ~30 minutes. Read initially as a testing hazard.

**Luís's correction: this is correct behaviour for that page** — the events data
changes rarely, so aggressive caching is the right call. The page to actually test
proxy changes against is the **real-time collaborative script editor**, where every
keystroke and lock hits the network with no comparable caching. Confirms this
page's existing framing: Airtable pressure concentrates in the editor, not the
events view.

## Scope boundary, clarified 2026-08-18

LiveScript reaches Airtable via **both** the `airtable` SDK and hand-built REST
calls. Per [[2026-08-18 1-1 Matheus - Luís]], getting `X-App-Id` onto *all* of that
traffic — i.e. fully migrating LiveScript's SDK-routed calls — is explicitly **not**
the current job. The current job is the [[Airtable Proxy]] working correctly;
validating it via the REST-transport traffic alone is an accepted stopping point,
not a gap to close urgently. See the *Scope of the current work* section on that
meeting page.

## Open questions

- Which data is in scope for the dedicated database, and on what engine?
- Which of the *travas* can actually be lifted once the proxy is in place, and
  which are genuine product constraints?
- Is the badly-implemented second Airtable consumer described in the meeting a
  separate app, or another surface of this one? (unverified)
- **Does the `airtable` SDK have a native way to route through a custom
  endpoint/proxy**, without a `pnpm patch`? **Answered 2026-08-18**:
  `endpointUrl` is native and works; `customHeaders` looks native but is a
  no-op for every SDK call this app makes (routes through the deprecated
  `runAction`, which ignores it) — confirmed still true, and the SDK has no
  newer release that fixes it (`npm view airtable dist-tags` still `0.12.2`).
  Full options comparison for Luís in
  [[How LiveScript sends the proxy X-App-Id header]].
