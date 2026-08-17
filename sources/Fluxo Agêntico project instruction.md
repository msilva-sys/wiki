---
type: source
status: active
updated: 2026-08-17
date: 2026-08-17
aliases: [instrucao projeto fluxo agentico, project instruction, AI-First]
source: "raw/instrucao-projeto-fluxo-agentico.md.txt"
tags: [agents, architecture, spec, ai-first]
---

# Fluxo Agêntico — project instruction

**The authoritative specification for [[Agent Flow]].** Titled *"Instrução do
Projeto: Fluxo Agêntico AI-First — Área de Projetos"*, created by Gabi
*(unverified: "Gabi" is ambiguous across the transcripts; Gabrielle Ferreira owns
this project, so almost certainly her)*.

This supersedes verbal descriptions and adds per-agent specification the diagram
only gestured at. Where they differ, this document wins.

> [!info] It is a Claude Project
> The export shows this is a **claude.ai Project** with project instructions and
> attached files (this document plus `fluxo_agentico_ajustado.html`, the same
> diagram transcribed at [[Fluxo Agêntico diagram]]). It reports *"2% da
> capacidade do projeto utilizada"* and has an unconfigured **Programado**
> (recurring tasks) section.
>
> That partly answers "where does it live": today, a Claude Project. It also
> means the intended working method is **one conversation per agent or per
> connection**.

> [!warning] Date unresolved
> No internal or filename date; filesystem timestamp is the download
> *(unverified: from file mtime)*. Content predates 2026-08-10.

## Philosophy: AI-First

> "A IA é o meio de execução, não apenas um assistente. Agentes autônomos
> executam tecnicamente — escrevem código, criam automações, fazem deploy,
> monitoram, ensinam — enquanto humanos atuam como aprovadores estratégicos e
> refinadores de qualidade."

**AI is the means of execution, not an assistant.** Autonomous agents write code,
build automations, deploy, monitor, and teach. Humans are **strategic approvers
and quality refiners** — not operators.

This substantially resolves the deterministic-versus-autonomous question msilva
raised in [[2026-08-14 1-1 Matheus - Gabrielle]]: the stated intent is autonomy,
with humans at approval gates rather than in the loop.

## Build strategy: anarchic first, integrated second

> "A estratégia de construção é anárquica primeiro, integrada depois."

- **Phase 1 — anarchic.** Each agent built **independently**, closed scope, tested
  in isolation. Priority is agents running in production *individually, even
  without integration between them*. Explicit rationale: fast validation and
  iteration **without cross-dependency** (*"sem dependência cruzada"*).
- **Phase 2 — integration.** Once the main agents are validated, wire them per the
  architecture: A1 as the single front door, A2 as router, the three branches
  receiving from the classifier. **A6 only then starts capturing context from
  everything**, and the transversal intelligences begin running in parallel.

## Architecture — per-agent specification

### Intake

| Agent | Specification |
|---|---|
| **A1 Receptor Universal** | Captures input from **any channel: Slack, Monday, email, forms, webhooks, system alerts**. Normalizes to a structured format, enriches with historical context from A6 and duplication context from A13. **Target: < 10 seconds** |
| **A2 Classificador & Decisor** | Multi-dimensional analysis: **type** (bug, task, automation, improvement, new project), **scope** (corporate vs. area), **complexity** (trivial→complex), **risk** (zero→high). Routes to one of three branches |

### Branches

| Agent | Specification |
|---|---|
| **A3 Executor** (operational) | Orchestrates bugs, automations, maintenance. **Creates sub-agents on demand — no fixed subordinate agents.** Validates results, **escalates to a human on failure**. Receives A5 feedback |
| **A4 Teacher** (enablement) | Teaches areas to build for themselves. **Diagnoses maturity L0–L3**, generates personalized tutorials, tracks execution in real time, records the area's level progression |
| **A7 Discovery** (projects) | Complete autonomous discovery via structured chat. Produces a PRD with user stories, technical requirements, proposed architecture, effort estimate, risk analysis, and **MVP-vs-complete options**. Consults A10/A11/A12 to validate. **Asks for human approval only at the end** |
| **A8 Orchestrator** | Takes the approved PRD and runs the project: setup, daily monitoring, risk and blocker identification, scope management, stakeholder status, replanning suggestions, go-live with handoff to operations |
| **A9 Developer** | PRD through to production. Creates sub-agents on demand. **Controlled stack: React, Python, Node.js, known APIs, N8n, Vercel/Replit.** Three modes by complexity. Failure handling: **3 attempts with different approaches, always ships something functional, never pretends it worked** |

