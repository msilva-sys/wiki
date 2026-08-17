---
type: source
status: active
updated: 2026-08-17
date: 2026-07-23
aliases: [gkpacker, packer workflow, waves, graph engineering]
source: "raw/Clippings/Gabriel Packer → visorfinance.app (@gkpacker) no X.md"
url: "https://x.com/gkpacker/status/2080306086653894733"
tags: [agents, orchestration, dag, linear, skills, prior-art]
---

# Gabriel Packer — DAG-driven agent orchestration

A post by **Gabriel Packer (@gkpacker)**, solo founder of visorfinance.app,
published **2026-07-23**: *"Meu workflow com IA como solo founder, parte 2"*.
Clipped 2026-08-17.

> [!important] This is almost certainly the model Luís shared
> In [[2026-08-14 1-1 Matheus - Gabrielle]] msilva describes a model *"de um cara
> postando […] no Twitter ou no LinkedIn"* that Luís found relevant for how it
> orchestrated information flow, adding *"ele tava usando orca, se eu não me
> engano."* This post uses Orca, orchestrates information flow, and Luís
> separately suggested **graph-based** approaches. It fits on all three counts.

**It resolves two open questions** — see the bottom of this page.

## The architecture

**An orchestrator that writes no code**, and implementation agents that do.
Packer uses **Fable as orchestrator** and **GPT-Sol as the implementation agent**;
he is unsure what to call the pattern — *"não sei se isso já tem nome, se é o tal
do Graph Engineering"* — and describes it as **waves**.

The orchestrator:

- reads the **entire project in Linear**
- fetches each ticket's context and relations
- **builds the dependency graph**
- splits the project into **execution waves**
- creates **worktrees in Orca**
- starts **one implementation agent per ticket**
- monitors PRs, CI, and reviews
- **releases the next wave as blockers are merged**

**Parallelism comes from the graph, not from ticket count.** Three tickets with no
blockers all run in wave one. A fourth that depends on them starts only once all
three PRs are reviewed and merged to main.

**Each wave starts from a freshly updated `origin/main`.** His stated reason:
avoid branches built on unmerged code, avoid needless conflicts, and stop CI
passing against a branch different from the one that deploys.

## The ticket is the prompt

The load-bearing idea. A ticket cannot be *"add support for recurring
transactions"* — it must be **a unit of work executable by an agent without
implicit context**. Every ticket carries:

- imperative, specific title
- the problem, and why it needs solving
- scope, **and what is explicitly out of scope**
- expected behaviour
- relevant technical detail
- modules, functions, and files affected
- acceptance criteria
- test scenarios
- dependencies, in `blockedBy`
- rollout strategy and kill switch where there's risk
- events and metrics to tell whether the feature works
- i18n, LGPD, and factory requirements where applicable

### Rules he applies when creating projects

- **never** a separate ticket for tests
- migration and schema live in the same ticket
- **no "foundation" ticket** full of functions for future use
- every ticket produces one reviewable PR
- tickets over 5 points get broken up
- target: non-trivial PRs under **~400 lines**
- a risky feature is born with rollback and observability
- **the dependency graph is created explicitly, never inferred from titles**

## Built from four skills

Three official, one his own:

| Skill | Job |
|---|---|
| `orca-linear` | The ticket is the source of truth. Reads full context, moves the task to In Progress, attaches the PR, comments the result, moves to In Review |
| `orca-cli` | Creates and organizes worktrees, starts the agents' terminals, maintains project hierarchy |
| `orchestration` | Turns tickets into tasks with dependencies, dispatches to each agent, maintains the coordinator↔worker protocol |
| `orca-project-orchestrator` (his) | Combines the rest: Linear, DAG, worktrees, dispatch, supervision, PRs, releasing the next wave |

## The per-ticket cycle

The orchestrator builds a **self-contained prompt** with the full spec and the
delivery workflow. The implementation agent then: moves the ticket to In Progress
· reads relevant files before editing · **treats implementation and tests as
distinct workstreams** · implements only the ticket's scope · runs affected tests
and pre-commit · fixes failures · commits and pushes · opens a PR against main ·
links it to Linear · moves the ticket to In Review · reports back.

The orchestrator monitors throughout, and notably **distinguishes a real CI
failure from a run cancelled by a push**, checks the PR stayed in scope, and
triages automated reviews (he uses Code Rabbit and Codex).

**It never merges on its own** — *"aqui ainda prefiro ter um quality gate humano
(meu review)"*. On merge, the orchestrator refreshes `origin/main`, recalculates
which blockers are satisfied, and launches the next wave.

## The line worth keeping

> "Obviamente colocar mais agentes não corrige uma especificação ruim. Ter um bom
> contrato de API, um grafo explícito e quality gates permitem escalar a execução
> sem perder controle da qualidade."

More agents don't fix a bad specification. An explicit graph and quality gates are
what let execution scale without losing control of quality.

## What this resolves

**Luís's "gráfis" — resolved.** [[2026-08-14 1-1 Matheus - Gabrielle]] recorded
*"o Luiz já deu umas ideias de questão de gráfis"*, flagged unverified because the
transcription was garbled. Given Luís pointed at this post, he meant a
**dependency graph / DAG driving execution order** — not a graph database or a
general framework. That's now a concrete, worked suggestion rather than a guess.

**The Orca ambiguity — resolved.** Two unrelated things share the name:
Livemode's **Orca** is a business-critical machine-learning system
([[2026-08-14 Papo de Projetos]]); Packer's **Orca** is the tool creating git
worktrees and running agent terminals. Don't conflate them.

## Why it matters to [[Agent Flow]]

This is a **working implementation of the projects branch** — A7 → A8 → A9 —
running in production for one person:

| Agent Flow | Packer's equivalent |
|---|---|
| **A7 Discovery** — generates a complete PRD | The ticket template above. His spec discipline *is* the PRD standard |
| **A8 Orchestrator** — orchestrates projects | Fable: DAG, waves, dispatch, PR/CI monitoring |
| **A9 Developer** — develops to production, sub-agents on demand | One implementation agent per ticket; parallelism from the graph rather than on-demand spawning |

Four further points of contact:

- **Linear is the source of truth** — and Livemode is migrating to Linear
  ([[2026-08-14 Migrate project management from Jira to Linear]]). The alignment is
  accidental but useful.
- **A human quality gate at merge** matches the AI-First stance in
  [[Fluxo Agêntico project instruction]] — humans as strategic approvers, not
  operators.
- **Everything is skills.** Third independent convergence on packaging, after the
  accounting distribution work and the Gabriel session
  ([[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]]).
- **Spec quality bounds agent quality.** A7 is the highest-leverage agent in the
  architecture on this evidence, not A9.

## Open questions

- Are `orca-linear`, `orca-cli`, and `orchestration` publicly available, and what
  do they actually require to run?
- The model does **one agent per ticket**; A9 is specified to **create sub-agents
  on demand**. Different decompositions — is one better for Livemode's work?
- Packer is a solo founder with a single codebase. How much survives a
  multi-team, multi-system environment?
