---
type: synthesis
status: active
updated: 2026-08-19
date: 2026-08-17
aliases: [agent flow research, research agenda, agent flow status]
tags: [agents, research, planning, index]
---

# What should the Agent Flow research phase study?

> [!warning] The selection criterion changed on 2026-08-18
> msilva's manager wants the agent with the **most utility** built first. Everything
> in the *Settled* table below that concerns **which** agent goes first was settled
> under a different test — *lowest risk of failing*. The A5 design details still
> hold; the choice of A5 is reopened.
>
> **msilva's position, 2026-08-18: A1 + A2 first** — a position, not a decision.
> Full reasoning both ways: [[Which agent should be built first]].
>
> Two new facts feed it: **[[Orca (CDE)]] and other services are to be plugged into
> the [[Airtable Proxy]]**, and **A7 cannot be chat-only**.

> [!danger] New, same day: Luís wants A5/Watcher decoupled from the proxy entirely
> [[2026-08-18 1-1 Matheus - Luís]], hours after the note above was written.
> Contradicts the proxy-Orca-reach framing directly; not resolved. **Blocks further
> A5 design** until msilva runs separate discovery conversations with Gabrielle and
> Carol about what each imagines the Watcher doing. Full detail: [[Agent Flow]] and
> [[Which agent should be built first]].

> [!tip] A candidate reconciliation, 2026-08-19 — msilva's own idea, not confirmed
> [[2026-08-19 1-1 Matheus - Gabrielle]]: Watch as a **multi-tool consolidator**
> (proxy + LogRocket + Vercel) rather than a proxy-scoped agent — the proxy
> becomes one input, not the definition. Might satisfy both Luís (not
> proxy-defined) and Gabrielle (proxy still useful). **Doesn't unblock the
> discovery conversations above** — raise it there, don't treat it as the
> answer. Detail: [[Agent Flow]], [[Which agent should be built first]],
> [[How to implement A5 Watcher]].

> [!danger] Possible convergence, same day (Part 2) — attribution collapsed, unconfirmed
> msilva reports agreeing with a view attributed to Gabrielle that Watch
> scoped to the proxy doesn't make structural sense today — Luís's exact
> objection. Could mean the tension above is narrowing on its own, or could
> just be msilva's own view under collapsed attribution. Needs a direct check
> with Gabrielle. Same source.
>
> **Also from Part 2**: a third candidate framing — start with **the team's
> own most immediate pain** (Carol), not utility broadly. msilva's two pains
> (no unified cross-project backlog; no good discovery/documentation minimum)
> point at **A10+A14** and **A7+A8**, neither previously on the shortlist.
> Not incorporated into [[Comparing the first-agent candidates]] yet.

**Status board and router for [[Agent Flow]]'s research phase.** This page tracks
*what is settled and what is open*; the reasoning lives on the pages it points to.

> [!note] Demoted to an index 2026-08-17
> This was the first research page and originally carried all the reasoning. As
> later pages covered that ground better, it became a stale mirror — Track 1 still
> recommending A5 without the case against it, Track 2 calling scheduling "the real
> unknown" after it was settled. Rewritten as a router. **Reasoning belongs on the
> pages below; this page routes and tracks.**

## Where the reasoning lives

| Question | Page |
|---|---|
| Which agent first, and why — with the case against | [[Which agent should be built first]] |
| How to actually build A5 | [[How to implement A5 Watcher]] |
| The authoritative spec for all 14 | [[Fluxo Agêntico project instruction]] |
| The architecture as drawn | [[Fluxo Agêntico diagram]] |
| External prior art | [[Gabriel Packer - DAG-driven agent orchestration]] · [[Gabriel Packer - solo founder AI workflow (part 1)]] |

## Settled

