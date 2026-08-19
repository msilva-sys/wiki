---
type: concept
status: active
updated: 2026-08-19
aliases: [Linear structure, initiatives and projects, milestones, Liner]
tags: [linear, process, project-management]
---

# Linear Project Structure

How msilva's area organizes work in Linear. Recorded because
[[2026-08-14 Migrate project management from Jira to Linear]] told msilva to
restructure the [[Airtable Proxy]] issues **however he sees fit**, and left the
question of *shape* entirely open. This page is the shape.

Source: [[2026-08-18 1-1 Matheus - Gabrielle]], a screen-share walkthrough. Quotes are
verbatim from the transcript, including its garbling — *Linear* comes through as
"liner", *issue* as "iso"/"nicho", *milestone* as "mystone".

> [!important] The conventions are provisional, and msilva is invited to change them
> Gabrielle, unprompted: *"a gente tá muito nesse processo de entender o melhor fluxo de
> como criar as coisas no Lin[ear]. Então, não tem muito também um certo nem um errado
> […] pode ficar à vontade[,] cara[,] [se] ter algum insi[ght], alguma maneira que você
> tá achando que não tá funcionando[,] de pegar e de mudar."*
>
> Read everything below as current practice under active revision, not as policy. Two
> of the conventions were **changed the day before this meeting**.

## The hierarchy

| Level | What it means | Stated by |
|---|---|---|
| **Initiative** | The **product or solution** | Gabrielle, 2026-08-18 |
| **Project** | A **specific segment of value delivery** within the initiative | ⇧ |
| **Milestone** | A grouping unit for deliveries inside a project's backlog | ⇧ |
| **Issue / task** | The unit of work. What the status readout counts | ⇧ |
| **Subissue** | Breakdown inside an issue. **Invisible to the tracking board — by design, drill-down planned** | [[AI status reporting on Linear]] |

In Gabrielle's words:

> *"A gente tá tratando no final a iniciativa como o produto, o sistema, a solução em si
> e aqui projetos como um pedaço eh uma parte daquela iniciativa."*

The initiative/project distinction is the part that was previously being inferred.
It is not "big thing / small thing" — it is **solution / delivery segment**, which
is why one initiative can hold projects that look unrelated.

**Her first example is [[LiveScript]]:** the product is the initiative; the version that
went to production and each subsequent release are its projects. So *release* is a
natural project granularity, not just *feature area*.

### Three worked examples

**Airtable — governance and reliability** (*"governando confiabilidade"*), three
projects, all created by Luís:

1. the [[Airtable Proxy]];
2. **expanding the proxy to other apps beyond LiveScript**;
3. a **Livemode data hub** — an intermediate database, plausibly the wiki's
   already-recorded Phase 2. Gabrielle is relaying Luís second-hand and hedges four
   times; ask him.

Read against the definition, the *solution* is governing and stabilizing Airtable access
company-wide, and the proxy is one delivery segment. That matches msilva's own internal
framing of the proxy as company-wide infrastructure rather than a [[LiveScript]] fix
([[2026-08-14 Recap da Semana]]) — the initiative structure is where that intent is
actually encoded.

**A fourth sibling project joined 2026-08-19**: *Proxy em produção validado c/
LiveScript*, promoted out of `Proxy do Airtable`'s F3 milestone (see the fourth
convention below). First concrete instance of the "milestone → sibling project"
move Luís described, rather than just the three Luís had created up front.

**[[Farol]]**: the initiative is the product; the current build is project one, and
**V2** — bronze/prata/ouro layers plus a conversational AI layer — is project two. A
concrete demonstration that "the V2" is a project, not a roadmap aspiration.

**[[Agent Flow]]**, prospectively. Gabrielle: *"talvez quando você for fazer o de agente
do fluxo agêntico, talvez você iria criar uma iniciativa e aí talvez cada parte ali seria
um projeto."* Its own initiative, one project per part — which lines up with
*anarchic-first*, where each agent is independently buildable and independently in
production.

## How work enters — and Claude does most of it

1. **Business rules, context and PRDs are documented first.** The documentation is the
   input to the roadmap, not a byproduct of it.
2. **The documentation goes to Claude, which writes the Linear structure.** This is the
   step the wiki had reconstructed as manual:

   > *"eu primeiro pego […] alguma documentação ali do projeto em si, mas de produto,
   > PRDs, etc. E aí eu chego no [Claude] com ele aqui. E aí depois que eu faço isso, eu
   > peço para ele jogar tudo lá pro [Linear]. E aí ele […] cria tanto a descrição do
   > projeto […] ele próprio já cria aqui os milestones e aí […] dentro dos milestones vai
   > subir [issues]. Enfim, ele cria as tarefas sozinho."*

   Project description, milestones, issues and subissues, generated from the templates.
   See [[Agent Flow]] — this is a running agent workflow, and the wiki previously
   believed the status readout was the only one.
