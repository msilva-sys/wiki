---
type: source
status: active
updated: 2026-08-20
date: 2026-08-18
aliases: [akita hot take, harness engineering bullshit, spec-driven development critique, ai-memory]
source: "raw/Clippings/Hot take Harness, Loop Engineering, Graph Engineering são Bullshit.md"
url: "https://akitaonrails.com/2026/08/18/hot-take-harness-loop-engineering-graph-engineering-sao-bullshit/"
tags: [agents, orchestration, spec-driven-development, harness, prior-art, opinion]
---

# Fabio Akita — Harness, Loop and Graph Engineering are bullshit

Blog post by **Fabio Akita**, published **2026-08-18**, expanding on [a tweet](https://x.com/AkitaOnRails/status/2089734682325794897).
Clipped 2026-08-20. An opinion piece, not a design doc — treated as a strong,
receipted counter-argument rather than settled fact, per the schema's rule on
weighing sources.

## Core claim

When a technology commoditizes, money migrates to taxonomy: real, small
practices get renamed into "disciplines," which justifies courses and
certifications that expire every six months. Backed with his own data — 30+
public repos, an LLM coding benchmark, and a direct multi-agent experiment —
rather than just asserted.

## Harness Engineering

**Harness choice barely moves frontier-model output quality**, but matters a
lot for weak models and for cost:

- Weak model + good harness rescues it (Grok 4.3: 18pts naked in OpenCode →
  55pts in its own CLI). Frontier model + any harness: noise (Grok 4.5: 92 vs.
  91 across two harnesses).
- **Cost is where harness actually bites**: Grok 4.6 ran $1.19 in its native
  CLI vs. $6.33 via OpenRouter for the same ~11M tokens — 5x, from native
  provider caching, not "harness engineering."
- His verdict: *"harness bom é o que cobra menos e não atrapalha. O resto é o
  modelo."* Choosing a decent harness is an afternoon of reading docs and a
  token invoice, not a discipline with a learning track.

Also addresses **Hermes Agent** (Nous Research's open-source framework for
building your own personal agent — tools, loops, model routing, local/cloud
fallback) as the extreme case: it's a second job to maintain, and what it
actually solves — cross-session/cross-tool continuity — a decent harness plus
his own **ai-memory** already covers, without becoming your own
infrastructure administrator.

## Loop Engineering & Graph Engineering — real but small

**Loop Engineering** (agent loops: execute → verify with evidence → iterate
until a stop condition) is **test and review**, fifty years old, under a new
name. **Graph Engineering** (explicit node/branch/join workflow graphs, per
LangGraph) is legitimate *when the flow is genuinely branching* — most
projects are a straight line with an `if` in the middle, and modeling that as
a graph is buying a whiteboard to draw one arrow. Notably: even the serious
guides for both concepts already carry this caveat ("you probably don't need
this") — the distortion comes from the sales funnel dropping the caveat, not
from the guides themselves being wrong.

## The strong argument against heavy multi-agent orchestration

- **The compounding-failure math**: a 10-agent pipeline at 90% per-step
  accuracy (optimistic) succeeds ~35% of the time (0.9^10). Every node is a
  new failure point; every edge is tokens spent on agents talking to agents
  instead of working.
- **He measured it directly**: MiniMax M3 under an orchestrator scored 24
  (Tier D); the same model, unorchestrated, scored 91 (Tier A) — a 69-point
  swing from harness/plumbing alone. His benchmark's best overall score (96)
  came from one strong model in a simple loop, no orchestration.
  A prior experiment (April 2026, [strong-model-orchestrating-cheap-model](https://akitaonrails.com/2026/04/25/llm-benchmarks-vale-a-pena-misturar-2-modelos/)):
  **no multi-agent combination beat Opus alone** on a cohesive build task —
  planner and executor end up sequential with tripled latency, not actually
  parallel.
- **External corroboration**: Cognition ("Don't Build Multi-Agents",
  https://cognition.com/blog/dont-build-multi-agents, now read directly at
  [[Don't Build Multi-Agents]]) — every action carries implicit decisions
  other agents can't see, so no amount of individual reliability fixes
  divergence. Anthropic's own multi-agent research system post
  (https://www.anthropic.com/engineering/multi-agent-research-system, now
  read directly at [[How we built our multi-agent research system]]) admits
  **15x token cost**, that coding is a poor fit for multi-agent (most coding
  work isn't genuinely parallelizable), and that 80% of their measured
  improvement came from spending more tokens, not from the architecture. A
  Berkeley study of 86 production systems across 26 domains
  (https://arxiv.org/abs/2512.04123) found **68% of production agents run
  ≤10 steps before human intervention** — real production usage is
  supervised simple loops, not orchestrated constellations.
  > [!note] Both citations read directly, 2026-08-20 — the Anthropic citation was selective
  > This post cites only the Anthropic post's caveats. Read directly, that
  > post reports its multi-agent system **beat single-agent Opus 4 by 90.2%**
  > on their eval — it's a build report for a system that worked within a
  > defined fit (parallel, independent, high-value, non-coding), not a
  > blanket warning. See the fuller comparison on [[Agent Flow]] and on
  > [[How we built our multi-agent research system]] itself.
- **Where orchestration is legitimate**: genuinely parallel, independent bulk
  work (map-reduce, no new name needed), and unattended overnight runs holding
  credentials — there, independent verification and a hard budget are a
  security control, not a style preference.
- **Pull quote**: *"cada agente a mais multiplica os modos de falha. Se o
  resultado do seu sistema muda quando você troca o orquestrador, o seu
  sistema é o orquestrador — e o modelo era figurino."*

## The strong argument against Spec-Driven Development (SDD)

- **The core problem**: a spec detailed enough to generate correct code *is a
  program* — just written in prose, and prose doesn't compile. Every
  ambiguity is a bug no compiler catches, in an environment with no test, no
  linter, no feedback.
- **Specs rot at the first hotfix**: production bug gets patched directly in
  code, the spec silently becomes a lie. Calling it "source of truth" doesn't
  change that incentive.
- **Historical parallel**: UML/MDA promised "code generated from the model,"
  20+ years ago, and collapsed for the identical reason — the model was never
  the reality, the code was. "SDD is MDA with an LLM attached."
- **Thoughtworks put SDD in "Assess," not "Adopt,"** on their Technology
  Radar, after watching tools inflate small tasks into ceremony — echoing
  Rich Sutton's Bitter Lesson: handcrafted structure loses to scale, always
  has.
- **Temporal inversion**: agile's lesson was that you discover what you want
  by *building* — and LLMs just made iteration cheaper than ever. SDD proposes
  expanding the planning phase at exactly the moment iteration got cheap.
  Backwards, on both counts.
- **His own position, stated elsewhere and repeated here**: *"software
  emerge, não se planeja."* The important features of his own project(s)
  came from problems that appeared mid-build, not from anything a spec
  predicted.
- **Important nuance he keeps, not a blanket dismissal**: even pro-SDD
  writers split *"spec-as-source"* (the hyped, bad version — his actual
  target) from *"spec-anchored"* (lightweight, where the real value is
  today). Heavy spec is legitimate for large teams, legacy codebases, and
  async cross-timezone work — the old, always-valid design document, not a
  new default for everyone.
- **His own benchmark prompt is a one-page goal statement**, validated by
  running it, not by trusting the prose.

## His actual practice: Agile Vibe Coding

XP (Extreme Programming) with an LLM: tests, Clean Code, CI, pair-programming-style
driving, deploy. You direct the agent like a very fast pair: say what you want,
watch execution, correct while the error is cheap. **"10% do trabalho; os
outros 90% são engenharia de software normal, a de sempre."** 600+ hours, 500k+
lines, dozens of shipped projects, no graph, no certification.

## ai-memory — the tool behind "eu troco de harness como troco de cueca"

His own open-source tool ([github.com/akitaonrails/ai-memory](https://github.com/akitaonrails/ai-memory)),
positioned as the opposite of taxonomy-selling: it reads each harness's native
session format from the outside (never touching the original file), and keeps
a searchable ledger — messages, tool calls with results, compaction summaries,
git checkpoints, each event tagged with its origin harness. Switching tools
mid-project stops losing context, because **project knowledge lives in the
project, not in one tool's session.**

The consolidation step is the part that also answers SDD: what merits
surviving (decisions, rules, gotchas, failed attempts) gets distilled into
short Markdown wiki pages every agent reads before starting — *"a spec tenta
adivinhar o projeto; a wiki registra o projeto."* Fed by the work itself, so
it can't silently rot the way a pre-written spec does — when it's stale,
agents visibly trip on it every session, which is itself the freshness signal.

> [!important] Structural parallel to this vault — discussed with msilva, 2026-08-20
> This is the same design bet this wiki already makes (`CLAUDE.md`): don't
> trust a static spec, keep a living set of pages distilled from the actual
> work, read before acting. Two real differences: ai-memory's input is raw
> tool-call/session logs across harnesses (mechanical capture, aimed at
> same-day tool-switching continuity); this vault's input is meeting
> transcripts and design docs, and the distillation is editorial — resolving
> contradictions, marking `(unverified: …)`, tracking open questions — closer
> to what Akita calls "what merits surviving" than to the raw ledger under it.
> **Open question raised in that discussion, not decided**: should this vault
> ever also ingest raw coding-tool session transcripts (Claude Code, etc.),
> the way ai-memory does, rather than staying scoped to meetings/decisions/docs?
> No action taken — msilva wants it on record as a live question, not resolved
> here. Any change to what this vault ingests is a schema change and belongs
> in `CLAUDE.md`, not a silent scope drift.

## Why it matters to [[Agent Flow]]

Not written *about* Livemode, but it lands directly on the open orchestration
and spec questions already tracked there:

- **[[Agent Flow]]'s 14-agent design is exactly the shape Akita's compounding-failure
  argument targets** — long chains (A1→A2→A7→A8→A9), five transversal agents,
  an orchestrator (A8) deciding what goes where. His math and his own
  measured 69-point swing are a direct, receipted counterpoint to that
  breadth — not proof it's wrong (his own carve-out for genuinely parallel or
  unattended work may fit some of it), but a reason to weigh **is this chain
  actually necessary, or is a strong single agent with good tools most of the
  win** before committing to depth.
- **A7 Discovery is specified to generate "a complete PRD" that A8/A9 build
  from** — structurally close to the "spec-as-source" pattern Akita (and
  Thoughtworks) warn against, not the lighter "spec-anchored" version. Worth
  weighing against [[Gabriel Packer - DAG-driven agent orchestration]]'s
  actual practice, already cited approvingly on that page: Packer's
  "ticket is the prompt" is scoped per unit of work, dependency-graph-driven,
  and validated by running it with a human merge gate — closer to Akita's
  legitimate "spec-anchored" case than to full-system SDD. The two prior-art
  sources are not in tension; Agent Flow's A7 output contract sits closer to
  the risky end than either source's actual practice does.
- **The "questão de gráfis" Luís pointed at is Packer's dependency graph**,
  already resolved on [[Gabriel Packer - DAG-driven agent orchestration]] as
  a DAG, not a LangGraph-style architecture diagram — consistent with Akita's
  point that graphs earn their place only when the flow genuinely branches.
  Packer's ticket DAG genuinely branches (blocked-by edges); a 14-node
  architecture diagram for what's mostly sequential intake→classify→execute
  may not.
- **ai-memory's harness-switching answer is a plainer version of what
  [[Claude Agent SDK]] and the dev-subagent question are circling** — Luís's
  point that subagent design is project-harness scope, not architecture
  scope, matches Akita's own conclusion that harness choice is a detail, not
  a discipline, once you have continuity across it.

Not folded into any Agent Flow decision — registered as a counterpoint to
weigh, per msilva's direction (2026-08-20), not a reopened design question.

## Open questions

- Should this vault's own ingest scope ever extend to raw coding-tool session
  transcripts, the way ai-memory does — rather than staying scoped to
  meetings, decisions, and docs? Raised 2026-08-20, explicitly left open; any
  change here is a `CLAUDE.md` schema proposal, not a page-level decision.
- Akita's multi-agent benchmark numbers (24 vs. 91, the 69-point swing) come
  from his own LLM coding benchmark, not an independent audit — treated as
  a strong, receipted claim, not verified fact.