| | Where decided |
|---|---|
| **AI-First** — agents execute, humans approve. Autonomy is the default | [[Fluxo Agêntico project instruction]] |
| **Anarchic first, integrated second** — agents built independently, in production alone, no cross-dependency | ⇧ |
| ~~**A5 Watcher first**, targeting proxy telemetry~~ — **reopened 2026-08-18**: settled under a lowest-risk test, not a utility one | [[Which agent should be built first]] |
| **A5 does not poll** — Grafana fires, A5 receives. Hybrid: events for incidents, a daily pass for opportunities and liveness | ⇧ |
| **Grouping + throttling before the agent**, plus a ceiling that fails closed | ⇧ |
| **Direct access to Prometheus, Loki and the observability stack** — not just alert payloads | ⇧ |
| **Monorepo layout, A5-only contents**; `contracts/bug-sistema.md` written first | [[How to implement A5 Watcher]] |
| **Alert rules stay in the proxy repo**, versioned with the metrics they query | ⇧ |
| **Limits**: files but never fixes; never quotes payloads or headers; silent unless it can say why | ⇧ |

## The one open research question with no home yet

### Adoption — invocation, not friction

**Reframed 2026-08-17 by msilva.** The previous attempt died because **it was never
called** — the bot sat on one Slack channel waiting to be `@`-mentioned, while
people report problems through whatever path is at hand: a DM, another channel, a
meeting, walking over. **Channel fragmentation**, not unwillingness to file.

That splits the problem in two, and the design handles them unequally:

| Half | Addressed? |
|---|---|
| **Fragmentation** — reports arrive by many paths | **Yes.** A1 captures from Slack, Monday, email, forms, webhooks, system alerts |
| **Invocation** — the agent must still be addressed | **No.** If each channel needs a form, a webhook or a mention, the same failure is rebuilt six times over |

**Live question: does A1 listen passively, or wait to be addressed?** Passive
listening is a materially different system — classifying a firehose, with cost,
noise and privacy consequences nobody has discussed. Explicit invocation is cheap
and repeats the recorded failure. The instruction picks neither.

**A5 is immune to both halves** — its input is a system alert: one channel, no
fragmentation, no invocation, no human in the loop. That is why it went first
**under the lowest-risk criterion** — reopened 2026-08-18, see the warning above.

## To confirm with Gabrielle before 2026-08-24

She is on leave from that week for ~2.5 weeks. **Luís is the primary technical
contact and works afternoons only.** A deadline-setting meeting with both was set
for the end of the week beginning 2026-08-17.

- Is **A5 still her recommendation**, given the diagram, the instruction and the
  Packer material — none of which she had seen when she suggested it?
- ~~**Orca or LiveScript?**~~ **Resolved 2026-08-18** — Orca and other services are
  to be plugged into the [[Airtable Proxy]], so it is both, through one chokepoint.
  Replaced by: **is Orca's migration sequenced or directional, and when?**
  **msilva does not know (2026-08-18)** — it isn't mentioned in
  [[Orca Next Version]]'s own roadmap — so ask Gabrielle or Luís, and put no date
  on "A5 second". That is what decides A5's utility date.
- Does she accept the **narrower reading of opportunity detection** — the proxy
  surfaces *technical* inefficiency, not the product/process opportunity the
  instruction describes?
- **Is A13 in scope** as a second agent, or is deduplication just logic inside A5?
- Does **A5 filing into Linear** have her support, mid-migration?
- **Monday** is listed as an intake channel despite the Linear migration — is the
  instruction stale on that point?
- **A9's controlled stack omits Go** (React, Python, Node.js, known APIs, N8n,
  Vercel/Replit) while the [[Airtable Proxy]] is Go. Does the constraint apply only
  to new systems?
- Why were the agents **renumbered**? Knowing what was merged or split would say
  which parts are settled.
- Where is the **design documentation** she offered to share on 2026-08-10?

**Added 2026-08-18 from [[2026-08-17 Weekly - Projetos e Tarefas]]:**

