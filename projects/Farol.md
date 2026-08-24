---
type: project
status: active
updated: 2026-08-24
aliases: [Projeto Farol, farol]
tags: [farol, data, gcp, finance, bigquery, medallion]
---

# Farol

Not msilva's project. It has a page because it kept being referenced across five
meetings as a landmark, and because the wiki had **conflated its name with the AI
status readout** — see the correction below. Gabrielle described it directly in
[[2026-08-18 1-1 Matheus - Gabrielle]], which is the first coherent description on
record.

> [!warning] Name collision — resolved 2026-08-18
> [[AI status reporting on Linear]] was filed with *farol* as its alias, on the
> reading that the team called the weekly AI status readout "the farol". **That was
> wrong.** Re-reading the raw 2026-08-17 weekly, every occurrence of *farol* refers
> to this project — including the line about the readout showing *no deliveries*,
> which is *Farol's row in the readout*, not the readout naming itself.
>
> The correction matters because the two things are adjacent: the readout's most
> visible misreading was **about Farol**. Keeping the names distinct is what makes
> that sentence parse.

## What it is

**Farol is a Linear *initiative*, and the current build is its first project**
([[Linear Project Structure]]). Gabrielle, 2026-08-18:

> *"quando a gente vai também, por exemplo, no farol, que é o que o [Luís] tá tocando
> agora com a Yasmin, dentro dele a gente tem o primeiro projeto, que seria esse farol,
> que é o que a gente tá construindo agora, que é pegando […] as AP[I]s de cada
> plataforma, juntando tudo isso no banco. E a gente teria a V2, que é uma evolução dele
> […] pegando os dados da bronze, botando na ouro, na prata, botando ali uma camada
> conversacional de com IA."*

So V2 is **project two inside the initiative**, not a vague roadmap item. Concretely, per
[[2026-08-14 Recap da Semana]] and [[2026-08-17 Weekly - Projetos e Tarefas]]: it
pulls **corporate travel data** — **Uber, OnFly, Expresso** — for the finance team,
replacing manual spreadsheet work.

## Current state

- **In development**, with **Yasmin** collaborating; Luís on the infrastructure.
- In its **bronze** phase: land raw data, no transformation
  ([[2026-08-14 Recap da Semana]]).
- **GCP structuring is Luís's active task**, with the plan to run locally first so
  Yasmin can test before anything reaches production
  ([[2026-08-17 Weekly - Projetos e Tarefas]]). This was previously recorded as
  *blocked on a GCP project only Luís can create*; it is being worked, not waiting.
- One **Ultra Code** run took **Uber, OnFly and Expresso close to integrated** in a
  single unattended 4–5 hour pass. The leftovers were real inconsistencies in the
  vendors' APIs, bugs *"que estavam previstos"* — not agent errors
  ([[2026-08-17 Weekly - Projetos e Tarefas]]).

## The V2 plan

Quoted above. Two parts:

1. **Data layers — bronze / prata / ouro.** The medallion architecture. Bronze
   already exists as the current phase, so V2 is adding silver and gold on top of a
   landing zone that is already there.
2. **A conversational layer built on AI**, on top of the layered data.

> [!note] Why this is worth msilva's attention despite not being his
> Point 2 is **the area's second attempt at a user-facing AI system**, and the
> first one — the Linear status readout — failed twice in one sitting by reasoning
> past its evidence ([[AI status reporting on Linear]]).
>
> A conversational layer over gold-layer data is a much better-conditioned version
> of the same bet: the data is modelled before the model sees it, which is precisely
> the *"fetch only what you need, don't load the whole table"* lesson from
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] applied at the
> architecture level rather than the prompt level.
>
> It is also the closest thing in the area to an [[Agent Flow]] agent that someone
> other than msilva is actually going to build. *(unverified: no timeline for V2 was
> given, and no design document has been seen.)*

## Ownership is split by layer

Luís, 2026-08-17: *"a parte de dados pela gente, o restante todo por eles. A gente
só entrega dados para eles no final das contas."* — the data side is this team,
everything else is the finance team.

This settled a grey zone that [[2026-08-14 Papo de Projetos]] had left to the area
manager's discretion, and it does so with a third option the governance framing did
not anticipate: not "this team owns it" or "it transfers", but **split by layer**.

It also **contradicts** [[2026-08-14 Papo de Projetos]]'s record of Farol as *"being
built by the finance team themselves"*. Luís and Yasmin are visibly building it.
The contradiction is left standing rather than resolved, per the schema — the earlier
page reflects what was said then.

## Where it shows up elsewhere

- **The PR-review precedent.** Farol is where the area removed mandatory human PR
  review, with an AI reviewing in a fresh session instead —
  [[2026-08-14 No mandatory PR review while the proxy is pre-production]] cites it
  as the parallel change that justified the proxy's arrangement.
- **The Ultra Code evidence.** The strongest local data point that long unattended
  agent runs produce real work, used throughout
  [[Comparing the first-agent candidates]].
- **The status-readout failure case.** Reported as having *no deliveries* while Luís
  and Yasmin had *"andaram bastante"*, because it was all inside one parent issue.
  The cleanest demonstration of subtask blindness on record.

## An outside prioritization opinion (2026-08-24)

Carolina Bezerra, giving msilva a worked example of feeling-based
prioritization in [[2026-08-24 Agent Flow discovery with Carol]]: she does
not think Farol should have been a priority project. It arrived from
external demand pressure, never went through a prioritization queue, and
had already grown large by the time she noticed —
*"isso tá engasgado aqui na minha boca, na minha garganta"*. She rates it
low importance against everything else the team could otherwise build.
Recorded as her candid opinion, not a project-status change — Farol
remains `active`, in development, per the rest of this page.

## Open questions

- **What database?** BigQuery is the obvious guess given the GCP work, but it was
  never named. *(unverified.)*
- **Is V2 scheduled**, or is it a stated intention? No date was given.
- **Does the conversational layer have an owner or a design?** If it is unassigned,
  it is the most natural place for msilva's agent work to meet real demand — and
  nobody has connected the two.
- **What replaced the manual spreadsheets, and are they actually retired?** The
  value claim depends on it.
