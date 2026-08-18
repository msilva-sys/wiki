---
type: decision
status: active
updated: 2026-08-18
date: 2026-08-18
decided_by: Gabrielle Ferreira
source: "[[2026-08-18 1-1 Matheus - Gabrielle]]"
tags: [n8n, observability, tokens, audit, agents]
---

# Save n8n execution logs for audit

**Decision.** Turn on execution saving for **both failed and production runs**, so
that what causes errors and unexpected behaviour can be audited after the fact
(Gabrielle, [[2026-08-18 1-1 Matheus - Gabrielle]]).

Owner of the configuration change: **msilva**.

## What it fixes

[[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] recorded the same
blocker twice, from a debugging session that could not proceed:

- *"No execution logs. The first workflow wasn't logging at all."*
- *"Execution traceability is still the blocker"* — executions previously appeared
  and Gabriel could catch errors from them; then they stopped reliably showing.

That page treated this as a symptom of a shared instance and a stuck lock.
Gabrielle's diagnosis is more mundane and more actionable: **execution logs were
not being saved by default.** It was a setting, not a licence problem.

## Why it matters more than a logging toggle

The open question in that session was whether the flow's cost — **$7 in a day
against 11 centavos on a clean run** — comes from wholesale context loading or from
the flow **entering a loop**. Two workflows were observed running concurrently when
one should have triggered the next, with `simplificado` the suspect.

**Neither hypothesis is testable without execution history.** A cost spike that
happens intermittently can only be diagnosed from the run that spiked, and that run
is over. Saving executions is the precondition for the whole diagnosis, and it is
also why this is worth a decision page rather than a line in a meeting note:

- msilva has an open action to **review the `RR*` automations for loops**. This
  unblocks it.
- Saving *production* runs and not only failures is the load-bearing part. The
  runaway runs **succeeded** — they produced a wrong number expensively. A
  failures-only setting would have missed every one of them.

## Bearing on [[Agent Flow]]

n8n is the de facto runtime for the area's existing automations, on a licence shared
with finance. [[Agent Flow]] already records the shared instance as a real
constraint — no isolation, and one person's executions crowd another's out of view.

This decision establishes that **execution history is a deliberate setting in that
environment, and the default is off.** For any agent built under this programme,
that generalizes:

- **Observability is opt-in on the runtime, not just in the code.** The
  [[Airtable Proxy]] is observability-first by design; the platform the agents would
  actually run on is not.
- **A5 Watcher's cost-control thinking depends on measurement that must be switched
  on first** — see [[How to implement A5 Watcher]], where throttling is treated as
  the cost lever. You cannot throttle what you cannot see.

## The instrument that pairs with it

The **"Tech" account can see every flow in the company**, though per-user filtering
is limited enough that finding one person's runs means manually scanning update
history. Together with the token-consumption dashboard Gabriel can request
([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]), that is the
measurement surface for the token question.

## Confidence

> [!warning] The runtime is inferred, not stated
> Gemini's notes say *"ajustar as configurações das ferramentas para salvar as
> execuções"* — **"the tools"**, plural and unnamed. The identification as **n8n**
> comes from context: the decision is framed as fixing *Gabriel's budget and flow
> execution problems*, and Gabriel's flows are n8n
> ([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]). n8n also has
> exactly this setting, with exactly these two switches (save failed / save
> successful production executions), defaulting the way described.
>
> **What would disconfirm it:** if the intended tool is Claude Code session logging
> or the "Tech"/"Humans" account console instead. One question to Gabrielle settles
> it, and the same question settles the account ambiguity on
> [[2026-08-18 1-1 Matheus - Gabrielle]].

## Open questions

- **Does msilva have admin on the shared n8n instance** to change an
  instance-level setting? The licence belongs to finance.
- **Retention.** Saving all production executions on a shared instance has a cost
  and a data-sensitivity dimension — these flows carry CazéTV revenue figures.
  Nobody raised it.
- **Was it already off, or did it get turned off?** Gabriel said executions used to
  appear and then stopped. "Not saved by default" does not explain a change in
  behaviour.