- **Should the matriz external-events gateway be the first build?** It is A1 + A2 in
  miniature, arriving from the business with a named requester — but it has an
  unresolved blocker that is not technical: *who approves these at all*. See the
  use-case section in [[Agent Flow]]. This is the strongest candidate to appear since
  the comparison was written, and it deserves a line in that conversation.
- **What is the AI status readout, mechanically** — Linear's own feature, something
  the team built, or a vendor tool? [[AI status reporting on Linear]] is the only
  agent running in the area, it has a known defect that **A1 and A2 would inherit**,
  and nobody knows who owns it. Ask Luís as well as Gabrielle.
- **Is the readout's subtask blindness a target msilva could fix or replace?** A
  working agent, a real weekly audience, a specific defect, and a measured failure
  (6% vs 20–25%). Unusually well-defined for a first build. **Nobody proposed this —
  it is this vault's suggestion**, so it needs putting to her rather than assumed.
- **Should msilva be in the TES trial group?** He is not (Bia, Arthur, Kauan are),
  and formal vendor feedback is due the week of 2026-08-24 — i.e. while she is away.
  TES is the adjacent initiative most likely to overlap or pre-empt this project.
- **When is a long unattended agent run the right tool?** Luís's own open question
  about Ultra Code, and the same question as the token-cost constraint asked from the
  other end. He owns this one, not Gabrielle.

**Added 2026-08-18 from [[2026-08-18 1-1 Matheus - Gabrielle]].** That 1:1 was the
**last scheduled one before her leave**. Several items here were *answered* by it —
struck through, kept for the record — and the answers reshape the list.

- ~~**Which initiative does [[Agent Flow]] belong to?**~~ **Answered** — its own, with
  **one project per agent**, which fits *anarchic-first* exactly. Do this: an initiative
  with no projects is how the research becomes invisible.
- ~~**What are the "Tech" and "Humans" accounts?**~~ **Answered, as *both*** — two
  different tools sharing a naming convention. **Claude plans**: tech is limit-based
  (daily/monthly, no session windows), *humanos* is seat-based Pro/Max with more tokens
  and an upgrade path Carol had already requested for msilva. **n8n**: the tech login
  sees every flow in the company with per-execution token counts, filterable only by name.
- ~~**What is the AI status readout, mechanically?**~~ **Answered** — an in-house board
  Gabrielle controls that pulls from Linear, aggregating at issue level **deliberately**,
  with drill-down planned. The remaining question is narrower and better: **what produces
  the written insight and the pace classification?** That is the part that got Orca wrong
  and the only genuinely agent-like part.
- ~~**Are Luís's ticket templates usable as an agent output contract?**~~ Superseded by a
  better question: **what does Luís think is wrong with his own templates?** He is
  rewriting them because he dislikes how they came out. His diagnosis is worth more than
  the artifact. Gabrielle assigned asking him.
- **Is the Linear Business plan being bought?** Still open, still deadlined. Trial expires
  **2026-09-09**, one day before she returns, and *"se faz sentido assinar"* is genuinely
  undecided. msilva has already hit the free cap once.
- **How many n8n flows touch Airtable, and who holds which key?** New, and the most
  consequential of the lot. The citizen flows are intended proxy consumers, nobody knows
  how many there are, and each author holds their own PAT — [[Airtable Proxy]]. It is a
  prerequisite for A5 having anything to watch there, and **nobody owns the discovery**.
- **Is the tracking board an Airtable consumer?** One garbled clause says it pulls from
  Airtable. If true, the tool that watches msilva's project belongs behind it.

> [!important] Two of this list's premises weakened on 2026-08-18
> **The token evidence is retracted.** Gabriel switched LLM provider between the $7 run
> and the 11-centavos run, so *"cost is intermittent, not inherent"* has no clean
> measurement behind it. Don't design around either hypothesis yet.
>
> **The cost constraint is softer than recorded.** Gabrielle told Gabriel that spending
> more during stabilization is fine and that leadership agrees. Optimizing tokens is a
> goal, not a gate. Both details: [[Agent Flow]].
>
> **And A2's hard part moved.** Claude already writes this area's Linear backlogs from
> documentation — milestones, issues, subissues, unattended. Ticket *authoring* is
> solved here; what remains for the intake pair is **classification and routing**, and
> A1's intake breadth. That sharpens [[Which agent should be built first]] rather than
> changing its answer.

