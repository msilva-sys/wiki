---
type: decision
status: stable
updated: 2026-08-24
date: 2026-08-24
decided_by: Matheus Silva
source: "Chat with msilva, 2026-08-24, no transcript; rationale confirmed by Luís via Slack, same day"
tags: [proxy, airtable, auth]
aliases: []
---

# Control base access via PAT scoping, not proxy logic

**Decision.** [[Airtable Proxy]] will not implement its own base-level
authorization logic — a per-app base allow-list enforced in the proxy's code.
Which Airtable bases an app can reach is controlled by scoping that app's
Airtable PAT itself, using Airtable's own PAT-level base permissions, not by
a check inside the proxy.

## What this changes

- Corrects [[2026-08-21 Deploy Airtable Proxy privately behind VPN]], which
  described the request path as the proxy "verifies the configured base
  allow-list," and named "per-app base allow-lists" as one of the
  first-phase trust-model mitigations. That verification step is not being
  built; the page is corrected inline.
- Resolves the open question on [[Airtable Proxy]], "one base or all bases
  in v1" — there is no proxy-side base-restriction feature to decide the
  shape of. Base restriction is delegated entirely to how each PAT is scoped
  in Airtable.
- The trust-model description ("the app-id path selects the PAT, base
  allow-list, and telemetry identity") loses the base-allow-list clause —
  the app-id path now only selects the PAT and the telemetry identity.

## Why

Confirmed by Luís via Slack, 2026-08-24, after seeing the decision — this is
the rationale, not just agreement:

> *"Mas isso não tá já direto na config da PAT do Airtable? Não entendi a
> utilidade de termos isso dentro do Proxy. Nem lembro se fui eu que coloquei,
> mas não me aprece fazer sentido hoje. E não acho que deveríamos ter casos de
> aplicações compartilhando PAT né."*

Two distinct points:

1. **Redundant boundary.** Airtable's own PAT configuration already scopes a
   token to specific bases — a proxy-side allow-list would duplicate an
   enforcement point Airtable already provides. Luís doesn't recall deciding
   to add it originally, and doesn't think it holds up now.
2. **No PAT sharing across apps.** Each app should have its own PAT, never a
   PAT shared between multiple apps. This isn't a change — [[Airtable
   Proxy]]'s `PROXY_APPS` config already stores one token per app id — but it
   is now an explicit constraint, not just today's shape. It's also *why*
   point 1 holds: a base allow-list only earns its keep as a proxy feature if
   one PAT might legitimately serve several apps needing different bases: with
   one PAT per app, the PAT's own Airtable-side scope already **is** that
   app's base allow-list.

## Still true, unaffected by this

- PAT injection (the proxy still holds and injects the real Airtable PAT
  per app), path-based app identification, and the VPN/network trust
  boundary are all unchanged.
- Per-app PAT selection via `PROXY_APPS` is unchanged — only the
  base-verification step layered on top of that selection is dropped.

## Loose end

The repo's `PROXY_APPS` config shape already carries a `"bases": []` field
per app entry (`.env.example`, `livemode-airtable-proxy`) — the allow-list
this decision drops. It's currently unused/empty in the example and this
decision doesn't remove it from the code; that's a small follow-up for
whoever touches that struct next, not done here.
