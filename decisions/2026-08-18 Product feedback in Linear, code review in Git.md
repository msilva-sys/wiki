---
type: decision
status: active
updated: 2026-08-18
date: 2026-08-18
decided_by: Gabrielle Ferreira
source: "[[2026-08-18 1-1 Matheus - Gabrielle]]"
tags: [process, linear, git, review, product]
---

# Product feedback in Linear, code review in Git

**Decision.** Review is split by *kind*, not by stage:

| Kind of feedback | Where it lives |
|---|---|
| Product behaviour, business rules | **Linear**, in the issue's comments |
| Strictly technical, code-level | **Git** |

The team explicitly discussed where to centralize reviews and landed here
(Gabrielle, [[2026-08-18 1-1 Matheus - Gabrielle]]).

## The flow it implements

Work submitted by the dev team is **validated by someone from product**, who:

1. tests it in the interface;
2. **returns the task for correction** if it is inconsistent;
3. structures that exchange through **Linear's comment system**.

So Linear carries a real approval gate, with a return path — not just a status
field.

## Why this is a decision and not a detail

It resolves an ambiguity the wiki was carrying. The area had already removed
mandatory human PR review while the proxy is pre-production
([[2026-08-14 No mandatory PR review while the proxy is pre-production]]), which
left an open-looking question: is *any* review happening?

The answer is that review did not disappear, it **moved and specialized**. The
technical gate was relaxed; a product gate was formalized. Those are two different
decisions about two different risks, and reading them together is what makes the
first one defensible rather than reckless.

## Consequences

- **msilva's proxy work will eventually pass through a product validator.** Not yet
  — the proxy is pre-production and has no interface to test — but the framing of it
  as *company-wide infrastructure with Orca and other services as consumers*
  ([[Airtable Proxy]]) means it acquires product-facing behaviour, and a product
  reviewer, at some point.
- **Issue descriptions become the artifact a non-engineer reads.** They are the
  input to the validation, which raises the bar on how msilva writes the
  restructured proxy issues. Luís's Markdown templates
  ([[Linear Project Structure]]) are the intended aid.
- **Git review remains msilva's own** for the proxy, per the 2026-08-14 decision.
  This decision does not reopen it.
- **Two review surfaces means two places to look for the history of a change.**
  Worth knowing before hunting for why something was built a particular way.

## Bearing on [[Agent Flow]]

The architecture's premise is *humans as strategic approvers and quality refiners*
([[Fluxo Agêntico project instruction]]). This decision is that premise as an
org chart: **the strategic approval is product's, in Linear; the quality refinement
is technical, in Git.** Any agent that produces work for human approval has to know
which of the two gates it is submitting to, and they have different reviewers,
different vocabularies, and different rejection criteria.

For **A2** specifically — whose output is a ticket — the gate is the Linear comment
thread, which the [[Linear Project Structure|Slack integration]] can already deliver
to a channel.

## Open questions

- **Who is the product validator?** A role was named, not a person.
- **Does the return path have an SLA or a state?** "Returned for correction" as a
  Linear status vs. just a comment matters for anything computing progress from the
  board — including the status readout, which already miscounts
  ([[AI status reporting on Linear]]).
- **Does a product rejection require a Git change to be reverted?** Unstated, and it
  is the seam between the two surfaces.
