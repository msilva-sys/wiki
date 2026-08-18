---
type: meeting
status: active
updated: 2026-08-18
date: 2026-08-18
attendees: [Gabrielle Ferreira, Matheus Silva]
source: "raw/Matheus _ Gabrielle - 2026_08_18 11_04 GMT-03_00 - Anotações do Gemini.md"
transcription_confidence: low
tags: [1-1, linear, process, farol, n8n, tokens, templates]
aliases: [1-1 2026-08-18]
---

# 1:1 Matheus / Gabrielle (2026-08-18)

Second 1:1, 11:04. Almost entirely a **process handover**: how the area actually
structures work in Linear, walked end to end, plus the fix for the execution-log
blocker from
[[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]].

Timing matters. Gabrielle is **on leave from 2026-08-24** for ~2.5 weeks
([[2026-08-14 1-1 Matheus - Gabrielle]]), and msilva is about to restructure the
proxy issues on this board. This is the last scheduled 1:1 before that window.

> [!danger] Source is Gemini's summary only — no transcript body
> Unlike every other transcript in `raw/`, this file contains **only** Gemini's
> generated blocks (*resumo* / *próximas etapas* / *detalhes*). The transcript
> itself lives behind a Google Docs link and was not exported.
>
> **Consequence: there are no verbatim quotes on this page, and there cannot be.**
> Everything below is Gemini's paraphrase of what was said. Gemini does attribute
> speakers here (unlike the earlier files, where it collapsed everyone into one
> name), so attribution is usable — but the wording is never the speaker's.
> Treat every claim as **weaker evidence than the quoted pages**. Where a claim
> here conflicts with a quoted transcript, the quoted one wins.

> [!note] Naming — Gemini writes "Liner" throughout
> It means **Linear**. Confirmed by the content: milestones, initiatives, projects,
> teams, a Slack integration, a ~250-issue free cap, and a Business trial — all
> Linear, all already recorded in
> [[2026-08-14 Migrate project management from Jira to Linear]]. Also normalized
> below: *AirTable* to Airtable, *Live Mode* to Livemode, *Luiz* to Luís.

## Decisions

- **Product feedback goes in Linear, code review stays in Git** —
  [[2026-08-18 Product feedback in Linear, code review in Git]].
- **n8n execution logs get saved, for both failures and production runs** —
  [[2026-08-18 Save n8n execution logs for audit]]. This unblocks the debugging
  session that stalled on 2026-08-17.
- **"Evolução do Front-End" and "Estabilização" are split into separate Linear
  projects.** Already done, not proposed. Rationale: a single project mixing the
  two gave a completion percentage that read as neither, and splitting makes
  bottlenecks identifiable. Recorded on [[Linear Project Structure]] and
  [[AI status reporting on Linear]] — this is the team **restructuring the board to
  make the status readout accurate**, which is the same lever msilva has.
- **Freelancer work is isolated in separate Linear teams**, for access control.

## Action items

- [ ] **Gabrielle** — send msilva the Linear project link and the folder of
      documentation. Handover item; blocking nothing yet, but it is the thing that
      goes stale if it doesn't happen before 2026-08-24.
- [ ] **msilva** — migrate his tasks off Jira (`AIRTABLEGC`) into Linear. Third
      time this has been assigned:
      [[2026-08-14 Migrate project management from Jira to Linear]], restated by
      msilva himself in [[2026-08-17 Weekly - Projetos e Tarefas]], now an explicit
      1:1 action. **Do it flat** — see the readout constraint on
      [[AI status reporting on Linear]].
- [ ] **msilva** — turn on execution saving so the logs can be audited.
      *(Inferred to mean n8n; see the decision page for why, and what would
      disconfirm it.)*

## Open questions

- **What are the "Tech" and "Humans" accounts, mechanically?** Gabrielle
  distinguished them — Tech has daily and monthly caps, Humans has a larger token
  allowance and an upgrade path — and said Tech can see **every flow in the
  company**. Two readings fit and the notes don't separate them:
  - **n8n**, where "flows" are workflows and the poor per-user filtering matches
    the shared-licence problem already recorded (Gabriel's executions had been
    pushed down the list by other people's,
    [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]); or
  - **the Claude/Anthropic org account**, where the token allowance and upgrade
    path fit better than they do for n8n.

  *(unverified: both readings are consistent with the notes. One question to
  Gabrielle settles it, and it should be asked before 2026-08-24 — it decides where
  the token-consumption instrument actually lives.)*
- **Does the Business trial get bought?** Gabrielle stated a preference for staying
  on Linear but no purchase was recorded. See the expiry risk below.
- **Who is the "person from product" that validates dev deliveries?** The new
  review flow names a role, not a person. Relevant to msilva because his proxy work
  will eventually pass through it.
