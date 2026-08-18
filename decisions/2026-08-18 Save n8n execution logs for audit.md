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

**Decision.** Turn on execution saving for **production runs**, so that what causes
errors and unexpected behaviour can be audited after the fact (Gabrielle,
[[2026-08-18 1-1 Matheus - Gabrielle]]). Path: a flow's **three-dots menu →
settings**.

Already applied to at least one flow during the call — *"agora tá salvando os dois
porque a gente mudou."* Owner of the remaining `RR*` flows: **msilva**.

> [!important] Corrected 2026-08-18 — the defaults are narrower than first recorded
> This page first read *"both failed and production runs"* and *"execution history was
> off by default"*, from Gemini's summary. The transcript is precise:
>
> *"Quando a gente cria um fluxo aqui, ele tem por default […] não salvar as execuções.
> Eh, as falhadas ele tem por default, sim, e as que ocorrem em produção não. Então
> vocês tem que vir aqui e botar isso."*
>
> **Failed executions are saved by default. Successful production ones are not.** So
> the gap was never "no logging" — it was **no logging of the runs that succeeded**,
> which is exactly the blind spot that matters here.
>
> It also explains the symptom that baffled both of them: *"ele rodou lá o fluxo […]
> tava no execução[,] tava running, aí quando terminava sumia."* A run that disappears
> **on completion** is precisely what this default produces. They were watching the
> setting work as designed and reading it as a bug.

## What it fixes

[[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] recorded the same
blocker twice, from a debugging session that could not proceed:

- *"No execution logs. The first workflow wasn't logging at all."*
- *"Execution traceability is still the blocker"* — executions previously appeared
  and Gabriel could catch errors from them; then they stopped reliably showing.

That page treated this as a symptom of a shared instance and a stuck lock.
Gabrielle's diagnosis is more mundane and more actionable: **successful production
executions were not being saved.** A setting, not a licence problem.

That also resolves the "they used to appear and then stopped" puzzle. Failing runs were
always retained; **the flow started succeeding**, and success is what the default
discards. Nothing changed about the instance — the behaviour changed because the
outcome did.

## Why it matters more than a logging toggle

The open question in that session was whether the flow's cost — **$7 in a day
against 11 centavos on a clean run** — comes from wholesale context loading or from
the flow **entering a loop**. Two workflows were observed running concurrently when
one should have triggered the next, with `simplificado` the suspect.

That comparison has since been **retracted as unclean**: Gabriel switched LLM provider
between the two runs, so the cost delta cannot be attributed to run-to-run variance
(see the retraction on the CazéTV page). **This makes execution logs more important,
not less** — the loop hypothesis now has two people believing it and no clean
measurement supporting it. Gabrielle reached it independently: *"Eu não achei que […]
justificava[m] 7. A minha ideia era algum loop […] só que aí como eu não tinha esses
lo[g]s de execução, fiquei perdido."*

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

Logging into n8n with the **`conta tech`** shows every flow in the company —
*"quando a gente loga com a conta tec, a gente consegue ver o de todos"* — including
**per-execution token counts, input and output**. The limitation:

> *"a gente vê todos os fluxos […] Só que um problema[:] a gente não consegue tipo
> filtrar por usuário. Por exemplo, o Gabriel ele usa o financeiro, mas eu não consigo
> escrever financeiro e filtrar. Então tem que pesquisar pelo nome ou você pode ir
> rolando aqui […] ele vem por atualização."*

Sorted by last update, searchable by name only. That is also the mechanism behind the
earlier symptom of Gabriel's runs being *"pushed down the list"* — there is no
per-owner view, only recency.

Note this is a **different `conta tech`** from the Claude plan discussion earlier in
the same meeting — see the confidence note below.

## Confidence

> [!success] Confirmed n8n — transcript read 2026-08-18
> The first version of this page inferred the runtime from context, because Gemini's
> summary said only *"as ferramentas"*. The transcript removes the doubt: Gabrielle
> walks the n8n UI on screen (three dots → settings), names the two switches, changes
> one live, and the surrounding discussion is entirely about Gabriel's `RR*` flows.
>
> **The separate "Tech vs Humans" ambiguity also resolves — as *both*, about different
> tools.** The token allowance / upgrade discussion is **Claude plans**: *"ele não tem
> aquelas sessões de horas e de semanas […] tem o pro e tem o de 100, enfim, que você
> vai fazendo [up]grade"* — session-based limits, Pro and Max tiers. The
> all-company-visibility discussion is the **n8n login**. Two accounts named the same
> way in two tools, which is what made the summary unreadable. See
> [[2026-08-18 1-1 Matheus - Gabrielle]].

## Open questions

- **Does msilva have admin on the shared n8n instance** to change the setting on flows
  he doesn't own? The licence belongs to finance, and the remaining `RR*` flows are
  Gabriel's.
- **Retention.** Saving all production executions on a shared instance has a cost
  and a data-sensitivity dimension — these flows carry CazéTV revenue figures.
  Nobody raised it.
- ~~**Was it already off, or did it get turned off?**~~ **Answered** — neither. The
  setting never changed; the flow started **succeeding**, and success is what the
  default discards. See above.
- **Retention and sensitivity.** Saving all successful production executions on an
  instance shared with finance means retaining payloads that carry CazéTV revenue
  figures. Nobody raised it, and it is the one argument *against* this decision.
