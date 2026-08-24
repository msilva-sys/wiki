---
type: meeting-prep
status: active
updated: 2026-08-24
aliases: [one pager, first agent one pager, first agent prep]
tags: [agents, planning, decision, meeting-prep]
---

# First agent to build — decision needed before 2026-08-24

> [!danger] A5 deprioritized entirely — msilva, 2026-08-24
> [[2026-08-24 Deprioritize A5 Watcher as first-agent candidate]]: A5 is just
> one of 14 agents and gets no special focus going forward, as a candidate or
> as active design. The recommendation and comparison below still argue A5's
> case as of 2026-08-18 — kept as reasoning history, not live.
>
> **Open question this page can't answer on its own**: this page's meeting
> ("end of the week beginning 2026-08-17", date never filled in) may never
> have happened — nothing elsewhere in the wiki records it. If it didn't,
> the whole recommendation below is stale, not just the A5 point, since the
> broader "which agent first" conversation moved on considerably by
> 2026-08-20 (see [[Which agent should be built first]]). Confirm with
> msilva rather than assuming either way.

> [!note] Meeting date not yet recorded
> Set for "the end of the week beginning 2026-08-17" with Gabrielle and Luís. Exact
> date unknown — fill it in and rename this page to carry it. Full reasoning:
> [[Comparing the first-agent candidates]].

**The ask:** agree which agent I build first. **Gabrielle is on leave from 2026-08-24
for ~2.5 weeks**, so without a decision this slips about three weeks.

## Where we are

- The deliverable is *one agent actually built*, plus a proposal for which one.
- On 2026-08-10 the suggestion was **A5 Watcher**. It was chosen for being **least
  likely to fail** — it needs no human initiative.
- The criterion is now **most utility**. That is a different test, and it gives a
  different answer.
- **Nothing is decided.**

## Recommendation — A1 + A2, the intake pair

Capture from every channel → classify (type · scope · complexity · risk) → route.
**Humans are the three branches in v1.**

Four reasons, the first two comparative rather than absolute:

1. **A5 has no date, and it now has two blockers rather than one.** It needs traffic
   through the proxy: GC-5 must land, then Orca must migrate — that migration isn't
   scheduled, and isn't even mentioned on [[Orca Next Version]]'s own roadmap. And
   the **n8n flows people build for themselves are also meant to go
   behind the proxy** (Gabrielle, 2026-08-18) — but nobody knows how many there are and
   **each author holds their own Airtable key**, so that population needs centralized
   key distribution first, and nobody owns finding them.
2. **A7 can't be evaluated.** Its value is value-per-PRD × how often we start new
   projects, and **nobody has that number.** A1 + A2 is the build that produces it.
3. **The business has already asked for this shape.** In the 2026-08-17 weekly, the
   matriz/external-events problem was proposed as *"um agente para tá ali no meio"*
   — interpret, validate, then write. That is A1 + A2 in miniature, arriving from a
   real problem rather than from the architecture diagram
   ([[2026-08-17 Weekly - Projetos e Tarefas]]).
4. **Half of it is already built here.** Claude routinely writes our Linear backlogs
   from documentation — project description, milestones, issues, subissues, using Luís's
   templates. So **A2's remaining work is classification and routing, not writing
   tickets**, and the target format already exists in-house. This is the smallest of the
   four builds, not the largest ([[Linear Project Structure]]).

It is also the only candidate that is a **working system with no other agent
present**, and it attacks the documented reason the last agent failed: it was never
called, because it sat on one channel waiting to be @-mentioned.

**The cost, stated plainly:** this departs from *anarchic-first* — limbs before
spine. That is a real disagreement with the project instruction, and I would rather
raise it than work around it.

## The four candidates

| Candidate | Best thing about it | Worst thing about it |
|---|---|---|
| **A1 + A2** intake | Works alone; measures what actually arrives; **ticket-writing already solved in-house**, so it's the smallest build | Departs from anarchic-first; people must still send things |
| **A5** Watcher | Zero adoption risk; best-specified; one watcher covers every service behind the proxy — **including everyone's n8n flows, which nobody is watching** | **No date, and two blockers** — GC-5, **[[Orca (CDE)]]'s own team not observable yet** (nothing deployed on their next-version roadmap as of 2026-08-17 — the *current* Orca system is in production), and the n8n population needs key distribution plus a discovery step nobody owns |
| **A7** Discovery | Highest ceiling — spec quality bounds everything downstream | Can't be chat-only, so it drags A6's retrieval layer in with it |
| **A4** Teacher | Saves the most human time; the only automation on the enablement front | Vaguest spec; needs people to opt in; may duplicate the Claude Code training programme |

## Sequence I'd propose

**A1 + A2** on the machine and existing-traffic channels → **A5** once GC-5 lands, its
deduplication logic becoming the seed of A13 → **revisit A7** with a real
project-start rate in hand.

## Decisions I need from you

1. **Utility or lowest-risk?** And by utility do you mean *value if it works*, or
   *expected value* — value × chance it works × how often it runs?
2. **Is departing from anarchic-first acceptable** to start at intake? *(Related: you
   suggested Agent Flow gets its own initiative with each part a project — that
   structure is anarchic-first. Starting at intake cuts against it.)*
3. **Is Orca's proxy migration scheduled?** Not on [[Orca Next Version]]'s roadmap
   as written. This is what decides whether A5 can be dated at all.
4. **How often does the area start a new project?**
5. **A6 split** — retrieval as shared tooling now, curation as an agent in phase 2?
6. **Who owns finding the n8n flows that hit Airtable?** You said nobody knows how many
   there are and each person holds their own key. That discovery step is a prerequisite
   for both key distribution and any A5 coverage of them, and it currently has no owner.

~~6. **Can agent output file into Linear** while the migration is still bedding in?~~
**Answered 2026-08-18** — Claude already writes our project descriptions, milestones and
issues into Linear as routine practice. Dropped, and replaced above.

## Two things that would change my answer

- **If "utility" means value-if-it-works → A7**, not A1 + A2. I would then need the
  retrieval layer built first, which is a materially bigger first build.
- **If Orca becomes observable soon → A5** gets a date and becomes competitive again
  on exactly the grounds it was first suggested. As of 2026-08-17 nothing is deployed
  on [[Orca Next Version]]'s roadmap; that project is targeted to close 2026-08-28,
  but the proxy migration itself isn't on it — see [[Orca (CDE)]].
