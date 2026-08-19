---
type: concept
status: draft
updated: 2026-08-19
aliases: [claude code SDK, headless claude code, agent SDK]
tags: [claude-code, agents, tooling]
---

# Claude Agent SDK

Running Claude Code via API/CLI, without an IDE — Luís's description, in
[[2026-08-19 1-1 Matheus - Luís]]: *"Isso é você executar o cloud code via
API, né? Via linha de comando[...] Você consegue executar ele sem ser com uma
IDE. E é o cloud Code mesmo, ele baixa o projeto e você executa ele ali."*

Introduced as a topic msilva should start tracking, not something built or
scoped yet. Luís is already experimenting with it for his own purposes —
mentioned wanting to use it somewhere else in the company (unclear reference,
transcribed as *"a dinda"*) — and has already run some tests.

## Why it matters here

Not yet tied to a specific [[Agent Flow]] agent, but it's the natural
execution substrate for any agent in that architecture that needs to *run*
Claude Code rather than have a human drive it interactively — most obviously
**A3 Executor** and **A9 Developer**, both of which orchestrate coding work.
Luís drew an explicit line between this and the dev-subagent question Carol
raised the same call: subagent design is project-harness detail (see
[[Agent Flow]]); this SDK is the thing that would invoke that harness
programmatically instead of a human opening an IDE.

## Open questions

- What is Luís actually testing it for? Not stated beyond the unclear "a
  dinda" reference.
- Does this change how [[Agent Flow]]'s A3/A9 would be built — invoking Claude
  Code headless, rather than each agent being its own bespoke orchestration?
- Nobody on the team has reviewed the official docs/API surface yet, per this
  transcript.