3. The backlog is organized into **milestones**, which group deliveries.
4. Issues and subissues hang off those.

Linear also has a **native description-improving agent** for ad-hoc tasks: *"quando você
bota a descrição aqui, você tem o agentezinho[que] trata a descrição para dar uma
melhorada."*

Step 1 is worth noticing: the AI status readout independently identified
*large-scope projects advance slowly at the start, with early effort going into
documentation*, and Gabrielle called it a real pattern
([[AI status reporting on Linear]]). That is not a dysfunction the readout found —
it is the method working as designed. The readout measured the process and reported
it as a problem, which is its own small lesson about giving an agent a model of
progress that matches how the team actually works.

## Monitoring — the board is not Linear

The board the team reviews weekly is a **separate in-house tool that pulls from Linear**:

> *"Ele puxa todos os projetos que estão classificados como em andamento. E aí dentro de
> cada projeto você consegue selecionar se você quer buscar a fonte do [backlog] do
> liner, porque nem todos os projetos estão no liner ainda. […] ele puxa aqui todos os
> milst[ones] que tem lá no liner e as [tasks] dentro dele."*

- It pulls projects **classified as in-progress**, so a project's classification is what
  gets it onto the board at all.
- Per project you choose whether to source the backlog from Linear — because
  **not every project is in Linear yet**, mid-migration.
- **Aggregation stops at issue level, deliberately**: *"ele traz a nível de [issue], ele
  não traz a nível de sub[issue], que é para poder também não ficar tão confuso aqui. A
  gente precisa só ideia geral […] visibilidade do todo."* Subissue drill-down is planned
  — *"Eu vou depois liberar aqui também para conseguir clicar e abrir sub[issues]."*
- It appears to read **Airtable** (*"no final ele tá puxando do Air Table"*), which would
  make it a proxy candidate. *(unverified — one clause in a garbled passage.)*

That closes the standing question on [[AI status reporting on Linear]]: the subtask
blindness is a **design choice by the tool's owner**, not a defect of unknown origin.

**Work outside projects gets a personal board** — a second tab, for things like msilva's
help to Gabriel. Plus a self-filter (*"tudo que tá no seu nome, ele fica filtrado"*) and
an **inbox** for comment mentions.

### Restructuring to make the readout accurate — two fixes, not one

The *Fronte de Negócios* project read as *"2% 3% […] acho que se 6%"* complete. Both
fixes were to the board:

1. **Split the project.** The stabilization project's milestone contained everything in
   the backlog that was *evolution* rather than stabilization, dragging the number down.
   They created a separate front-end evolution project and moved it all —
   *"isso já dá um aspecto mais real […] [da] porcentagem de conclusão do projeto."*
2. **Break up the single milestone.** All stabilization tasks sat in **one** milestone,
   so **Carol** could not see where the work stood. Regrouped 2026-08-17; now
   *"a gente já consegue saber melhor o que realmente tá travado, [o] que a gente já
   andou."*

Fix 2 carries the sharper lesson, and the first version of this page missed it entirely:
**milestone granularity is the reporting instrument.** Milestones are the level the board
*does* count, so a milestone that holds everything is exactly as uninformative as a
subtask tree — while several well-scoped milestones make progress legible without any
nesting.

> [!important] Three conventions for msilva, in priority order
> He is restructuring the proxy backlog this week, and this board is what Gabrielle and
> Luís see while she is on leave from 2026-08-24.
>
> 1. **Several scoped milestones, not one big one.** This is the positive move — it makes
>    partial progress visible at the level the board reads.
> 2. **Split projects along "new work" vs "fixing earlier work"** so a percentage means
>    something. The proxy has that seam: v1 hardening versus new capability.
> 3. **Keep issues flat**; subissues don't roll up *yet*. Park found-but-unprioritized
>    work in Luís's `Triagem` column instead of burying it
>    ([[2026-08-17 Weekly - Projetos e Tarefas]]).
>
> Convention 3 is the one with an expiry date — drill-down is planned. Conventions 1 and
> 2 hold regardless.

