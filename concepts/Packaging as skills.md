---
type: concept
status: active
updated: 2026-08-18
date: 2026-08-17
aliases: [skills, packaging, skill packaging, distribution]
tags: [agents, skills, tokens, sharing, claude]
---

# Packaging as skills

**Wrapping a capability into a self-contained, distributable unit** — in practice,
a Claude *skill*: instructions plus any scripts, versioned as files, shareable, and
invocable without dragging its context along.

Filed as a concept because it has arrived independently from **six sources**
without anyone setting out to establish it, and because it appears to answer two
problems that were being treated as separate.

## The claim

**Packaging is simultaneously the token-cost lever and the sharing mechanism.**

- **Cost** — a packaged capability fetches what it needs rather than being handed
  everything. Narrow retrieval is a design decision made at packaging time, not a
  tuning step afterwards.
- **Sharing** — a skill is a file. It can be sent, versioned, reviewed, and run by
  someone who didn't write it.

Same mechanism, two problems. That convergence is what makes it worth a page
rather than a bullet.

## The evidence

| Source | What it shows |
|---|---|
| [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] | The clearest natural experiment. **The component packaged as a skill works** (report audit, ~2 min); **the unpackaged one blows context and burns tokens.** Same author, same system, same day |
| ⇧ (part 2) | The same skill had **already been shared with his team** — packaging solved distribution without anyone planning it |
| [[Gabriel Packer - DAG-driven agent orchestration]] | An entire production system built from four skills — `orca-linear`, `orca-cli`, `orchestration`, plus one of his own |
| [[Gabriel Packer - solo founder AI workflow (part 1)]] | *"Write agent instructions to files, not memory. Memory compacts and agents forget context mid-task; files persist and every agent reads the same source of truth"* |
| [[Sharing the accounting automation with the team]] | Distribution options worked through for the accounting case; skills among them |
| [[Sharing via Projects - the accounting project]] | Projects as the container packaged work lives in |

The Gabriel case is the strongest because it is a controlled comparison: two
components, one packaged and one not, and the packaged one is the one that works.

## Why narrow fetching is the cost lever

msilva's diagnosis, 2026-08-17: an agent handed an entire 816-row Airtable table
plus an uploaded spreadsheet, when it needed slices of both. The fix proposed was
not a smaller prompt but **a skill that fetches only what's needed** — *"ensinar o
agente a aprender só o que você quer, que aí não estoura esse contexto."*

> [!warning] Packaging is not the whole cost story — and the numbers don't settle it
> The same investigation found a run of that flow cost **11 centavos** against ~$7
> earlier. ~~Cost is **intermittent**, which points at a **runaway execution** — two
> workflows firing concurrently — rather than context volume as the primary cause.~~
>
> **Struck 2026-08-18**: the author switched LLM provider between the two runs, so the
> delta isn't attributable to variance — see
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]. The runaway-execution
> hypothesis still stands on two people's independent judgement, but **not on this
> measurement.**
>
> The structural point is unaffected, and it is the one this page needs: **packaging
> addresses a fixed context premium; it does nothing about a broken trigger chain.**
> Different problems, different fixes, and packaging only reaches one of them —
> regardless of which turns out to dominate the bill.

## What packaging does *not* solve

**Maintenance.** This is the gap that survives. Gabriel's team has his skill — but
whether anyone other than the author can debug it is untested
([[Agent Flow]]). Distribution and maintainability are not the same property, and
the wiki has evidence only for the first.

Two things that plausibly close it, neither yet tried here: a **README carrying the
four-field spec** (inputs/outputs · consults/feeds · success criteria · limits),
and **tests**. Both are why [[How to implement A5 Watcher]] puts A5's triage logic
in `skill/` with a spec and a test directory beside it, rather than inline.

**Isolation.** A shared runtime undoes some of the benefit: on the finance team's
shared n8n licence, one person's stuck execution blocked debugging entirely and
crowded others' logs out of view. Packaging the logic doesn't isolate the runtime.

> [!note] Partly a settings problem, not purely a tenancy one — 2026-08-18
> One of the three symptoms had a mundane cause: **execution logs were not being
> saved at all**, and the team turned saving on for both failures and production runs
> ([[2026-08-18 Save n8n execution logs for audit]]). So *"crowded others' logs out of
> view"* overstated it — some of that history did not exist to be crowded.
>
> The stuck-lock and no-per-user-filtering halves stand, and they are genuine tenancy
> problems. **Worth separating**, because it narrows what packaging is being asked to
> solve: the runtime still isn't isolated, but observability on a shared runtime turns
> out to be configurable rather than structurally lost.
>
> More precisely still (transcript read 2026-08-18): **failed executions were being
> saved all along; successful production ones were not.** So the history wasn't crowded
> out — for successful runs it never existed. Per-user filtering is the real tenancy
> defect: the list is searchable by name only and sorted by recency, which is what
> pushed one person's runs out of view.

## Consequences already acted on

- A5's triage logic is specified as a **skill**, not inline code —
  [[How to implement A5 Watcher]].
- The agents monorepo keeps `shared/` **empty until two agents need the same
  thing**, so extraction follows evidence rather than prediction.
- `contracts/bug-sistema.md` is written first: the contract is the packaged
  interface between agents that don't exist yet.

## Open questions

- **Does packaging survive complexity?** Every example here is small — a report
  audit, a Linear wrapper, a worktree manager. Nothing tests a skill doing
  something genuinely large.
- **Where do skills live** once there are several — the agents monorepo, a Project,
  or somewhere the whole company can reach? Bears on
  [[Sharing via Projects - the accounting project]].
- **Cross-area sharing** is unexamined. Within a team it works; between areas
  nobody has tried.
