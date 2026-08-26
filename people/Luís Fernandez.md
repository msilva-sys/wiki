---
type: person
status: active
updated: 2026-08-25
aliases: [Luís, Luis Fernandez]
tags: [people, engineering]
---
	
# Luís Fernandez

Tech Lead for the area. Part-time — afternoons only, with flexibility
([[2026-08-14 Papo de Projetos]]). Validates architecture and systems,
sometimes builds directly, otherwise supports.

## Owns / decides

- Reviews Yasmin's work in Git (implementation review), distinct from
  Gabrielle's product review
  ([[2026-08-18 Product feedback in Linear, code review in Git]]).
- Primary technical contact for [[Agent Flow]] and the [[Airtable Proxy]]
  while Gabrielle is on leave.
- Pointed msilva at [[Gabriel Packer - DAG-driven agent orchestration]] as
  prior art for the agent architecture; disagrees with Gabrielle on scoping
  A5 Watcher to the Airtable Proxy specifically — unresolved as of 2026-08-18
  ([[Which agent should be built first]]).
- Doing the GCP structuring for [[Farol]]; ran a 4–5 hour unattended Ultra
  Code session on it ([[2026-08-17 Weekly - Projetos e Tarefas]]).
- Author of the in-house Linear ticket templates (bugs/spikes/epics),
  currently rewriting them — dissatisfied with how they turned out.
- Maintains his own evolved skills set inside the "Fronte de Negócios" repo,
  separate from any shared skills repo.
- Set the process norm, 2026-08-18: bring compared options before acting on
  a technical decision; communicate async and often rather than saving
  things for scheduled 1:1s
  ([[2026-08-18 Bring options to Luís before deciding, communicate async and often]]).
- Helping Carolina build the team-wide skills/plugins repo (`livemode`) and
  the Claude Code training programme.
- Granted msilva explicit autonomy over Linear organization decisions,
  2026-08-19 — *"não me considero dono do projeto[...] se sente com liberdade
  para poder tomar essas decisões"* ([[2026-08-19 1-1 Matheus - Luís]]).
  Self-assesses as using *"5% do potencial"* of Linear; reading its docs over
  the weekend of 2026-08-22/23 to bring back organizing guidance.
- Independently confirms the backlog/project visibility pain Gabrielle and
  Carol have also raised (2026-08-19) — see [[Agent Flow]].
- Already experimenting with the [[Claude Agent SDK]] (headless Claude Code)
  for his own purposes, separate from this team's work (2026-08-19).
- Told Carol that Agent Flow's dev-subagent design is project-harness scope,
  not architecture scope (2026-08-19) — see [[Agent Flow]].
- Frames working AI agents as colleagues — *"para mim são pessoas [...] eu
  tô dando capacidade para ele"* — a philosophy he says he uses in his own
  workshops. Pushed msilva, 2026-08-20, to finish the agent-inventory
  discovery work (agir/informar heuristic) before any implementation or
  memory design — independently reinforcing msilva's own same-day doubts
  about the 14-agent diagram's shape ([[2026-08-20 1-1 Matheus - Luís]]).
- Ran a full agent-by-agent verdict pass on the 14-agent diagram the same day
  ([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]): A1 and A2
  merge, reconfirmed as one agent; A6 Curator isn't a standalone agent for now
  (organized memory as infrastructure still matters); A3 and A9 are the same
  agent; A8+A9 collapse toward "Claude Code itself"; only A13 Deduplication
  genuinely fits "transversal intelligence," with A10/A11/A12 being separate
  concerns.
- Pushes a working method of specifying every agent as **actor → input →
  output** only, deferring all inter-agent graph/sequencing design until
  necessity forces it — same conversation.
- Tasked msilva with a bounded validation experiment: build a small A10
  Portfolio prototype in **LangGraph** and compare it against building the
  same thing directly in Claude, to settle whether a framework is even
  needed — same conversation.
- Reviews pre-production proxy work by watching a **live walkthrough** of
  captured metrics rather than reading a diff, when standing up the review
  environment himself would cost more time than it's worth
  ([[2026-08-24 1-1 Matheus - Luís]]).
- Wants to build the GCP Cloud Run/Pulumi deploy step **together** with
  msilva rather than solo, explicitly so msilva learns how Luís evaluates and
  critiques that kind of setup — same call, 2026-08-24.
- Flags a GCP account-tier risk: the current (first paid) plan has no
  per-user project isolation, so anyone with account access can see every
  project; he believes only the Enterprise tier fixes it — 2026-08-24, see
  [[2026-08-21 Deploy Airtable Proxy privately behind VPN]]. **Named as a
  bus-factor risk, 2026-08-26** — he's the sole owner of GCP/Cloud
  Run/Pulumi structuring, cross-read against Bossabox's Team Topologies
  instrument in [[What Bossabox's Assessment suggests for Agent Flow]].
- **On [[2026-08-25 Farol - Dados]]**, alongside msilva, Carolina and
  Arthur Tavares — Gemini mislabeled the whole four-person call as
  Carolina Bezerra alone (corrected 2026-08-25). One line matches his own
  2026-08-17 ownership stance almost exactly — *"a gente não cria banco
  de dados para outras áreas"* — attributed to him with above-average
  confidence on that basis, though Arthur (also data-side) isn't ruled
  out. Most of the meeting's technical content isn't confidently his.
- **Was physically on the [[Bossabox Engagement]] discovery call**
  alongside msilva and Carolina — all three picked up on Carolina's mic,
  so the transcript's single "Speaker A" isn't reliably any one of them
  (corrected 2026-08-25 by msilva;
  [[2026-08-25 Bossabox AI transformation discovery]]). One line from that
  call, referring to him in the third person and so **not** spoken by him,
  reconfirms his afternoon-only availability as the reason those meetings
  get scheduled for the afternoon.
