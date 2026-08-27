---
type: concept
status: draft
updated: 2026-08-24
aliases: [harness template, trigger input harness output, agent template (Luís)]
tags: [agent-flow, architecture, harness, claude-agent-sdk]
---

# Agent Harness Template

A generic shape for specifying any agent, proposed by Luís Fernandez in a
Slack message to msilva, 2026-08-21 (no `raw/` file — pasted directly into
the working chat, not a transcript):

```
Trigger (Human | Machine)
Trigger Channel (Slack | Webhook | Cron | etc)
Input
====================
AGENT PROCESS
- Harness
---- Soul.md (como deve se comportar, falar etc)
---- Skills
---- Tools
---- MCP
---- etc
====================
Output (result, changed systems, etc)
```

## How this connects to what's already in the wiki

**Refines the actor→input→output method.** [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]] has Luís proposing that every entity in [[Agent Flow]] be specified only as actor → input → output, and to stop designing the inter-agent graph. This template is the same idea with more resolution: the "actor" side splits into **Trigger type** (Human vs. Machine) and **Trigger Channel** (Slack, Webhook, Cron, etc.) — a distinction the earlier method didn't carry. Between input and output sits the part the earlier method deliberately left as a black box: the **Harness**.

**Gives "harness" a concrete shape for the first time.** The word had shown up twice before this, in two different senses:

- [[2026-08-19 1-1 Matheus - Luís]]: dev-subagent design is *"100% harness de projeto"* — the target project's own setup (CLAUDE.md, skills, tooling), decided per-project, not something [[Agent Flow]]'s 14-agent diagram needs to specify.
- [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]: A12 (Data Gov) and A13 (Deduplication) are each called a *"harness"* — a rule-enforcement/guardrail layer, closer to an automated gate than to something that reasons.

This template resolves both into one consistent concept: **Harness = Soul.md + Skills + Tools + MCP**, the substrate any agent (or project) runs on, regardless of who or what triggers it. The first usage was this concept applied at the project level; this message generalizes it to every agent in the architecture.

> [!tip] A candidate reading of the A12/A13 "harness" call — msilva's own synthesis, not confirmed with Luís
> If Harness = Soul.md + Skills + Tools + MCP, then calling A12/A13 a "harness"
> rather than an agent may not be loose language — it may mean they run mostly
> on Skills + Tools, with little or no Soul.md (no autonomous
> reasoning/behavior layer). That would line up with msilva's own
> **agir/informar** heuristic ([[2026-08-20 1-1 Matheus - Luís]]): something
> that only enforces a rule, rather than acting or reasoning over it, reads as
> a skill/harness component, not a standalone agent. Not tested against the
> rest of the 14-agent list, and not run past Luís as a reading of his own
> word choice.

**Substrate this would presumably run on**: [[Claude Agent SDK]], introduced by Luís on 2026-08-19 as running Claude Code via API/CLI without an IDE — the mechanism an agent would use to *invoke* a harness programmatically. Not yet tied together explicitly by either of them.

**Not every agent gets its own standalone harness.** msilva ([!msilva] callout, 2026-08-21): some of the 14 agents don't need this template applied to them at all — they may instead be invoked as a Tool/Skill *inside* a parent agent's harness, with no standalone Trigger/Input/Output of their own. This confirms, rather than just echoes, the "maybe just a skill" flag already on A4 Teacher and the transversal group above: those agents may not be top-level entries in [[Agent Flow]] at all, but components nested inside another agent's Skills/Tools layer.

**Trigger Channel sits alongside, not in place of, the four entry channels.** msilva ([!msilva] callout, 2026-08-21): Trigger Channel (Slack, Webhook, Cron, etc.) is a separate axis from [[Agent Flow]]'s four entry channels (`Bug sistema` · `Bug manual` · `Tarefa` · `Consultoria`) — both coexist in the model rather than one replacing the other.

**Reforçado ao vivo, não só por escrito.** [[2026-08-26 1-1 Matheus - Luís (agentes A10-A14)]]: Luís levanta a mesma distinção trigger/input de novo, em conversa, especificamente sobre A10/A14 — não é um conceito isolado do Slack de 2026-08-21, é como ele pensa sobre agentes de forma consistente.

## Leituras externas relacionadas (ingeridas 2026-08-24)

Duas clippings do mesmo lote (`raw/Clippings/`, capturadas 2026-08-21) usam
"harness" de formas complementares a este template, não idênticas:

- [[Harness engineering for coding agent users]] (Böckeler/Martin Fowler) —
  descreve o *ciclo de controle* (guias feedforward + sensores feedback,
  computacional vs. inferencial), não a composição do harness. Sua categoria
  "behaviour harness" — o comportamento funcional do sistema — é o problema
  mais difícil de todos, segundo a autora, e é onde A7→A8→A9 (o pipeline de
  projetos) realmente vive.
- [[How to Build a Custom Agent Harness]] (Runkle/LangChain) — descreve
  **como construir** um harness via middleware plugável.
  `HumanInTheLoopMiddleware` e `SubAgentMiddleware` são infraestrutura já
  nomeada para duas coisas que o desenho de 14 agentes ainda trata como
  questão em aberto: onde ficam os gates humanos, e como A3/A9 criam
  sub-agentes sob demanda.

Nenhuma das duas resolve qual substrato roda o Harness de Luís (Claude Agent
SDK vs. LangChain `create_agent`) — ficam como vocabulário/comparação, não
como resposta.

## Open questions

- Which of the 14 agents are top-level (their own Trigger/Input/Harness/Output) vs. nested inside another agent's harness as a Tool/Skill — and for the nested ones, whose harness do they belong to? Not yet run past Luís.