- **Where does the Livemode Data Hub sit** — a live project under the Airtable
  governance initiative, or a placeholder? It was named as a sibling of the proxy,
  which would make it the proxy's downstream. Nothing else was said.

## Facts stated

All attributed by Gemini to Gabrielle Ferreira unless noted.

### Linear's hierarchy, stated explicitly for the first time

- **Initiative = the product or solution. Projects = specific segments of value
  delivery.** First time the wiki has the intended semantics rather than inferring
  them.
- The **Airtable governance** initiative contains the [[Airtable Proxy]], the
  **expansion to other applications**, and a **Livemode Data Hub**. So the proxy is
  one project inside a three-project initiative — see [[Airtable Proxy]] for why
  that matters to how it is framed.
- **Backlog is organized into milestones**, which act as grouping units for
  deliveries, above tasks and subtasks.
- Work starts from **documented business rules and context**, which then feed the
  roadmap and backlog. Documentation-first, matching the pattern the status readout
  identified as *"realmente um padrão"* ([[AI status reporting on Linear]]).
- A **centralized board** pulls milestones and tasks across projects, with
  visibility at issue level; project-specific detail is behind the project link.

Written up as a page in its own right: [[Linear Project Structure]].

### Projeto Farol, described directly

- Farol consolidates data from **several platforms' APIs into a single database**.
  In development, **Yasmin collaborating**.
- **V2 plan**: data layers — **bronze / prata / ouro** — plus a **conversational
  layer built on AI**.

This is the clearest description of Farol yet and it gets its own page: [[Farol]].
It also **forces a correction** on [[AI status reporting on Linear]], which had
taken *farol* to be the nickname of the AI status readout. It is not; it is this
project.

### Linear plan and cost

- The **free plan caps around 250 issues** (Gemini renders it "150 to 250 testes").
  Consistent with what
  [[2026-08-14 Migrate project management from Jira to Linear]] already recorded.
- The team is on a **Business trial with 22 days left** — expires **2026-09-09**.
- Gabrielle prefers to stay on Linear; the tool serves both the agents and the team
  well.

> [!warning] The trial lapses while Gabrielle is on leave
> 22 days from 2026-08-18 is **2026-09-09**. Gabrielle returns **2026-09-10**. The
> trial expires the day before she is back, and msilva is being asked to move the
> `AIRTABLEGC` backlog into that workspace in the meantime.
>
> If the plan reverts to free, the ~250-issue cap is back — and that cap was the
> original reason the migration was postponed. **Nobody stated this in the
> meeting**; it falls out of putting the two dates side by side. *(unverified:
> whether a purchase is already in motion.)* Worth raising before 2026-08-24,
> because the person who can authorize it is the person leaving.

### Review, feedback and templates

- New flow: work submitted by the dev team is **validated by someone from product**,
  who tests in the interface and can **return the task for correction** if it is
  inconsistent. Structured through Linear's **comment system**.
- **Luís created Markdown templates** for ticket types — **bugs, spikes, epics** —
  to keep tasks and subtasks consistent. Complements the ticket-template thinking
  in [[Agent Flow]] (*"the ticket is the prompt"*) with something that actually
  exists in the tool.
- Feedback split: **product and business-rule** feedback in Linear; **strictly
  technical and code** review in Git.

### Slack integration

Linear projects can be connected to specific Slack channels, pushing project
progress and task movements automatically. Mentioned as a possibility, not
configured. Noted on [[Agent Flow]] as an already-available delivery channel — an
agent that needs to tell people something may not need to build notification.

### Execution logs and token cost

- Gabrielle diagnosed Gabriel's budget and execution problems as **execution logs
  not being saved by default**. That is the same blocker
  [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] recorded twice
  as execution traceability being the blocker — and it now has a cause and a fix
  rather than a symptom.
- Token in/out flows were reviewed against the cost swing **$7 to 11 centavos**.
  Independently restates the 2026-08-17 finding; two sources now agree the $7 was
  the bad case, not the rate.
- The **Tech account can see every flow in the company**, but **per-user filtering
  is limited** — finding one person's runs means manually scanning update history.

## What this changes elsewhere

| Page | Change |
|---|---|
| [[AI status reporting on Linear]] | *farol* is a project, not this system's nickname. Corrected inline. |
| [[2026-08-14 Migrate project management from Jira to Linear]] | The structural shape to migrate *into*, and the trial's expiry date. |
| [[Airtable Proxy]] | It is one of three projects in the Airtable governance initiative. |
| [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] | The logging blocker is resolved. |
| [[Pulse]] | Front-end evolution and stabilization are now separate Linear projects. |
| [[Agent Flow]] | Slack integration; templates; token visibility via the Tech account. |