> [!important] Fourth convention, 2026-08-19 — a milestone you're actively working can be promoted to its own project
> [[2026-08-19 1-1 Matheus - Luís]], walking the hierarchy live in Linear with
> Luís. **Corrected from an earlier, looser reading of this call** — the raw
> transcript is more specific than "only the active project needs structure":
> Luís explicitly said the milestone msilva was actively working in should
> become its own **project**, sibling to the one holding it, inside the same
> initiative:
>
> > *"aqui você vai pegar uma versão dessa inteira aqui, que é onde você tá
> > trabalhando. **Isso é um projeto, cara. Esse projeto tem milestones.**
> > Quais são os milestones? A forma que você quiser se organizar..."*
>
> Everything else — the milestone(s) left behind, and the rest of the
> restored, deleted-then-restored Jira-migrated backlog (see
> [[2026-08-14 Migrate project management from Jira to Linear]]) — doesn't need
> to move over now. Deleting it is fine; it becomes its own sibling project
> inside the initiative later, at no rush:
>
> > *"Todas essas issas aqui, o restante pode deletar tudo, não tem
> > problema[...] vai virar em algum momento **projeto irmão** desse aqui
> > dentro da iniciativa. Isso te tira carga cognitiva."*
>
> **Executed 2026-08-19**: `Proxy do Airtable`'s F3 milestone (*"Proxy em
> produção validado c/ LiveScript"*) promoted to its own project of the same
> name, sibling to `Proxy do Airtable` inside **Airtable GC — Governança e
> Confiabilidade**. `Proxy do Airtable` keeps F1, F2, and Backlog deferido
> untouched, per msilva's explicit call to leave it as-is.
>
> **Reshaped again, same day, at msilva's request.** First pass gave the new
> project one milestone per existing epic — wrong, per msilva: *"epic are more
> related to an issue, no?"* Milestones are project-level checkpoints, epics
> are issue-level parents; a 1:1 mirror between them is redundant, and one of
> the six (the already-100%-done MVP epic) had nothing left to track going
> forward. Reshaped to **3 delivery checkpoints** — *Proxy funcionalmente
> completo* (auth + observability + the finished MVP work underlying them),
> *Proxy em produção* (deploy), *Validado com o LiveScript* (integration) —
> and the epics became real `parentId` parents of their own issues instead of
> siblings-by-milestone. See [[Airtable Proxy]] for the issue-level detail,
> including a real finding that fell out of this pass: the *Deploy em
> produção* and *IaC (Pulumi)* epics turned out to be the same outcome built
> two ways (manual vs. code), not two phases — merged into one parent
> (`PRO-84`) before the milestone reshape, rather than kept as two.
>
> **Tension worth flagging, not yet resolved**: real parent/sub-issue
> structure is exactly what convention 3 above (issues flat, no subtask
> rolled-up progress) exists to avoid — the readout's 6%-vs-20-25% blind spot
> was measured on a *parent* issue with unfinished children, same shape as
> `PRO-78`/`PRO-84`/`PRO-74`/`PRO-95` now. Flagged to msilva before doing it;
> he chose to proceed with 4 of the 6 candidate clusters anyway (skipping the
> already-Done MVP cluster). Milestones remain the safety net for readout
> accuracy — they're what the board actually counts — so the risk is
> contained as long as milestone progress stays the source of truth for
> reporting, not subtask rollup.
>
> Also from this session: **msilva has autonomy to make Linear organization
> decisions himself** — Luís explicitly declined to be treated as the
> project's owner (*"não me considero dono do projeto"*) and only wants to be
> told what changed, not asked up front. And Luís admits he's using *"5% do
> potencial"* of Linear himself, and is reading Linear's own docs over the
> weekend of 2026-08-22/23 to bring back organizing guidance.

## Teams as the isolation boundary

**Teams are the workspace and permission unit** — *"a gente tem os times que são como se
fosse o workspace"*. Two exist for freelancers specifically: *"A gente só criou esses dois
aqui separados porque são com [freela] para ele não ter acesso a todos os nossos outros
projetos."* The proxy's project sits in the main *projetos* team.

**But initiatives cut across teams:** *"os times é mais como se fosse você dando acesso
[…] Mas a iniciativa ela olha pro todo projeto, ela não vai ser ligada dentro."* So team
isolation scopes *access*, not the initiative rollup.

Relevant beyond staffing: it is the only access-control mechanism in the area's
tooling that has been described at all. If an [[Agent Flow]] agent is ever given
Linear credentials, the team boundary is what scopes it — with the initiative-level
caveat above.

## Templates — two kinds, and their author is unhappy with them

- **Per-type templates**: Markdown files for **bugs, spikes and epics** —
  *"o Luís, ele criou uma[s] templates […] para cada tipo de [issue] […] Se é bug, se é
  spike"*. Applied when the issue is created.
- **A structural template**: *"como dividir as tarefas em M[ilest]on[e], em épicos, em
  [issues] e sub[issues]"* — the conventions for what each level means. This is arguably
  the more valuable of the two, since it encodes the granularity decisions above.
- A further `.md` **relating the project to Linear and to the skills**.
- Gabrielle has her own set and rates it poorly: *"o meu eu não achei que tá tão bom."*

