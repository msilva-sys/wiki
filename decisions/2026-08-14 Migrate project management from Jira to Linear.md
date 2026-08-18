---
type: decision
status: active
updated: 2026-08-18
date: 2026-08-14
decided_by: Gabrielle Ferreira
source: "[[2026-08-14 1-1 Matheus - Gabrielle]]"
tags: [process, tooling, linear, jira]
---

# Migrate project management from Jira to Linear

**Decision.** The area centralizes all project management in **Linear**. Jira is
being wound down. msilva is explicitly encouraged to migrate the
[[Airtable Proxy]] project and **restructure its issues however he sees fit**.

## Why it was blocked, and why it isn't now

Linear's free plan caps at **250 issues**, and migrating everything at once would
have hit the ceiling. The migration was deliberately postponed for that reason.
A **subscription trial upgrade** has since removed the constraint, so the
migration is live.

> [!danger] Corrected 2026-08-18 — the trial *suspends* the cap, it does not remove it
> The line above read *"has since removed the constraint"*. It is a **time-boxed
> Business trial with 22 days left as of 2026-08-18** — expiring **2026-09-09**
> ([[2026-08-18 1-1 Matheus - Gabrielle]]). Gabrielle stated a preference for
> staying on Linear; **no purchase was recorded.**
>
> The dates line up badly. Gabrielle is on leave **2026-08-24 → 2026-09-10**, so the
> trial lapses the day before she returns, and the migration msilva has been asked to
> perform is what fills the workspace toward the cap in the meantime. Nobody in the
> meeting connected these; it falls out of the calendar. *(unverified: whether a
> purchase is already in motion.)*
>
> **Ask before 2026-08-24** — the person who can authorize the plan is the person
> leaving.

## Why restructuring is sanctioned, not just tolerated

Luís, who authored the original proxy issue breakdown, considers it outdated —
he has said he would design it differently today. Gabrielle's framing was that
whoever picked up the project would likely need to redesign it anyway, and that
the redesign and the migration should happen together. He has **already
reordered** the issues once, to keep msilva inside the proxy and out of
[[LiveScript]] for now.

## Consequences

- **The wiki's Jira assumptions are now legacy.** `AIRTABLEGC` and refs like
  [[AIRTABLEGC-34]] describe the old board. Keep them as historical record; don't
  create new pages against Jira IDs.
- A Linear project shell for the proxy **already exists** — it needs issues and
  descriptions filled in, not creating.
- Issue IDs will change. Any page citing a Jira key should eventually carry the
  Linear equivalent.

## Why the proxy was in Jira in the first place (2026-08-17)

Not a leftover — a deliberate tool trial. Luís, in
[[2026-08-17 Weekly - Projetos e Tarefas]]: he was *"o rebelde contra o Arthur"*
and tested the options against each other, *"tentei no Linear, tentei no Jira,
tentei em qualquer [uma]."* The proxy was consequently **one of the projects
deliberately left un-migrated**, and msilva *"acabou pegando as tarefas"* —
inheriting them on the losing board.

Two things follow. The `AIRTABLEGC` board's legacy status is unambiguous: it was a
trial, it lost. And the restructuring licence on this page is not a favour to a new
joiner — the issue breakdown was written inside an experiment that has since been
abandoned.

## How msilva should structure the issues — added 2026-08-18

This page sanctions restructuring but said nothing about *shape*. It now has a
constraint, from [[AI status reporting on Linear]]: the AI status readout the team
reviews weekly **ignores subtask progress** and reports a parent issue as
undelivered until it is wholly done. Measured effect on a real project: **6%
reported against 20–25% actual.**

**Flat issues are legible; deep subtask trees are invisible.** msilva is
restructuring while the project-deadline conversation is live and he has the least
established track record on the team, so a 6%-shaped readout is a real cost.
Luís's `Triagem`-column convention is the complement — found-but-unprioritized work
goes there, visible, rather than being buried as subtasks.

## The structure to migrate *into* — added 2026-08-18

This page sanctioned restructuring and the section above constrains it to *flat*.
[[2026-08-18 1-1 Matheus - Gabrielle]] supplies the rest of the shape, written up in
full at [[Linear Project Structure]]:

- **Initiative = product or solution. Project = a specific segment of value
  delivery.** The [[Airtable Proxy]] is **one of three projects** in an **Airtable
  governance** initiative, alongside *expansion to other applications* and a
  *Livemode Data Hub*. Migrating the proxy backlog means populating one project, not
  representing the whole programme.
- **Milestones group deliveries** inside the backlog. That is the level above issues
  — and the one the readout does count, unlike subtasks. Milestones are therefore the
  place to express "this is a chunk of work" without incurring subtask blindness.
- **Business rules and context get documented first**, and the roadmap and backlog
  derive from that documentation. For the proxy the documentation already exists
  (`airtable-proxy-design.md`, `doc/STATUS_proxy_airtable.md`), which puts msilva
  ahead of the normal starting point rather than behind it.
- **Luís authored Markdown templates for bugs, spikes and epics.** Use them rather
  than inventing an issue format; consistency here is what the product-validation
  gate reads ([[2026-08-18 Product feedback in Linear, code review in Git]]).

**A third assignment of the same task.** Migrating his own items was assigned here on
2026-08-14, restated by msilva himself in
[[2026-08-17 Weekly - Projetos e Tarefas]], and made an explicit 1:1 action item on
2026-08-18. It has not moved in four days, and the trial clock above is now running.

## Resolved

- **Does the `AIRTABLEGC` board get archived, migrated wholesale, or abandoned in
  place?** → **Drained wholesale.** Luís, 2026-08-17: *"graças a Deus, a gente caiu
  no Linear. Então agora a gente tira tudo de lá."* msilva restated the migration of
  his own items as his task in the same meeting.

## Open questions

- What is the Linear project/team identifier, and what do the new issue keys look
  like? Still not stated as of **2026-08-18**, now across three meetings. Gabrielle
  has an open action to send the project link, which answers it.
- **Is the Business plan being purchased before 2026-09-09?** See the correction
  above. This is the one with a deadline.
- Confirm the reordering with Luís before restructuring further — Gabrielle
  suggested checking his thinking first. Note that as of 2026-08-17 Luís said he had
  been following msilva *"um pouco mais distante do que deveria"*, so this needs
  actively seeking out rather than waiting for.
- Do the subtask-rollup mechanics have a fix, or is flat structure the only
  workaround? See [[AI status reporting on Linear]].
