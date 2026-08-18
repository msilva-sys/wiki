---
type: meeting
status: active
updated: 2026-08-18
date: 2026-08-17
attendees: [Gabrielle Ferreira, Luis Fernandez, João Victor Andrade, Yasmin Macedo, Arthur Tavares, Matheus Silva]
source: "raw/Weekly - Projetos e Tarefas  - 2026_08_17 14_03 GMT-03_00 - Anotações do Gemini.txt"
transcription_confidence: low
tags: [weekly, process, linear, proxy, agents, pulse, farol, orca, matriz]
aliases: [Weekly de Projetos, Weekly 2026-08-17]
---

# Weekly - Projetos e Tarefas (2026-08-17)

Monday-afternoon team meeting, 38 minutes. Two halves: **projects** walked against
an AI-generated status readout in Linear, then **tasks per person**.

> [!important] A meeting type the wiki didn't know about
> The rhythm table in [[2026-08-14 Recap da Semana]] lists Recap da Semana (Fri),
> Papo de Projetos, and the weekly 1:1. **This is a fourth**, and it overlaps Recap
> da Semana heavily — same per-person walkthrough, three days later. Whether both
> persist is unknown; that table has been corrected to include this one.

> [!warning] Transcription quality
> Gemini labelled only three speakers — `Gabrielle Ferreira`, `Luis Fernandez`,
> `João Victor Andrade` — and collapsed **Yasmin, Arthur, Carolina and msilva all
> into the Gabrielle label**. Attribution below is inferred from content and is
> marked `(speaker inferred)` where it carries weight. Where a claim's owner can't
> be established, it says so rather than guessing.

> [!note] Scope
> Most of this meeting is other people's project status. Per msilva's standing
> instruction, what bears on his own work is recorded in full and the rest is kept
> as named landmarks so it stays recognizable later. Personnel and vendor-pricing
> chatter stays in the raw file.

> [!info] Same-day context
> Opens with *"o Gabriel vem hoje […] foi aniversário dele"*. This is the meeting
> **one hour before**
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]].

## Decisions

Genuinely concluded here — most of the meeting is status, not decisions.

- **The Jira board is emptied into Linear, not abandoned in place.** Luís: *"graças
  a Deus, a gente caiu no Linear. Então agora a gente tira tudo de lá."* Closes an
  open question on
  [[2026-08-14 Migrate project management from Jira to Linear]].
- **DOR was stood down for the Fronte de Negócios 2.0 workshop**, even though DOR
  offered to come without approval in hand. Deliberate, and taken jointly — see
  [[Pulse]].
- **Approval escalates a level** — the project goes through a second, more senior
  approval round before reaching Sérgio, rather than proceeding on the sign-offs
  already in hand.
- **Farol's ownership is split.** Luís, answering a direct question: *"a parte de
  dados pela gente, o restante todo por eles. A gente só entrega dados para eles no
  final das contas."*

## Action items

- [ ] **Check your own entry in the internal Hub and report corrections — by
      2026-08-18.** Owner: msilva (asked of everyone). Explicitly time-boxed to
      *"entre hoje e amanhã"* because Gabrielle leaves after that. **Today is the
      deadline.** The Hub was broken only because a deploy had been forgotten; it
      works again. Arthur was excused. Someone reported theirs was empty; the link
      is in the projects group.
- [ ] **Migrate his Jira items to Linear.** Owner: msilva — he restated it himself
      as part of integrating with the team. Also tracked on
      [[2026-08-14 Migrate project management from Jira to Linear]].
- [ ] **Decide whether the Fronte automated test suite goes live this week** — a
      suite *isolated from Airtable*. Owner: Luís, with Gabrielle.
- [ ] **Present Ultra Code to the team** and agree when it is the right mode of
      work. Owner: Luís, no date.
- [ ] **Send the Airtable documentation standard** (tables, fields, meaning, owner,
      who fills) so the CRM map follows the same pattern. Owner: Gabrielle, to João
      Victor.
- [ ] **Formalize TES feedback to the vendor** — targeted for the week of
      2026-08-24. Owner: Gabrielle (speaker inferred).
- [ ] **Re-present Fronte de Negócios 2.0** to Cristian, Ribon and Zoca in the week
      of 2026-08-24.

## msilva's own status, as he gave it

His proxy progress on the record in his own words for the first time since
[[2026-08-14 Recap da Semana]]:

