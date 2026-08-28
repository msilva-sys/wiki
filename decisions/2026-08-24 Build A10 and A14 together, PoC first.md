---
type: decision
status: active
updated: 2026-08-28
date: 2026-08-24
decided_by: Matheus Silva
source: "chat with Claude, 2026-08-24"
tags: [agents, agent-flow, a10, a14, planning, decision, poc]
aliases: [A10+A14, build A10 and A14 together, M1 scope]
---

# Build A10 Portfolio and A14 PM Agent together, PoC first

**Decision.** msilva builds [[Agent Flow]]'s **A10 Portfolio** and **A14 PM
Agent** as one combined effort, not sequentially — starting with a **PoC**
before committing to production, extending the LangGraph-vs-Claude-Code
prototype Luís already tasked
([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]) to cover both
agents' scope instead of A10 alone. This is the scope of **M1** on the
[[Fluxo Agêntico project instruction|Fluxo Agêntico]] Linear project.

## Why

Resolves the question [[2026-08-24 Start Agent Flow with A10 Portfolio]] left
open: whether A10 and A14 are one build or two. Both were named together from
the start — Gabrielle's, Luís's and Carol's convergence on "the team's own
cross-project pain" is really two faces of the same gap: no backlog-wide
prioritization view (A10) and no proactive status/delivery communication
(A14) — see [[Comparing the first-agent candidates]]'s framing of
"A10+A14" as one pain. Building them together:

- **Doesn't break anarchic-first.** Neither agent depends on the other's
  decisions, or on an agent that doesn't exist yet — per
  [[Fluxo Agêntico project instruction]], A10 *"sugere, nunca executa"*, and
  A14 in this phase just needs something already tracked to report on.
- **Reuses the PoC Luís already assigned**, rather than opening a second
  validation process for a second agent.
- **Applies Carol's own prioritization test reasonably well** — her
  work-effort-savings and quality-improvement variables both favor this pair
  (poupa o recap manual hoje feito na mão; melhora visibilidade sem depender
  de feeling) — see [[2026-08-24 Agent Flow discovery with Carol]] for the
  framework itself.

## What this doesn't settle

- **A14 is scoped down for this phase.** Its full spec — following a request
  through to delivery — presupposes a pipeline (A1/A2/A7/A8) that doesn't
  exist yet ([[Comparing the first-agent candidates]], "Also considered,
  briefly": *"Follows a request through to delivery — presupposes the
  pipeline exists"*). In this phase A14 reports on whatever is already
  tracked (mainly Linear), not on a request it received itself end-to-end.
- **Kept as two specs, one build.** Each agent still gets its own
  actor→input→output frame — inputs/outputs, what it consults/feeds,
  success criteria, limits — per Luís's working method
  ([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]). One
  succeeding while the other doesn't is a real possible PoC outcome, and the
  separate specs are what let that show up instead of being averaged away.
- **Production timeline isn't set.** The PoC is the gate; if it doesn't
  validate the approach, M1 doesn't roll into a production build as scoped
  here.

## Reforçado 2026-08-28 — revisão de contratos do Luís, escopo mantido em A10+A14

Luís revisou 5 dos 11 contratos de agente (A1+A2, A10, A14, A4, A5) num
arquivo compartilhado por DM (Slack, 2026-08-28, respondendo ao artefato
que msilva enviou um dia antes mapeando input→output de cada agente).
msilva confirma aqui: a atenção agora fica só nos comentários sobre **A10**
e **A14** — os de A1+A2, A4 e A5 ficam registrados como feedback pra
quando esses agentes entrarem em escopo, não acionados agora. Mantém a
mesma lógica desta decisão (M1 é só A10+A14).

- **A10**: deixar explícito que a entrada é backlog de *projetos* (visão
  macro, não issue a issue), e considerar uma dimensão de "saúde do
  portfólio como um todo" além do item-a-item — Luís sugere pesquisar
  gestão de portfólio de projetos pra embasar quais dimensões entram aí.
- **A14**: entrada "um projeto do Linear" é genérica demais; saída deveria
  cobrir mais do papel de PM — desvio de escopo com evidência, desvio de
  prazo com causa, bloqueios ativos com dono, decisões pendentes sem
  resposta, riscos com idade (novo vs. recorrente), um veredito explícito
  (`no_trilho`/`atenção`/`em_risco`), e recorte por audiência (time vs.
  stakeholder). Reforça o que **não** é papel do A14: repriorizar, mudar
  escopo, cobrar pessoas.

**Resolvido 2026-08-28, mesma sessão**: discutido em chat (separando o que
era só ajuste de redação do que era feature nova, e o quanto cada feature
custava — ex. veredito explícito e riscos com idade seriam baratos por já
reaproveitar `memory.py`/`rules.py`; desvio de escopo e recorte por
audiência seriam trabalho novo de verdade). **msilva decidiu não mexer em
nada disso agora** — a prioridade é fechar a PoC primeiro (framework
LangGraph vs. Skill vs. Agent SDK, per
[[Como implementar a PoC do A10+A14 (LangGraph, Skill, Agent SDK)]]), não
expandir o escopo do A10/A14 no meio do caminho. As sugestões do Luís
ficam registradas aqui como backlog pra depois da PoC, nenhuma delas
entra no `contracts.py` atual.

## Aprofundado 2026-08-28, mesma sessão — duas sugestões esclarecidas

Discussão continuou lendo o artefato original completo (todos os 11
contratos, não só o resumo do Luís) — duas conclusões que revisam o
"backlog indiferenciado" acima:

- **"Saúde do portfólio" (A10) é papel do A10 mesmo, não do A14** —
  msilva confirmou. Achado relevante: `/api/a14/overview` (construído
  nesta mesma sessão pro frontend) já faz um rollup parecido
  (`total_projects`/`projects_with_alerts`) — vale considerar se o A10
  deveria consumir esse agregado do A14 em vez de recalcular do zero,
  quando essa frente for retomada.
- **"Desvio de escopo com evidência" (A14) — corrigida uma leitura
  errada minha.** Cheguei a registrar isso como *bloqueado* até o A7
  Discovery existir (por não haver um "grafo aprovado" formal pra
  comparar). msilva corrigiu: o mesmo julgamento por inferência que o
  A10 já usa no critério `escopo_descontrolado` (ler sinais — data de
  criação, vínculo com milestone, menção em comentário — e formar um
  julgamento evidenciado, não uma prova) serve igual pro A14. Não é
  bloqueio estrutural, é escolha de desenho: **julgamento do LLM agora,
  ajustável pra diff mecânico contra o grafo aprovado quando o A7
  existir.** `msilva`: **"cabe agora, depois podemos ajustar quando
  existir o A7"** — essa sugestão específica sai do backlog pós-PoC e
  vira candidata a entrar no `contracts.py` do A14 quando essa frente
  for retomada (ainda não implementada nesta sessão — sessão é wiki,
  não código).

## What this changes elsewhere

- [[Agent Flow]] — noted as the concrete shape of the next build.
- [[2026-08-24 Start Agent Flow with A10 Portfolio]] — its open "A10+A14, one
  build or two" question is answered here: **one build, two specs.**
- [[Como implementar a PoC do A10+A14 (LangGraph, Skill, Agent SDK)]]
  (2026-08-26) — elabora o *como* desta decisão: a PoC virou três frentes
  leves de validação (LangGraph, Skill, Agent SDK), não mais LangGraph vs.
  Claude Code, e o objetivo deixou de ser "framework é necessário" pra virar
  "ter algo palpável + opções em mãos".
