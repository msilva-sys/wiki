---
type: concept
status: active
updated: 2026-08-18
aliases: [status readout, AI status agent, Linear status readout]
tags: [linear, agents, process, observability, metrics]
---

# AI status reporting on Linear

An AI reads the area's Linear board and produces a per-project status readout —
progress percentages, pace classification, and written insight — which the team
walks through at the top of [[2026-08-17 Weekly - Projetos e Tarefas]].

> [!warning] Correction 2026-08-18 — this system is **not** called *"o farol"*
> This page was originally filed with *farol* as its alias, on the reading that the
> team nicknamed the readout "the beacon". **That was wrong.** Re-reading the raw
> 2026-08-17 weekly, every occurrence of *farol* is the **project** [[Farol]] — the
> travel-data consolidation Luís and Yasmin are building. Gabrielle described that
> project directly in [[2026-08-18 1-1 Matheus - Gabrielle]], which is what surfaced
> the error.
>
> The confusion was close to designed: the readout's most visible misreading was
> *about* Farol, so "the farol reported no deliveries" parses either way. It means
> **Farol's row in the readout**, not the readout naming itself.
>
> **The closest thing to a name is Gabrielle's** *"nosso repórter"* — our reporter,
> 2026-08-18. Nobody has given it a proper one.

This page exists because it was believed to be **the only agent-like system actually
running in msilva's area**, and therefore the only source of empirical evidence about
[[Agent Flow]] that isn't a design document.

> [!warning] Corrected 2026-08-18 — it is not the only one
> [[2026-08-18 1-1 Matheus - Gabrielle]]: **Claude already writes the area's Linear
> backlogs.** Gabrielle hands it documentation and PRDs and it creates the project
> description, the milestones, and the issues inside them, using Luís's templates —
> *"ele cria as tarefas sozinho."* Linear also has a native description-improving
> agent, and Luís authors issue context through Claude.
>
> That is a second running agent-like system, and a **better-behaved** one: daily use
> by the manager, no disputes recorded. This page's evidence is still the most
> *instructive* — a system failing in public teaches more than one that works — but the
> "only agent running here" framing was an artifact of nobody having described routine
> practice. See [[Agent Flow]].

> [!success] Mechanism found 2026-08-18 — and the subtask blindness is deliberate
> Gabrielle walked the tool on screen in [[2026-08-18 1-1 Matheus - Gabrielle]]. It is
> **an in-house board Gabrielle controls**, not a Linear feature and not a vendor tool.
> She calls it *"nosso repórter"* — the closest thing to a name anyone has used.
>
> - It **pulls from Linear**: *"Ele puxa todos os projetos que estão classificados como
>   em andamento […] ele puxa aqui todos os milst[ones] que tem lá no liner e as
>   [tasks] dentro dele."*
> - Per project you choose whether to source the backlog from Linear, *"porque nem
>   todos os projetos estão no liner ainda"* — so its inputs are heterogeneous
>   mid-migration.
> - **The subtask blindness is a design choice, not a bug**: *"ele traz a nível de
>   [issue], ele não traz a nível de sub[issue], que é para poder também não ficar tão
>   confuso aqui. A gente precisa só ideia geral […] visibilidade do todo."*
> - **Drill-down is already planned**: *"Eu vou depois liberar aqui também para
>   conseguir clicar e abrir sub[issues]."*
> - It appears to read **Airtable** — *"no final ele tá puxando do Air Table, então só
>   você botar API do Air Table ou pelo MCP do próprio [Claude]"* — which would make it
>   an [[Airtable Proxy]] candidate. *(unverified: one clause in a garbled passage.)*
>
> **This reframes the mechanical failure below.** It is not an agent that cannot sum
> subtasks; it is a board deliberately aggregating one level up to cut noise, whose
> owner intends to add the missing level. The flat-issues advice still holds — but as
> adapting to a temporary setting, not to a broken system.
>
> Still unknown: what generates the *written insight* and pace classification, whether
> that part is scheduled, and whether it reads anything beyond the board.

## It got real things right

Four findings the team accepted, at least partly:

- **Large-scope projects advance slowly at the start**, with the early effort
  going into documentation. Gabrielle called this *"realmente um padrão"* — a real
  pattern, and one she recognized across projects rather than in one.
- **A significant share of team effort goes to correcting or redoing earlier
  deliveries** rather than creating new value. Gabrielle pushed back on the
  framing for Orca but conceded it for Fronte: *"o fronte realmente […] voltado a
  corrigir o que a gente experimentou de errado."*
