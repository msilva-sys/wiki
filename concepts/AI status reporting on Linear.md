---
type: concept
status: active
updated: 2026-08-18
aliases: [farol, o farol, the farol, status readout, AI status agent]
tags: [linear, agents, process, observability, metrics]
---

# AI status reporting on Linear

An AI reads the area's Linear board and produces a per-project status readout —
progress percentages, pace classification, and written insight — which the team
walks through at the top of [[2026-08-17 Weekly - Projetos e Tarefas]]. Referred
to as the **farol** (beacon/dashboard light).

This page exists because it is **the only agent-like system actually running in
msilva's area**, and therefore the only source of empirical evidence about
[[Agent Flow]] that isn't a design document. Everything else — the 14-agent
architecture, the build strategy, the candidate comparison — is on paper.

> [!warning] What this page does not know
> Who built it, what it runs on, whether it is Linear's own feature or something
> the team wired up, what prompt or data it gets, and whether it runs on a
> schedule. None of this was said. It is *used* in the meeting, not explained.
> **Ask Luís or Gabrielle** — see the open questions below. Every claim here is
> inferred from how the team talked about its output.

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
measured misreadings in one readout:

| Project | Reported | Actual, per the owner |
|---|---|---|
| Farol | *no deliveries* | Luís and Yasmin *"andaram bastante"*, all within one parent |
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
them. The first is deterministic and fixable by summing differently. The second is
the agent reasoning past its evidence — the failure mode no amount of data
plumbing removes.

## What this implies for Agent Flow

**A1 and A2 would inherit the subtask blindness directly.** Both read Linear
([[Agents read primary sources]]), and the recommended first build is the
[[Comparing the first-agent candidates|intake pair A1 + A2]]. Anything computing
state or progress from that board hits the same parent/subtask ambiguity. This is
a concrete, already-observed design constraint on the first agent, not a
hypothetical — and it was found for free by watching an existing system fail.

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
prior attempt as having *failed on adoption, not capability*. The farol is in
active weekly use despite being demonstrably wrong twice in one sitting, which is
a meaningful counter-signal about what adoption actually requires: being in the
meeting where the work is discussed.

## Practical consequence for msilva, now

He is about to **restructure the proxy issues in Linear**
([[2026-08-14 Migrate project management from Jira to Linear]]). The farol will
report on that structure to Gabrielle and Luís, and he is the person with the
least established track record — the one who can least afford a 6%-shaped
readout while the deadline conversation is happening.

**Flat issues are legible to the farol; deep subtask trees are invisible to it.**
Luís's `Triagem`-column convention from the same meeting is the complementary
half: it gives found-but-not-yet-prioritized work somewhere visible to live,
instead of buried as subtasks.

## Open questions

- **What is it, mechanically?** Linear's own feature, a team-built agent, or a
  vendor tool. Determines whether the subtask summation is fixable at all.
- **Does it read anything beyond Linear?** The context it lacked on Orca exists in
  the Hub and in `Brain` — if it reads only the board, that failure was
  structural and predictable.
- **Is the subtask defect known and being fixed**, or was 2026-08-17 the first
  time anyone said it out loud? Luís opened the meeting saying the team may need
  *"começar a organizar de uma forma diferente"*, which reads like the team
  adapting to the tool rather than the reverse.
- **Would it be a candidate for msilva to fix or replace?** It is a working
  agent with a known defect, a real audience, and a weekly slot — an unusually
  well-defined target. Nobody proposed this; it is this page's suggestion, and
  it belongs on the list for
  [[What should the Agent Flow research phase study]].