### Monitoring

**A5 Watcher** — the most concretely specified agent in the document, and the one
msilva was advised to build first:

- **System health every 5 minutes**, usage patterns **every hour**, general report
  **every 24 hours**.
- **Incident detection**: systems down, recurring errors, failing workflows.
- **Opportunity detection**: unused features, frequent manual processes,
  redundancies between areas.
- Feeds A3 directly when it finds something actionable.

### Memory

**A6 Curator (Hub Central)** — all agents connect to it. Four functions:

1. **Institutional memory** — captures and organizes everything that happens.
2. **Continuous learning** — analyses patterns, improves tutorials, creates
   templates.
3. **Corporatização** — detects redundancies *between areas* and proposes
   unification.
4. **Strategic intelligence** — weekly reports, capacity alerts, pattern alerts.

### Transversal intelligences (run in parallel)

| Agent | Specification |
|---|---|
| **A10 Portfolio** | Analyses full backlog and capacity. Detects stalled items, misaligned prioritization, capacity bottlenecks, uncontrolled scope. Collaborates with A11/A12 before suggesting. **"Sugere, nunca executa"** |
| **A11 Product** | Real usage analysis: **DAU/WAU/MAU**, features used, time per screen, errors, abandonment. **"Investiga antes de recomendar."** Detects unused features, misused features, manual workarounds, unexpected patterns |
| **A12 Data Governance** | **Soft enforcement (alerts but permits)**: sensitive data, wrong format, low quality. **Hard enforcement (blocks)**: unauthorized external API, critical compliance export, **deletion of an entire base**. Continuous audit, real-time integration validation |
| **A13 Deduplication** | Detects and **blocks** duplicated work. Feeds context to A1 so in-flight or already-solved demands are caught **before entering the flow** |
| **A14 PM Agent** | Follows the client from request to delivery. Guarantees status visibility, communicates proactively, acts as automated point of contact |

## Connections — authoritative list

```
A1 → A2 → A3 / A4 / A7      routing by type
A7 → A8 → A9                projects flow
A5 → A3                     monitoring feedback
A5 → A10, A11, A12          feeds intelligences with monitoring data
A10 ⇄ A11 ⇄ A12             collaboration between intelligences
A10, A11, A12 → A9          consulted before project decisions
A13 → A1                    duplication context to the receptor
All → A6                    central memory and knowledge hub
```

## How the project is meant to be used

- **One conversation per agent, or per connection between agents.** Use the
  diagram as reference.
- When working on an agent, define: **its inputs and outputs**, which agents it
  consults or feeds, **its success criteria**, and **its limits — what it does
  not do**.
- Worked examples given: *"Preciso definir o prompt do A3 (Executor)"* and
  *"Como o A13 (Deduplication) deve se comunicar com o A1?"*

That four-part frame (inputs/outputs · dependencies · success criteria · limits)
is effectively the required template for the "understanding of each agent"
deliverable in [[Agent Flow]].

## What this resolves

- **The build order question** — answered: anarchic first, no cross-dependency.
  See the correction in
  [[What should the Agent Flow research phase study]].
- **`Bug (sistema)`** — A1 explicitly ingests *"alertas de sistemas"*, so system
  alerts are a first-class channel. The [[Airtable Proxy]]'s alerting is a
  candidate producer.
- **Deterministic vs autonomous** — intent is autonomy with human approval gates.
- **Dotted lines from the intelligences** — they go to **A9**, confirming the
  geometry reading in [[Fluxo Agêntico diagram]] over its stale comment.
- **A13 is not the only blocker** — A12 has hard enforcement too.

## Open questions

- **Monday is named as an intake channel**, but tracking is migrating to Linear
  ([[2026-08-14 Migrate project management from Jira to Linear]]). Is Monday
  still in use, or is this document stale on that point?
- **The controlled stack omits Go**, which the [[Airtable Proxy]] is written in.
  Does A9's stack constraint apply only to new systems?
- **What counts as "production" for a Phase 1 agent** running with no integration?
- **Which agent does msilva build first?** The document doesn't say; Gabrielle
  verbally suggested A5.
- Is the **Programado** (recurring tasks) capability intended as the scheduling
  mechanism for A5's 5-minute and hourly cycles?
