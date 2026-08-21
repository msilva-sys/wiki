---
type: concept
status: draft
updated: 2026-08-21
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

## Open questions

- Does every one of the 14 agents map cleanly onto this template, or do some (the ones already flagged as "maybe just a skill" — A4 Teacher, the transversal group) turn out to have no real Soul.md at all?
- Is "Trigger Channel" meant to replace or sit alongside the four entry channels already named in [[Agent Flow]] (`Bug sistema` · `Bug manual` · `Tarefa` · `Consultoria`)?