> [!warning] Luís is rewriting his because he dislikes how they turned out
> *"ele tava ajustando um pouco porque ele não tava gostando muito dos templates de como
> tavam. Vai também depois perguntar a ele o que que ele achou que tá ficando ruim."*
>
> So do not simply adopt them. The templates are the local instance of *the ticket is the
> prompt* ([[Gabriel Packer - DAG-driven agent orchestration]]), and they are what Claude
> uses when it authors the backlog — but **their author's account of why they fail in use
> is the better input** to A2's output contract than the templates themselves. Gabrielle
> assigned asking him.

## Slack integration

Linear projects can be wired to specific Slack channels, pushing progress updates
and task movements automatically. Available, **not configured** — and Gabrielle offered
it for the proxy specifically:

> *"dá para você conectar lá no canal do que já existe do proxy e aí botar para ele,
> tipo, ah, toda vez que você tiver um update, ele post[a] ou sempre que você mover uma
> tarefa."*

**A proxy Slack channel already exists.** Choose the trigger: every project update, or
every task movement.

Worth carrying forward: an agent that needs to report to humans may not have to
build a notification path. If A2 writes a Linear issue, the existing integration
delivers it. See [[Agent Flow]] and [[How to implement A5 Watcher]], where
delivery-channel choices are otherwise treated as open.

**Release notes to Slack, specifically — from Luís, 2026-08-19.** Linear can
already auto-post a release note to a Slack channel whenever a project ships
a new version. Raised in the context of [[Agent Flow]]'s A14 (PM Agent),
which is largely a reporting/status layer — this piece of it may need no
new build at all. See [[2026-08-19 1-1 Matheus - Gabrielle]].

**Project updates are hand-written weekly.** Gabrielle writes a project health note and
status each Friday — *"ele traz aqui a saúde do projeto"* — framed as her own practice
rather than a team requirement. Worth knowing it exists as a surface: it is the narrative
that sits alongside the computed percentage, and the place a wrong number can be
qualified in words.

## Plan constraints

- Free plan caps around **250 issues** (*"uma barreira de 250, se não me engano, 150"*) —
  the original reason the Jira migration was postponed.
- The team is on a **Business trial expiring 2026-09-09**
  ([[2026-08-18 1-1 Matheus - Gabrielle]]).
- **The purchase is undecided**, not pending: *"pra gente entender qual o plano faz
  sentido a gente assinar e se faz sentido assinar."*
- Gabrielle's stated preference is to stay: *"eu particularmente eu gosto do liner, eu
  acho que a gente vai continuar nele."*

> [!warning] The cap is not permanently gone, and msilva has already hit it
> [[2026-08-14 Migrate project management from Jira to Linear]] recorded the trial
> as having *removed* the constraint. It **suspended** it. The trial expires
> 2026-09-09; Gabrielle returns 2026-09-10. Migrating the `AIRTABLEGC` backlog into
> the workspace in that window is exactly what fills the cap.
>
> And it is not theoretical: Gabrielle to msilva, *"você tomou um rate limit, né? […] não
> podia mais criar."* He hit the ceiling already, before the migration.

## Open questions

- ~~**What is a `Release`, and how does it map to milestones?**~~ **Answered
  2026-08-19, msilva** (resolving a question Luís raised the same call, in
  [[2026-08-19 1-1 Matheus - Luís]]): a release isn't a Linear-native concept
  sitting below Project — **a release *is* a project inside the initiative**,
  matching the LiveScript example above. Milestones stay a manual, per-project
  call (convention 1 above); "ticket" and "issue" are the same thing.
- ~~**What is the Linear team/project identifier, and what shape are the new issue
  keys?**~~ **Answered 2026-08-18** — checked Linear directly instead of waiting on
  Gabrielle's link. Team `Projetos-livemode`, `PRO-*` keys, three projects under the
  **Airtable GC - Governança e Confiabilidade** initiative exactly as described above:
  [Proxy do Airtable](https://linear.app/projetos-livemode/project/proxy-do-airtable-0d3e6dd699eb),
  **Proxy expandido para outros apps**, **LiveMode Data Hub (POC)**. See
  [[2026-08-14 Migrate project management from Jira to Linear]] for the fuller finding:
  the proxy project already held 60 issues across its 4 milestones, and the migration
  itself turned out to be substantially already done.
- ~~**Which initiative does [[Agent Flow]] belong to?**~~ **Answered** — its own, one
  project per part (Gabrielle, hedged).
- **What does Luís think is wrong with his templates?** He is rewriting them. Assigned.
- **Is the Livemode data hub a real project?** Described as an intermediate database for
  migrating off Airtable, second-hand and hedged four times. Ask Luís.
- **Does the tracking board really read Airtable?** If so it is a proxy candidate, and
  the tool watching msilva's project would sit behind the project itself.
- **Does a project have to be classified *em andamento* to appear on the board?** Implied.
  If so, classification is a visibility lever worth knowing about.