- **Much of the portfolio has no delivery date**, weakening planning.
- **Hidden delay accumulates in projects with no rigorous pace tracking.** The
  sharpest observation in the readout: projects classified as *slow* have **no
  formally late tasks**, because elapsed time doesn't correspond to progress — so
  conventional delay metrics never fire. Gabrielle's own summary: *"a gente não
  tá conseguindo quantificar o atraso realmente."*

That fourth point is a genuine analytical finding. It describes a class of failure
the board structurally cannot show, which is a harder thing to surface than a
late-task list.

## It got real things wrong, in two distinct ways

**1. Subtask blindness — a mechanical defect.** It treats a parent issue as
undelivered until the whole thing is done, and ignores progress on subtasks. Two
measured misreadings in one readout.

*Reclassified 2026-08-18: **not a defect, a deliberate aggregation level** — see the
mechanism callout above. The misreadings below are real; the cause is a design
trade-off (clarity over completeness) that its owner plans to revisit, not a summation
bug.*

| Project | Reported | Actual, per the owner |
|---|---|---|
| [[Farol]] | *no deliveries* | Luís and Yasmin *"andaram bastante"*, all within one parent |
| Fronte de Negócios | **6%** | *"por volta de 20, 25%"* |

Gabrielle: *"ele tá considerando a entrega da tarefa como um todo […] e
desconsiderando o andamento que a gente faz dentro daquela tarefa, das
subtarefas."* Luís: *"esse 6% assusta mais do que a realidade."*

Note the causal direction. The team's own habit of opening one issue with many
subtasks is what produces the misreading — *"muitas tarefas ele tá abrindo como
uma tarefa com subitens dentro dela."* The agent is not wrong about the data; it
is wrong about what the data means, because the team's convention and the agent's
model of progress disagree. **Fixing this is a convention change as much as a code
change.**

**2. Missing context — a judgment defect.** It reported that a meaningful share of
effort goes to fixing prior work, and named Orca as an instance. Gabrielle
rejected that outright: *"acho que ele não tem o contexto, né?"* Orca is new
capability, not remediation. The readout had the ticket data and drew a
conclusion the ticket data cannot support.

These are different failures with different fixes, and it matters not to conflate
them. ~~The first is deterministic and fixable by summing differently.~~ **Restated
2026-08-18**: the first is not a summation bug at all — it is a **deliberate
aggregation level with drill-down already planned** by the tool's owner. So it is
"fixable" in the weakest sense: it will be fixed, on someone else's schedule, and it
was never wrong. The second is the agent reasoning past its evidence — the failure
mode no amount of data plumbing removes.

**The asymmetry is the point.** One of these two failures has an owner, a cause and a
plan. The other has none of those, and nobody in the meeting treated it as a problem
worth solving — Gabrielle rejected the Orca conclusion and the group moved on. **For
anything built under [[Agent Flow]], the second class is the real design burden**, and
it is the one this meeting produced no mechanism for.

## What this implies for Agent Flow

**A1 and A2 inherit the parent/subissue ambiguity, but not the blindness.** Both read
Linear ([[Agents read primary sources]]), and the recommended first build is the
[[Comparing the first-agent candidates|intake pair A1 + A2]]. Anything computing state
or progress from that board has to decide what a partially-done parent means — that
ambiguity is in the data and it is real.

*Refined 2026-08-18:* what A1 and A2 do **not** inherit is the *choice* made here. The
board aggregates at issue level to reduce noise for a human audience skimming a weekly
readout. An intake agent has a different audience and a different question, so it should
make its own call rather than copy this one. **The transferable lesson is that the choice
exists and is consequential** — worth 6% against 20–25% when made wrong for the purpose —
not that issue-level is correct.

**And A2's output side is already solved here**, which is the more actionable finding:
Claude writes this area's Linear backlogs from documentation
([[Linear Project Structure]]). See [[Agent Flow]].

**Humans-as-approvers is validated, and so is the failure it catches.** The
architecture's premise is that *"humanos são aprovadores estratégicos e
refinadores de qualidade"* ([[Fluxo Agêntico project instruction]]). This meeting
is that premise working: the readout was wrong twice, a human caught both in real
time, and the output was corrected before it drove any decision. It also shows the
cost — the correction consumed a chunk of the meeting, and Gabrielle still left
saying she lacked visibility.

**Confident wrong numbers are worse than no numbers.** A 6% figure produced a
stated near-panic reaction (*"eu vou entrar em pânico"*) against a reality of
20–25%. The readout's presentation carried no uncertainty. For any agent reporting
status to people who make decisions on it, **the confidence of the output is part
of the design**, not a presentation detail. This sharpens the throttling and
noise-control thinking in [[How to implement A5 Watcher]]: a monitoring agent that
cries wolf loses its audience, and a reporting agent that states a wrong number
flatly does the same damage faster.

