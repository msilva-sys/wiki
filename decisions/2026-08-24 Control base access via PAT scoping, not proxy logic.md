---
type: decision
status: stable
updated: 2026-08-24
date: 2026-08-24
decided_by: Matheus Silva
source: "Chat with msilva, 2026-08-24, no transcript"
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

Not captured beyond the decision itself — no transcript, no reasoning given
in chat. *(unverified: ask msilva if a rationale is worth recording — e.g.
Airtable already offers PAT-level base scoping, so a proxy-side allow-list
would duplicate enforcement of the same boundary for no real gain.)*

## Still true, unaffected by this

- PAT injection (the proxy still holds and injects the real Airtable PAT
  per app), path-based app identification, and the VPN/network trust
  boundary are all unchanged.
- Per-app PAT selection via `PROXY_APPS` is unchanged — only the
  base-verification step layered on top of that selection is dropped.
