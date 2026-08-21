---
type: decision
status: stable
updated: 2026-08-21
date: 2026-08-19
decided_by: Matheus Silva, Luís Fernandez
source: "[[2026-08-19 1-1 Matheus - Luís]]"
tags: [proxy, auth, identification, sdk]
aliases: [URL-path app identification, drop X-App-Id header]
---

# Identify proxy apps by URL path, not header

**Decision.** The [[Airtable Proxy]] identifies which app is calling it by the
**URL path the app is configured to hit** — e.g. `proxy.livemode.com/livescript`
— instead of a header like `X-App-Id`. Settled 2026-08-19, msilva's call, with
Luís's explicit sign-off. Confirmed directly with msilva (2026-08-19): it's a
**path**, not a subdomain.

## Why

[[How LiveScript sends the proxy X-App-Id header]] found that the `airtable`
SDK doesn't reliably carry a custom header — `customHeaders` is a no-op on the
`runAction` transport every real call in [[LiveScript]] uses, and the only
fixes on the table were a `pnpm patch` Luís distrusts, or a real migration off
the SDK. Luís's framing in this call: *"a gente não tem controle que as
pessoas não vão usar o SDK[.] Então teria que ser alguma solução que funciona
com SDK também."*

A URL path is set once, at onboarding, as part of the same `endpointUrl` /
base-URL config every app already needs to point at the proxy at all
([[Airtable Proxy]] point 2, design §11). It doesn't ride inside the request
the way a header does, so it works identically whether the app calls through
the SDK or hand-built REST — there is no separate SDK-transport problem left
to solve.

msilva's own summary of the payoff, on why he'd been holding back: *"a gente
teria dois lugares de validar[...] a identificação, né? tanto [URL] quanto
cabeçalho. Mas[...] não tem pra onde fugir."* Luís, closing it: *"a gente pode
descartar o header, a gente passa a usar só isso[.] Não precisa mais procurar
no header porque independente se tá vindo pelo SDK ou pela chamada rest, a
gente vai fazer a mesma solução."*

## What this retires

[[How LiveScript sends the proxy X-App-Id header]]'s entire five-option
comparison (`pnpm patch` / `customHeaders`-only / REST migration / SDK upgrade
/ `node-fetch` interceptor) existed to get a header onto SDK traffic. None of
that work is needed if identification doesn't depend on a header at all. That
page now points here instead of continuing to carry the comparison as live.

## What's still open

- ~~**Routing detail beyond "path."**~~ **Resolved in code 2026-08-20**: callers
  use `/{appId}/v0/...`; the proxy removes the leading app segment before its
  authorization/parser/director pipeline sees the canonical `/v0/...` path.
- **`X-Api-Key` / app-key authentication remains a future layer.** Identification
  and authentication stay decoupled. The first private deployment deliberately
  trusts the VPN/network and uses the path only for PAT selection, base
  authorization, and telemetry; see
  [[2026-08-21 Deploy Airtable Proxy privately behind VPN]].
- ~~**Whether `X-App-Id` is retained as a redundant check.**~~ **Resolved in code**:
  it is removed from identity handling and stripped before forwarding upstream.
- ~~**Not yet implemented.**~~ **Implemented and hardened** by repo commits
  `8e4297b` (2026-08-20), `bf5e681` and `12d1423` (2026-08-21). The repo and
  tests are now the ground truth for canonical-path and trailing-slash behavior.
- ~~Luís had separately floated a written "mini relatório" comparing solutions
  before "batendo o martelo" — unclear whether a written report is still
  wanted for the record.~~ **Answered 2026-08-19, msilva** (via a `[!msilva]`
  callout on [[2026-08-19 1-1 Matheus - Luís]], since resolved and deleted
  there): no — the identification question is already settled, no written
  report needed.