## Still open, no owner

- **New, 2026-08-18 — discovery, not design, is the next step for the Watcher/A5.**
  Separate ~30-minute conversations with Gabrielle and Carol, deliberately apart,
  about what each imagines the agent doing at a level of detail msilva could act
  on. Prerequisite for resolving the Gabrielle-vs-Luís tension above. Owner:
  msilva.
- **How do agents talk to each other?** The unsolved problem that killed the prior
  attempt.
- **Automated QA** is a first-class stage in Packer's flow and absent from all 14
  agents. Gap or deliberate omission?
- **Does the homologation flow gate this work?** It validates architecture,
  tooling, security and governance before implementation
  ([[2026-08-14 Papo de Projetos]]).
- **Is there a token budget**, or only the unease recorded in the 1:1?
- **Where does A6 Curator's memory live** — the agents repo, or this vault, which
  is already a working prototype of memory-as-files?
- ~~**Who owns context retrieval?**~~ **Answered 2026-08-18 — not an agent.**
  See [[Agents read primary sources]]: a shared `tools/` layer in the monorepo, not
  a fifteenth agent, because a retrieval *agent* would create the cross-agent
  dependency phase 1 forbids. Corollary: **A6 splits** into retrieval (tools, now)
  and curation (an agent, phase 2). *Recommendation, not yet agreed with Gabrielle.*
- **How often does the area start a new project?** **msilva has no metric for this
  (2026-08-18).** So A7 cannot be evaluated on utility at all — which is itself an
  argument for A1 + A2, the build that would produce the number. Ask Gabrielle.
- **Is A1 + A2 one agent or two?** New 2026-08-19, [[2026-08-19 1-1 Matheus -
  Gabrielle]]. Splitting earns its keep only if A2 gets inputs beyond A1.
- **Does A3 need a universal discovery/planning step, or only complex work?**
  New 2026-08-19, same source — msilva questioning his own A3-vs-A7 split.
  Could collapse the fork into "discover always, fork by complexity after."
  **Part 2, same day**: cuts the other way in places — Carol and msilva's own
  practice both favor *less* upfront context, while Luís's heavy-context
  approach isn't visibly faster. Tension, not resolved either direction.
- **Is UX/usage-pattern monitoring part of A5 Watcher, or a separate product
  concern?** New 2026-08-19, same source.
- **Does this team own A12 Data Gov**, or does data-usage validation belong to
  a platform/foundation layer? New 2026-08-19, Part 2.
- **Is A13 Deduplication the same agent as A10 Portfolio?** New 2026-08-19,
  Part 2 — redundancy detection shows up in both.
- **Is A6 a 4-way split, not 2-way?** New 2026-08-19, Part 2 — its four named
  functions (memory, learning, redundancy detection, agent-interface) may be
  more than the existing retrieval/curation corollary accounts for.
- **Is Carol's intranet tool already A10 Portfolio?** New 2026-08-19, Part 2 —
  unconfirmed.

## Corrected along the way

Kept visible rather than deleted, per the schema:

- **The "sequencing problem" this page originally led with does not exist.** It
  argued A5 needed A3, and A6 was a precondition. The instruction's *anarchic
  first* strategy answers both — a consumer-less agent is the method.
- **"Nothing addresses adoption" was wrong.** A1 addresses the fragmentation half
  by design; only invocation is unaddressed.
- **"A5 would be watching an empty pipe" was misleading.** The local `otel-lgtm`
  stack means only threshold *tuning* is blocked by GC-5.
