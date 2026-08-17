---
type: decision
status: active
updated: 2026-08-17
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

## Open questions

- What is the Linear project/team identifier, and what do the new issue keys look
  like? Not stated in the meeting.
- Does the `AIRTABLEGC` board get archived, migrated wholesale, or abandoned in
  place?
- Confirm the reordering with Luís before restructuring further — Gabrielle
  suggested checking his thinking first.