- **Both services running locally and talking to each other** — *"já consegui rodar
  tanto o [proxy] quanto o live script na minha máquina. Já fiz a integração entre
  os dois."*
- **The metrics named in the ticket description are being captured** — he ties this
  back to the issue text, i.e. the [[AIRTABLEGC-34]] metric list.
- **Current work is proxy authentication** — *"saber de quem tá se comunicando com o
  proxy, pra gente conseguir ter o controle melhor de métricas, observabilidade e
  tal."* Independent confirmation of the app-auth work on [[Airtable Proxy]] and of
  the client side in [[How LiveScript sends the proxy X-App-Id header]].
- **[[Agent Flow]] got no parallel time last week** — *"eu ainda não consegui […]
  tocar em paralelo a questão dos agentes, do agente autônomo […] a semana passada
  eu tava muito me adaptando a tudo."* He expects this week to be better.

Worth keeping honest about that last point: the parallel-track split in
[[2026-08-10 Onboarding runs proxy and agent flow in parallel]] was only enacted
from 2026-08-17 by [[2026-08-14 1-1 Matheus - Gabrielle]], so week one of the split
had not begun when he said it. The agent work so far is the research recorded in
this vault, not anything built.

## Why the proxy issues were in Jira at all

Luís explains it, and it is not a leftover — it was deliberate tool-trialling:

- He was *"o rebelde contra o Arthur"*, testing the options: *"tentei no Linear,
  tentei no Jira, tentei em qualquer [uma]."*
- So the proxy was **one of the projects deliberately not migrated**, and when
  msilva arrived he *"acabou pegando as tarefas"* — inheriting them in Jira.
- Resolution: everything comes out of Jira into Linear.

This makes the legacy status of [[AIRTABLEGC-34]] and the `AIRTABLEGC` board
unambiguous — it was a trial, it lost, and it gets drained.

## Linear: two things that change how msilva should structure his issues

**1. The rollup is subtask-blind.** The status generator treats a parent issue as
undelivered until the whole thing is done, and **ignores subtask progress**
entirely. Two measured instances:

| Project | Reported | Actual, per the owner |
|---|---|---|
| Farol | *no deliveries* | Luís and Yasmin *"andaram bastante"* — all inside one parent |
| Fronte de Negócios | **6%** | *"por volta de 20, 25%"* |

Gabrielle: *"ele tá considerando a entrega da tarefa como um todo […] e
desconsiderando o andamento que a gente faz dentro daquela tarefa, das
subtarefas."* Luís: *"esse 6% assusta mais do que a realidade."*

> [!important] Directly actionable for msilva
> He is about to **restructure the proxy issues in Linear** as he sees fit
> ([[2026-08-14 Migrate project management from Jira to Linear]]). How he nests them
> decides whether his progress is visible to Gabrielle and Luís at all — and he is
> the person with the least established track record and the most to lose from a
> 6%-shaped readout. Flat issues are legible; deep subtask trees are invisible.

**2. The `Triagem` column is now an async review handoff.** Luís's convention,
offered to the team as a tip: he parks problems he finds in Triagem so Gabrielle can
review them before they enter the board as real priorities. Worth adopting rather
than inventing a parallel mechanism.

## The AI status readout — an in-house precedent for an agent

Filed as its own page: **[[AI status reporting on Linear]]**. In brief, the readout
produced four real insights and two real failure modes at once, and Gabrielle argued
with it out loud. It is the closest thing at Livemode to a running [[Agent Flow]]
agent, and the first evidence about that architecture that comes from a system
rather than a design document.

## Ultra Code — Luís ran an unattended multi-hour agent workflow

Luís tested Claude Code's **ultracode** on Farol and wants to present it:

- *"ela abre um processo de trabalho muito maior do que só abrir subagentes. Ele
  cria um workflow de trabalho, ele organiza todo o workflow […] para fazer as
  coisas em paralelo e fica rodando, sei lá, 4, 5 horas fazendo tudo que tem que ser
  feito."*
- Intended only as a test; the **result was Uber, OnFly and Expresso close to
  integrated** in one run. The leftovers were bugs *"que estavam previstos"* — real
  inconsistencies found in the vendors' APIs, not agent errors.
- Yasmin picks it up from there to test and finish the integrations.
- It recovered time lost the previous week.
- Open, in his words: *"em que momento que a gente usa isso e em que momento que a
  gente trabalha do jeito que a gente vem trabalhando."*

