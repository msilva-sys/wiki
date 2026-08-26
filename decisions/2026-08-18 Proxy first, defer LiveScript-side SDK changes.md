---
type: decision
status: active
updated: 2026-08-26
date: 2026-08-18
decided_by: Luís Fernandez
source: "[[2026-08-18 1-1 Matheus - Luís]]"
tags: [proxy, livescript, scope]
aliases: [proxy first, LiveScript SDK deferred, scope boundary proxy vs livescript]
---

# Proxy first, defer LiveScript-side SDK changes

**Decision.** msilva's job is to get the [[Airtable Proxy]] itself working
100% — not to finish migrating [[LiveScript]]'s own code onto it. Any fix that
requires changing LiveScript's code (not the proxy's) is deferred by default,
not urgent. **Confirmed as a general pattern 2026-08-19** by msilva during the
Linear backlog review of the "Proxy em produção validado c/ LiveScript"
project: most remaining LiveScript-side changes will be deferred under this
rule, not just the one case it was first stated for.

## Why

Luís, 2026-08-18 1:1: *"Teu primeiro trabalho é botar o proxy funcionando
100%. O teu trabalho não é botar o live script funcionando [...] considera
que o que tá sendo pelo SDK a gente não tá querendo olhar agora."*

LiveScript calls Airtable through **both** the `airtable` SDK and hand-built
REST calls. Getting a full migration onto everything the SDK does is a
LiveScript-side concern that can wait; validating the **proxy itself** is the
actual job. Partial migration is explicitly fine, not a failure state — *"a
gente não quer sair de zero para 100% [...] 30% só vai passar pela TI. É um
problema? Não [...] é uma necessidade identificada."*

## What this blocks

- **`PRO-76`** (propagar `app.route` nas métricas/spans do proxy) — needs
  LiveScript to **send** that attribute in the first place, which is a
  LiveScript-side code change. Recorded on the issue itself (comment,
  2026-08-19). Still open.
- ~~**`PRO-82`** (fluxo de onboarding e emissão de chaves por app) — same
  limit applies, per the same comment on `PRO-76`.~~ **Resolved 2026-08-20**:
  Luís settled the onboarding flow directly — no self-serve tooling now,
  worst case is direct config in the base, per [[2026-08-18 Bring options to
  Luís before deciding, communicate async and often]]. No longer blocked by
  this scope rule; see [[Airtable Proxy]] for the resolution.
- **`PRO-62`** (commit LiveScript's `feat/airtable-observability-local`
  branch so it talks to the proxy, plus confirming the REST-call path's
  base-URL source) — committed to by msilva in the 2026-08-24 1:1
  ([[2026-08-24 1-1 Matheus - Luís]]), **confirmed deferred 2026-08-26**: no
  LiveScript-side change made, not happening for now. Still Backlog, not
  canceled — this is the same scope line, not a change of plan.

## What's still open

- **Exact boundary of "mostly deferred."** Not every LiveScript-side change is
  necessarily out of scope forever — msilva plans to raise this directly with
  Luís rather than treat the boundary as fully settled from one comment and
  one backlog-review confirmation.
- Whether this scope line gets revisited once the proxy itself reaches the
  "Proxy em produção" / "Validado com o LiveScript" milestones, where some
  LiveScript-side integration (`PRO-96`) becomes unavoidable regardless.
