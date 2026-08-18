---
type: concept
status: active
updated: 2026-08-18
date: 2026-08-18
aliases: [primary sources, direct access, retrieval layer, context retrieval]
tags: [agents, architecture, retrieval, context, a6]
---

# Agents read primary sources

**An agent should reach the underlying data directly rather than wait for the agent
that was supposed to digest it.**

Filed as a concept because it arrived three times independently in
[[Agent Flow]] design work, each time as a local fix, before anyone noticed it was
one rule. It now decides a structural question — see *No fifteenth agent* below.

## The rule

Where a design has agent **X** consuming a summary produced by agent **Y**, and Y
does not exist yet, the fix is **not** to build Y. It is to give X query access to
whatever Y would have read.

Events tell an agent *when*. Query access lets it establish *what*.

## The three instances

| Instance | The design said | What was settled instead |
|---|---|---|
| **A5 Watcher** | Consume Grafana alert payloads | **Direct access to Prometheus, Loki and the observability stack.** Otherwise A5's quality is bounded by thresholds a human wrote — *"the intelligence migrates into Grafana"* ([[Which agent should be built first]], risk 4) |
| **A7 Discovery** | *"Discovery autônomo completo via chat estruturado"*, consulting A10/A11/A12 to validate | **Read the PRD corpus, `Brain`, Linear, the documentation Hub, the five systems' repos and meeting transcripts.** Raised by msilva 2026-08-18: chat alone makes A7 a bad-spec generator with good prose |
| **A1 Receptor** | Enrich from A6 (history) and A13 (duplication) | Neither exists. A1 v1 reads **Linear and the existing Airtable team board** directly |

Supporting evidence from prior art, arriving at the same place from a different
direction — a practitioner replying to
[[Gabriel Packer - solo founder AI workflow (part 1)]]:

> "memory compacts and agents forget context mid-task. files persist across
> sessions and every agent reads the same source of truth."

That is an argument for shared durable sources plus direct reads, and **against** a
retrieval intermediary.

## Why it matters: it is what makes anarchic-first possible

[[Fluxo Agêntico project instruction]] requires phase 1 agents to run
independently, *"sem dependência cruzada."* Read literally, that is impossible for
any agent whose spec has it consuming another agent's output — which is most of
them.

This rule dissolves that. **Cross-agent dependency is forbidden; depending on a
data source is not.** Substituting direct reads for a missing upstream agent is how
a phase 1 agent gets built at all.

## No fifteenth agent

Every phase 1 candidate needs the same substrate, and **no agent among the fourteen
owns it as a deliverable** — A6 Curator owns it as a *product*, in phase 2. msilva
asked on 2026-08-18 whether that calls for a new agent.

**It does not**, and this concept is the reason:

- **A retrieval agent would create a cross-agent dependency for A1, A5 and A7 at
  once** — exactly what phase 1 forbids. A shared *library* is not an agent
  dependency; a shared *agent* is. This is the decisive objection.
- **It would be that intermediary this rule exists to avoid.**
- **It duplicates A6**, and **A13 exists to detect and block duplicated work**.
  Proposing a second hub is the failure the architecture explicitly guards against —
  and the **`Brain` repository already does A6's job**
  ([[2026-08-14 Papo de Projetos]]), so there may be an implementation to read
  before writing one.
- **Retrieval carries no judgment.** An LLM hop adds tokens and latency to a
  deterministic tool call. Per [[Packaging as skills]], narrow fetching is a
  packaging decision, not an agent.

### What to build instead

A **shared tool layer** in the monorepo already decided on
([[How to implement A5 Watcher]]) — a `tools/` package beside `contracts/`, one tool
per source: Linear, `Brain`, the five systems' repos, the PRD corpus,
Prometheus/Loki. Each agent imports what it needs.

Precedent, from the prior art Luís pointed at: **Packer's production system is built
from four skills** — `orca-linear`, `orca-cli`, `orchestration`, plus one of his own
([[Gabriel Packer - DAG-driven agent orchestration]]). `orca-linear` is precisely a
retrieval tool for Linear, and it is not an agent.

Build it **thin and per-agent**, accumulating from real use rather than designed up
front. A1's v1 needs Linear and the existing Airtable board, and nothing more.

## Corollary: A6 is two jobs, and only one of them is an agent

| Half | Shape | When |
|---|---|---|
| **Retrieval / access** — read Linear, `Brain`, repos, PRDs, Loki | **Tools.** Deterministic, no LLM | Now, incrementally, per agent |
| **Curation** — what is worth remembering, cross-area redundancy, weekly synthesis | **Genuinely an agent.** Real judgment | Phase 2, as A6 |

A6 as drawn is four functions in one box ([[Fluxo Agêntico project instruction]]),
which is why it reads as a phase-2 monolith. Split it and phase 1 agents get what
they need **without A6 existing** — which is what anarchic-first was asking for.

## Open

- **Does Gabrielle accept the A6 split?** It reinterprets the architecture's
  centrepiece, so it is a spec disagreement to raise explicitly, not a detail.
- **What does `Brain` actually contain and expose?** Unread. It is the single
  highest-value thing to look at before building any retrieval, and it may already
  be the tool layer.
- **Access and permissions** are unexamined. Direct reads across Linear, repos and
  Loki mean an agent with broad read scope, and [[Which agent should be built first]]
  already flags Loki access as an unreviewed egress path.