Bears on [[Agent Flow]] two ways: it is a working demonstration of the AI-First
premise (*"a IA é o meio de execução"*) inside this team, by the tech lead — and a
4–5 hour unattended run is exactly the shape of the **token-cost constraint** in
[[2026-08-14 1-1 Matheus - Gabrielle]], the $7/day figure that made cost an explicit
design goal.

## The matriz gateway — an agent proposed by the business, not the architecture

The most substantial technical discussion in the meeting, and the only place anyone
proposed building an agent for a problem they actually had.

**The situation.** Osmar built an application in Vercel centralizing the requests
*reportagem* makes to create **external events**. Leg one works. Leg two is creating
those events **automatically in the matriz**.

**Why that's hard:**

- The matriz is **sensitive data**, with many areas and access paths hanging off it.
- Osmar asked for the production-naming standard to change from `RJ externo` /
  `SP externo` to something generic, since the two-state assumption no longer holds.
  Arthur was pulled in to assess the impact; it has since been routed to Gabriel.
- **Nobody wants to give Osmar write access** — *"eu acho que não pode dar acesso ao
  Osmar criar isso. Porque, enfim, padrões de dados, pode criar qualquer coisa lá."*
- **Cancellation, not just creation**, has to reflect back.
- Responsibility shifts: today **Nina and Diego** in reportagem create these by hand;
  under automation they stop being the authors.

**Two proposals, both worth keeping:**

1. **An agent as the validating middle** — *"a gente pode até criar um agente para
   tá ali no meio, ver o que que ele mandou pra gente, a gente já interpretar ali e
   validar ou não e jogar na matriz."*
2. **The flow should live in Airtable, not only Vercel** — *"o pedido pode até
   entrar na Vercel, mas ele deveria conectar com uma base do [Airtable] porque
   senão você perde a história e você não sabe quem é [da] automação."* Floated
   shape: a **parallel base** that receives requests and writes into the matriz
   applying the team's standards, logging who requested and who cancelled.

**Unresolved:** who is supposed to approve these at all — *"mas quem deveria
[a]provar isso? Porque no final é uma gravação."*

> [!important] Why this matters to msilva specifically
> This is a **write path into the matriz** — the same tables whose IDs are pinned in
> [[Proxy Environments]]. It is also an approval-gated intake with an
> interpret-validate-write agent in the middle, which is A1 + A2 in miniature,
> arriving from the business rather than from [[Fluxo Agêntico diagram]]. See the
> candidate note on [[Agent Flow]].

## Facts stated

- **Farol's ownership is split** — data side this team, everything else the finance
  team; *"a gente só entrega dados para eles."* (Luís) This settles the grey zone
  Carolina left to the manager's discretion in [[2026-08-14 Papo de Projetos]].
  Note the tension: that page records Farol as *"being built by the finance team
  themselves"*, but Luís and Yasmin are visibly building it here.
- **Farol's GCP structuring is Luís's active task**, with the plan to run locally so
  Yasmin can test before anything reaches production. Progresses the *"blocked on a
  GCP project only Luís can create"* note in [[2026-08-14 Recap da Semana]].
- **Fronte de Negócios was in bad shape.** Luís: *"o projeto tava completamente
  podre […] quem tava desenvolvendo não tinha percebido isso, tava só tentando
  correr."* Gabrielle adds the mechanism: the documentation was inconsistent with
  reality, and *"não foram implementadas corretamente por IA, porque ela tinha um
  contexto errado do que realmente [aquilo] significava."* Luís wrote a glossary of
  the main terms on 2026-08-14 and **roughly 50% carried inconsistencies** in what
  the term actually meant inside the company's process.
- **Two weeks to production on Fronte**, and Gabrielle said plainly she lacks
  visibility: *"se eu vou me basear pelos 6%, eu vou entrar em pânico."* A separate
  meeting was agreed for it.
- **Luís expects usability work on Fronte beyond functionality** — *"não teria
  expectativa […] de isso ir pro ar e a galera estar usando sem precisar de alguns
  bons ajustes de usabilidade, não de funcionamento."* He is noting them rather than
  fixing them, to avoid extending the stabilization.
- **Orca: nothing is deployed.** The schedule was closed the previous week; Pedrinho
  has started but is still on documentation and the wordmap; *"nada ele ainda subiu
  para deploy em produção."* Pace was flagged to Carol and Luís. Deliveries planned
  for 2026-08-17, 2026-08-21, 2026-08-24 and 2026-08-28, with the project targeted
  to close by 2026-08-28. Relevant to [[Airtable Proxy]], which records Orca as a
  planned consumer, and to [[Which agent should be built first]], where Orca was the
  candidate first subject for A5 — **it is not observable yet.**
