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

## Resolved

- **Does the `AIRTABLEGC` board get archived, migrated wholesale, or abandoned in
  place?** → **Drained wholesale.** Luís, 2026-08-17: *"graças a Deus, a gente caiu
  no Linear. Então agora a gente tira tudo de lá."* msilva restated the migration of
  his own items as his task in the same meeting.

## Open questions

- What is the Linear project/team identifier, and what do the new issue keys look
  like? Still not stated as of 2026-08-17.
- Confirm the reordering with Luís before restructuring further — Gabrielle
  suggested checking his thinking first. Note that as of 2026-08-17 Luís said he had
  been following msilva *"um pouco mais distante do que deveria"*, so this needs
  actively seeking out rather than waiting for.
- Do the subtask-rollup mechanics have a fix, or is flat structure the only
  workaround? See [[AI status reporting on Linear]].
