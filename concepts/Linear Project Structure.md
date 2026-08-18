---
type: concept
status: active
updated: 2026-08-18
aliases: [Linear structure, initiatives and projects, milestones, Liner]
tags: [linear, process, project-management]
---

# Linear Project Structure

How msilva's area organizes work in Linear. Recorded because
[[2026-08-14 Migrate project management from Jira to Linear]] told msilva to
restructure the [[Airtable Proxy]] issues **however he sees fit**, and left the
question of *shape* entirely open. This page is the shape.

Source: [[2026-08-18 1-1 Matheus - Gabrielle]], where Gabrielle walked the whole
method. Note that page's caveat — the raw file is Gemini's paraphrase, not a
transcript, so there are no verbatim quotes anywhere below.

## The hierarchy

| Level | What it means | Stated by |
|---|---|---|
| **Initiative** | The **product or solution** | Gabrielle, 2026-08-18 |
| **Project** | A **specific segment of value delivery** within the initiative | ⇧ |
| **Milestone** | A grouping unit for deliveries inside a project's backlog | ⇧ |
| **Issue / task** | The unit of work. What the status readout counts | ⇧ |
| **Subtask** | Breakdown inside an issue. **Invisible to the status readout** | [[AI status reporting on Linear]] |

The initiative/project distinction is the part that was previously being inferred.
It is not "big thing / small thing" — it is **solution / delivery segment**, which
is why one initiative can hold projects that look unrelated.

### The worked example: Airtable governance

The **Airtable governance** initiative holds three projects:

1. the [[Airtable Proxy]];
2. **expansion to other applications**;
3. a **Livemode Data Hub**.

Read against the definition, this says the *solution* is governing Airtable access
company-wide, and the proxy is only the first delivery segment of it. That matches
msilva's own internal framing of the proxy as company-wide infrastructure rather
than a [[LiveScript]] fix ([[2026-08-14 Recap da Semana]]) — the initiative
structure is where that intent is actually encoded.

## How work enters

1. **Business rules and context are documented first.** The documentation is the
   input to the roadmap, not a byproduct of it.
2. Roadmap and backlog are built in Linear from that documentation.
3. The backlog is organized into **milestones**, which group deliveries.
4. Tasks and subtasks hang off those.

Step 1 is worth noticing: the AI status readout independently identified
*large-scope projects advance slowly at the start, with early effort going into
documentation*, and Gabrielle called it a real pattern
([[AI status reporting on Linear]]). That is not a dysfunction the readout found —
it is the method working as designed. The readout measured the process and reported
it as a problem, which is its own small lesson about giving an agent a model of
progress that matches how the team actually works.

## Monitoring

- A **centralized board** pulls milestones and tasks across multiple projects.
- Visibility is deliberately kept at **issue level** for clarity; anything more
  specific means opening the project itself.
- Project structure is **actively tuned**, not set once — see below.

### Restructuring to make the readout accurate

The team split **"Evolução do Front-End"** from **"Estabilização"** into separate
projects. The stated reasons:

- a more accurate read of **completion percentage** — one project mixing new
  front-end work with remediation produced a number that described neither;
- easier **bottleneck identification**.

This is the same problem [[AI status reporting on Linear]] documents from the other
end. The readout reported *Fronte de Negócios* at **6%** against an actual 20–25%,
and Luís said the 6% frightened more than reality did. The fix the team reached for
was **not** fixing the agent — it was reshaping the board so the agent's arithmetic
comes out right.

> [!important] The direct lesson for msilva
> Board structure is a **reporting instrument**, and it is treated as adjustable.
> Two conventions follow, and they point the same way:
> - **Split projects along "new work" vs "fixing earlier work"** so the percentage
>   means something.
> - **Keep issues flat**; subtasks do not roll up. Park found-but-unprioritized
>   work in Luís's `Triagem` column instead of burying it
>   ([[2026-08-17 Weekly - Projetos e Tarefas]]).
>
> He is restructuring the proxy backlog this week, and this readout is what
> Gabrielle and Luís see while she is on leave from 2026-08-24.

## Teams as the isolation boundary

**Freelancer projects live in separate Linear teams**, for security and access
control. Teams are the tenancy unit here, not a labelling convenience.

Relevant beyond staffing: it is the only access-control mechanism in the area's
tooling that has been described at all. If an [[Agent Flow]] agent is ever given
Linear credentials, the team boundary is what scopes it.

## Templates

Luís authored **Markdown templates** for ticket types — **bugs, spikes, epics** —
to keep tasks and subtasks consistent in both documentation and flow.

This is the concrete local instance of something [[Agent Flow]] treats as a design
input: *the ticket is the prompt*
([[Gabriel Packer - DAG-driven agent orchestration]]). A1 and A2 would need a
target shape to write into, and Packer's ticket template was the only candidate on
record. **There is now an in-house one.** Whether Luís's templates are rich enough
to drive an agent — scope, explicit non-goals, affected files — is unknown and
worth reading before designing A2's output.

## Slack integration

Linear projects can be wired to specific Slack channels, pushing progress updates
and task movements automatically. Raised as available, **not configured**.

Worth carrying forward: an agent that needs to report to humans may not have to
build a notification path. If A2 writes a Linear issue, the existing integration
delivers it. See [[Agent Flow]] and [[How to implement A5 Watcher]], where
delivery-channel choices are otherwise treated as open.

## Plan constraints

- Free plan caps around **250 issues** — the original reason the Jira migration was
  postponed.
- The team is on a **Business trial expiring 2026-09-09**
  ([[2026-08-18 1-1 Matheus - Gabrielle]]).
- Gabrielle's stated preference is to stay on Linear.

> [!warning] The cap is not permanently gone
> [[2026-08-14 Migrate project management from Jira to Linear]] recorded the trial
> as having *removed* the constraint. It **suspended** it. The trial expires
> 2026-09-09; Gabrielle returns 2026-09-10. Migrating the `AIRTABLEGC` backlog into
> the workspace in that window is exactly what fills the cap.

## Open questions

- **What is the Linear team/project identifier, and what shape are the new issue
  keys?** Still unanswered as of 2026-08-18, now across three meetings.
- **Are Luís's templates usable as an agent output contract**, or are they human
  prose? Read them.
- **Which initiative does [[Agent Flow]] belong to** — its own, or somewhere
  existing? Never stated, and it determines where msilva files the research.
- **Is the Livemode Data Hub a real project or a placeholder?** It was named as a
  peer of the proxy inside the same initiative.