**It is evidence the area adopts this kind of thing.** [[Agent Flow]] records the
prior attempt as having *failed on adoption, not capability*. The readout is in
active weekly use despite being demonstrably wrong twice in one sitting, which is
a meaningful counter-signal about what adoption actually requires: being in the
meeting where the work is discussed.

## Practical consequence for msilva, now

He is about to **restructure the proxy issues in Linear**
([[2026-08-14 Migrate project management from Jira to Linear]]). The readout will
report on that structure to Gabrielle and Luís, and he is the person with the
least established track record — the one who can least afford a 6%-shaped
readout while the deadline conversation is happening. Gabrielle is on leave from
**2026-08-24**, so for ~2.5 weeks this readout *is* his visible progress.

**Flat issues are legible to the readout; deep subtask trees are invisible to it.**
Luís's `Triagem`-column convention from the same meeting is the complementary
half: it gives found-but-not-yet-prioritized work somewhere visible to live,
instead of buried as subtasks.

## The team's response: reshape the board, not the agent — 2026-08-18

[[2026-08-18 1-1 Matheus - Gabrielle]] answers what the team actually did about the
misreadings, and it is not what this page assumed.

They made **two** structural fixes, both to the board:

1. **Split the project.** Everything in the backlog that was *evolution* rather than
   *stabilization* sat in the stabilization project's milestone, dragging the
   percentage down. They created a separate front-end evolution project and moved it
   all across — *"isso já dá um aspecto mais real […] [da] porcentagem de conclusão."*
2. **Break up the single milestone.** All stabilization tasks were in **one**
   milestone, so **Carol** could not see where the work stood. Regrouped 2026-08-17;
   now *"a gente já consegue saber melhor o que realmente tá travado, [o] que a gente
   já andou."*

Nobody proposed changing the readout. Fix 2 is the one with the sharper lesson:
milestone *granularity* is what makes progress legible, and it is the level the board
does count.

That is a coherent choice rather than a capitulation. One project mixing new
front-end work with remediation produces a percentage describing neither, and the
readout was right that the number was low — it was wrong about what the number
meant. Separating the two makes the arithmetic and the reality converge from the
board's side.

**But note which failure it fixes.** Splitting projects addresses the *category*
confusion. It does nothing about subtask blindness — a deep subtask tree is equally
invisible in a well-named project. And it does nothing about the second failure
class, reasoning past the evidence. **Two of the three problems are untouched**, and
the one being worked is the one the team can fix without owning the agent.

So the pattern is: **the board is treated as the adjustable component, and the
aggregation level as fixed.** Not because nobody can change it — Gabrielle owns the
tool and intends to add subissue drill-down — but because reshaping the board fixed
the immediate misreading and the drill-down is a later improvement. For msilva the
operative fact is unchanged: see [[Linear Project Structure]], where the board
conventions are collected.

**And he is invited to change them.** Gabrielle, on the method as a whole: *"a gente
tá muito nesse processo de entender o melhor fluxo […] não tem muito também um certo
nem um errado […] pode ficar à vontade[,] [se] ter algum insi[ght], alguma maneira que
você tá achando que não tá funcionando[,] de pegar e de mudar."* The conventions are
provisional by the manager's own account.

## Open questions

- ~~**What is it, mechanically?**~~ **Answered 2026-08-18** — an in-house board
  Gabrielle controls, pulling from Linear at issue level by design. See the callout at
  the top.
- ~~**Is the subtask defect known and being fixed?**~~ **Answered** — it is a
  deliberate aggregation level, and drill-down is planned. Not a defect anyone is
  hunting.
- **Does it read anything beyond Linear?** Still open, and now sharper: it appears to
  read **Airtable** too, per one garbled clause. The context it lacked on Orca exists
  in the Hub and in `Brain`. Confirm the Airtable dependency first — if real, the board
  that watches msilva's project is itself a candidate to sit **behind** it
  ([[Airtable Proxy]]).
- **What produces the written insight and the pace classification?** The aggregation is
  explained; the *analysis* is not. This is the part that got Orca wrong, and it is the
  only part that is actually agent-like.
- **Would it be a candidate for msilva to fix or replace?** Weaker now than when this
  page proposed it. The mechanical half has an owner with a plan, so the remaining
  target is the judgment half — smaller, and harder to specify. Still worth raising,
  but no longer the unusually well-defined build it looked like. On the list at
  [[What should the Agent Flow research phase study]].
