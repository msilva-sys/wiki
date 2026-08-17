---
title: Sharing via Projects — what belongs in the accounting project
type: reference
status: active
updated: 2026-08-17
date: 2026-08-14
aliases: [sharing via projects, the accounting project]
tags: [claude, projects, sharing, accounting, knowledge-base]
related: "[[Sharing the accounting automation with the team]]"
---

# Projects as a sharing mechanism

Companion to [[Sharing the accounting automation with the team]].

> [!warning] Yes — but for the *context*, not the *automation*
> A Project shares **what the team needs to know**. It does not share **how to do the
> procedure** (that's a skill), **access to the data** (that's a connector), or **the
> schedule** (that's a task). Share only a Project and each person still has to
> re-invent the method — they'll just be re-inventing it against the same chart of
> accounts.
>
> Projects are one leg of a three-leg stool. A good leg. Not the stool.

And one trap to know before you plan around it: **Cowork projects are local to the
desktop and cannot be shared** on Team or Enterprise plans. Only **claude.ai Projects**
support sharing. If the automation ends up running in Cowork, the shared Project is a
companion to it, not its home. (Cowork projects *can* reference a chat project as
context — that's the bridge between the two.)

---

## What a Project actually shares

| Shared | Not shared |
|---|---|
| Custom instructions | Individual chats — **private by default**, even in a shared project |
| Knowledge files (documents) | Skills / procedures |
| The workspace itself, org-wide or invite-only | Connectors and data access |
| Permission levels: *Can view* / *Can edit* | Scheduled tasks |

That chat privacy default cuts both ways. Good: nobody browses a colleague's draft
analysis. Bad: the team can't learn from each other's good prompts. Mitigate it with a
**worked-examples document** in project knowledge that people contribute back to.

Sharing is a Team/Enterprise capability; Free accounts cap out at five projects, and paid
plans get the RAG expansion.

---

## What belongs in this project

### Instructions — the house rules, written once

This is the highest-value part, and the part people skip. Encode the workflow you'll
teach on Monday so it applies to every conversation without anyone remembering it:

- Always use code execution for tabular data. Never print raw rows into the conversation.
- Aggregate to the question's grain before analysing; drill into detail only where the
  aggregate flags something.
- Every number that will be reported must tie out — state the check and show the code.
- Output conventions: BRL, thousands separators, the fiscal calendar, date format,
  PT-BR or EN for deliverables.
- Materiality thresholds — what's worth flagging and what's noise.
- What must go to a human before it leaves the team.

### Knowledge — reference material, not data

- **Chart of accounts** — a lookup table, small, and it makes every future answer sharper.
- **Entity / subsidiary structure** and the intercompany mapping.
- **Cost centre list** with owners.
- **Close calendar** and the obligations calendar (SPED ECD/ECF and friends).
- **Data dictionary for the ERP export** — what each column means, units, sign
  conventions, which fields are unreliable, known quirks. Quietly the most valuable
  document in the whole project: it's what lets someone who didn't build the export ask a
  correct question about it.
- **Accounting policy notes** and materiality thresholds.
- **Known-issues log** — recurring reconciliation items, standing adjustments, the
  accounts that always look wrong and why.
- **Worked examples** — a redacted sample export plus the correct answer and the prompt
  that produced it. Teaching by demonstration scales better than teaching by rule.
- **Prompt library** — the handful of prompts that reliably work.

### Explicitly keep out

- ❌ **The ledger itself.** Transaction detail in project knowledge gets chunked and
  retrieved, and retrieved chunks get summed into confident, wrong totals. Data goes
  through code execution or a connector — never through retrieval. This is the single
  most important rule on this page.
- ❌ **Anything sensitive** if the project is org-visible. Check who can see it before
  uploading, and prefer invite-only for anything with real figures.
- ❌ **Live or fast-changing data.** Project knowledge is a snapshot; it goes stale
  silently and nobody notices until a number is wrong.
- ❌ **Files near the 30MB-per-file limit** — if it's that big, it's data, not reference.

---

## How the pieces fit together

```
Shared claude.ai Project ──→ what the team knows
   instructions + chart of accounts + data dictionary + examples
                │
                ├──→ Plugin / skills ──→ how the team does it
                │       (published to a private marketplace, targeted at the group)
                │
                ├──→ Connector / MCP ──→ where the data lives
                │       (admin-configured once, not per person)
                │
                └──→ Cowork project ──→ where each person runs it
                        (local, per-user, can reference the chat project)
```

Build them in that order. The Project is the cheapest to create and the one that makes
every other piece work better, so start there — just don't stop there.

---

## Practical next step

Before Monday, you could stand up an empty shared Project with the instructions block
drafted. Then in the meeting, filling in the chart of accounts and the data dictionary
becomes a task you do *with* them — which is a much better collaboration than handing
over a finished artefact, and it surfaces exactly the domain knowledge you need for scoping.

## Sources

- [What are projects?](https://support.claude.com/en/articles/9517075-what-are-projects)
- [Manage project visibility and sharing](https://support.claude.com/en/articles/9519189-manage-project-visibility-and-sharing)
- [Organize your tasks with projects in Claude Cowork](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)
- [RAG for projects](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects)
- [Skills explained: Skills vs prompts, Projects, MCP, subagents](https://claude.com/blog/skills-explained)