- **Camila starts Wednesday 2026-08-19**, not Monday as previously announced.
- **Gabrielle's week is handover** — organizing it and making sure everything is
  passed on, plus receiving Camila Wednesday to Friday, with projects still the
  priority. She is on leave from the week of 2026-08-24
  ([[2026-08-14 1-1 Matheus - Gabrielle]]).
- **Luís has been supervising msilva less closely than he intended** — *"eu só tenho
  feito o acompanhamento próximo ali do, um pouco mais distante do que deveria, do
  Mateus."* He also does small unregistered tasks with Carol and wondered aloud
  whether he should be tracking them.
- **Luís has nothing of his own this week** — his items sit in the backlog.
- **The TES trial is live.** The tool was presented the previous week and went well;
  Bia, Arthur and Kauan were added to a WhatsApp group for questions and
  perspectives; Bianca has already relayed feedback; a use-case meeting is planned;
  the speaker has access and will test this week, with formal feedback to the vendor
  the week after. **msilva is not in that group** — worth knowing, since
  [[Agent Flow]] and [[Comparing the first-agent candidates]] both treat TES as
  overlapping his problem space.
- **The old audience dashboard died with a departed employee's account.** It was a
  Data Studio dashboard tied to Henrique's account; the account was restored and it
  died anyway, and the history could not be recovered. Murilo had already been
  building a replacement, which Rai accelerated; a real audience page now exists and
  Arthur only needs to add a redirect from the central de dados. Luís suggested a
  transparent redirect *"para que as pessoas nem percebam o que mudou."*
  Worth generalizing: **single-account ownership of production dashboards is a live
  failure mode here**, and the same pattern is why the matriz schema knowledge is in
  transition ([[2026-08-14 Papo de Projetos]]).
- **The thumbnail generator is being switched off.** Design built a Figma plugin that
  does it in about two minutes; the baton was handed over the previous week and the
  team's version now needs turning off, including removing the API from the company
  card.
- **Yasmin finished documenting the audience monitor's data flow** — BigQuery and the
  application on Vercel, in both README and Docs; the N8N part is thinner. This week:
  a 24-hour-history feature, and an external-recording registration flow in the
  matriz she says she doesn't fully understand yet.
- **João Victor's first three weeks are a defined onboarding contract.** This week:
  cross-checking N8N and Monday flows owned by Yasmin, a detailed CRM architecture
  map (properties, boards, pipelines, what fills what), standardizing and cleaning
  the loss reasons, reactivating date automations, and low-risk fixes. Gabrielle told
  him to reuse **the Airtable documentation standard** — tables, fields, meaning,
  owner, who fills — so the CRM map and the Airtable map can be cross-referenced
  later when tracing automations that write to both.
- **A Slack channel for the CRM work** was created by João Victor, with Júlia, plus a
  weekly demand document.

## Open questions

- **Do Recap da Semana and this Weekly both continue?** They cover nearly the same
  ground three days apart.
- **Who actually approves external event creation** in the matriz — raised and left
  unanswered.
- **Does the Fronte test suite get isolated from Airtable by mocking, by a separate
  base, or by going through the [[Airtable Proxy]]?** Not discussed — but a project
  two weeks from production that wants Airtable-free tests is a natural proxy
  consumer, and nobody in the room made that connection.
- **Is the "second Airtable consumer already over-fetching" Orca, Fronte, or the
  matriz flow?** Still unresolved from [[Airtable Proxy]]; nothing here settles it.
- **Who was speaking** for Fronte de Negócios 2.0 and for the LiveScript/Júlia items.
  The transcript collapses them into the Gabrielle label and the content fits
  Carolina for the former. Flagged rather than guessed.

## Notable quotes

> "O projeto tava completamente podre, vamos dizer, por isso que tava dando
> problema." — Luís, on Fronte de Negócios

> "Se eu vou me basear pelos 6%, eu vou entrar em pânico." — Gabrielle, on the Linear
> readout

> "A gente pode até criar um agente para tá ali no meio, ver o que que ele mandou pra
> gente, a gente já interpretar ali e validar ou não e jogar na matriz." — on the
> Osmar / matriz gateway

> "Ele cria um workflow de trabalho […] e fica rodando, sei lá, 4, 5 horas fazendo
> tudo que tem que ser feito." — Luís, on Ultra Code
