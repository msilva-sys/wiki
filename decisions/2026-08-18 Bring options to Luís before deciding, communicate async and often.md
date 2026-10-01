---
type: decision
status: active
updated: 2026-10-01
date: 2026-08-18
aliases: [decision process norm, present options first]
tags: [process, communication]
---

# Bring options to Luís before deciding, communicate async and often

**Decided:** [[2026-08-18 1-1 Matheus - Luís]], as a request Luís made and msilva
agreed to, not an order (*"é um pedido, não é uma ordem"*).

## The rule

When msilva hits a real technical or architectural decision point, he brings Luís
**the options and a comparison**, not a conclusion already acted on. Luís
committed to the same in reverse — bringing msilva into decisions that affect his
work, the way he already does with Gabrielle (leaving her a comment from inside
Claude when something needs her call).

Alongside it, a lower-friction communication habit: don't save things for the next
scheduled 1:1. Send an audio note, a text, or a screenshot the moment something
worth surfacing happens.

## Why

**Trigger**: msilva had already implemented and shipped a fix for the LiveScript
Airtable-SDK header problem — a `pnpm patch` on the `airtable` SDK — without
bringing Luís the alternatives first. Luís's reaction on hearing the detail for
the first time: he doesn't trust the mechanism (*"eu confio zero nisso"*) and
wants to see it compared against other options before deciding whether it stays.
See [[How LiveScript sends the proxy X-App-Id header]] for the technical decision
itself, already shipped as commits `754896b` / `d565c26`.

Luís's framing: *"encontrou um problema, tem, eu tenho caminhos diferentes, cara.
Eu não tenho problema nenhum de eu te procurar e te apresentar para tomar a decisão
junto [...] então eu espero que você faça a mesma coisa comigo."* And on the cost
of not doing it: *"você tá desperdiçando informação sem necessidade."*

## How to apply

- Applies specifically to **technical/architectural decisions with real
  alternatives** — not every micro-choice, but anything where a different call
  would meaningfully change downstream work (the SDK patch is the paradigm case).
- Doesn't require waiting for a scheduled 1:1. The point is to raise it *when it
  happens*, async, in whatever form is fastest (audio/text/screenshot).
- Retroactive: this doesn't undo the SDK patch decision on its own, but it does
  mean that decision was reopened with alternatives on the table. Reopening it
  is in fact what led to it being **retired outright the next day**: see
  [[2026-08-19 Identify proxy apps by URL path, not header]], which moved app
  identification off headers entirely, mooting the header-vs-patch comparison
  this norm was meant to enable — see [[How LiveScript sends the proxy X-App-Id header]]
  for that history.
- Read together with Luís naming being in the office as underused leverage —
  *"o que tem de mais rico de estar no escritório é você tá com facilidade para
  acessar todo mundo"* — the norm is partly about using that proximity, not just
  about async messages.

## Applied since

- **`PRO-82` (onboarding/key-issuance flow), 2026-08-18.** 3 options posted as a
  Linear comment (manual env edit / self-serve script over env / defer until DB
  migration), msilva's lean stated but framed as Luís's call. **Resolved
  2026-08-20**: Luís picked the simplest path — no self-serve tooling now,
  worst case is direct config, and the real onboarding flow gets worked out
  once real apps outside the team start hitting the proxy. Matches msilva's
  stated lean (option 3).
- **`PRO-94` (Pulumi program language), 2026-08-19.** Same pattern: 3 options
  (Go / TypeScript / Python) posted as a Linear comment, msilva's lean (Go, for
  toolchain/CI consistency with the proxy) stated but left as Luís's call.
  **Resolved 2026-08-20**: Luís aligned on Go. Matches msilva's stated lean.

## Violated, 2026-10-01 — three concrete instances in one 1:1

[[2026-10-01 1-1 Matheus - Luís]]: Luís opens with direct feedback that the
norm wasn't followed, three times:

- **Intake resumed without telling anyone**, despite an earlier agreement to
  pause it while waiting on Gabrielle's A10/A14 feedback. msilva's own
  account: he was partly idle, picked up a side request from Carolina (an
  "agente priorizador" prototype) and kept going on intake rather than
  flagging he had capacity free.
- **Cloudflare Workers chosen over Next.js**, with a full technical
  justification behind it — but decided and acted on before telling Luís,
  not brought as options first.
- **Supabase adopted** believing it was already-approved team stack — Luís
  calls this "ruído de comunicação" and says there was a prior decision
  *against* using Supabase that msilva wasn't aware of or didn't recall.
  **That prior decision has no record in this wiki** — open question on the
  meeting page, unconfirmed.

**New corollary stated explicitly this time**: the team is a **technical
team, not a product team** — the stack should stay simple and standardized.
GoLang in the proxy remains the one justified exception (agreed jointly,
pre-existing). Any other deviation needs a comparison of options brought to
Luís *before* acting, not after — this is the same rule as above, just with
a concrete criterion for what counts as "a real decision point" in stack
choices specifically. Luís's own illustration: a past (pre-msilva) rushed
decision to migrate to Deno had to be reverted because it didn't fit the
team's context.

**Pattern across all three**: msilva moved from "communicate before a
decision with real alternatives" to just not communicating at all when he
judged himself capable of deciding alone. Luís's suggested fix is a
standing phrase rather than a judgment call each time: *"vou fazer isso
aqui, beleza? Se não tiver nenhuma outra coisa, se você não tiver tempo pra
me ajudar, posso avançar?"* — stating the intended action and leaving the
other person the opening to object, instead of silently assuming they
wouldn't have time to weigh in.
