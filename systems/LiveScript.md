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

> [!note] Name reconciliation
> The transcript says "LiveScript" (garbled as *live stre / live strip /
> Live Scit*); the codebase notes say "roteiros app" (`config.service.ts`,
> `narrator.service.ts`). Confirmed by msilva on 2026-08-17 that these are the
> same system. Both names kept as aliases.

## Why it strains Airtable

Real-time collaborative editing on a backend that is an API with request limits,
not a database. When one person edits a line, everyone else must see the edit
and the lock immediately. During the **World Cup**, a day of high concurrent
usage exceeded what the Airtable API could absorb —
[[2026-08-10 Onboarding Técnico - Matheus]].

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

## Open questions

- Which data is in scope for the dedicated database, and on what engine?
- Which of the *travas* can actually be lifted once the proxy is in place, and
  which are genuine product constraints?
- Is the badly-implemented second Airtable consumer described in the meeting a
  separate app, or another surface of this one? (unverified)
