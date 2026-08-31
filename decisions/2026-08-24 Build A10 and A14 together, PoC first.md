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
  msilva confirmou. **Correção da mesma sessão**: cheguei a sugerir o
  A10 consumir o agregado que `/api/a14/overview` já calcula
  (`total_projects`/`projects_with_alerts`) — msilva corrigiu, isso
  violaria o "anarchic first" já decidido em [[Agent Flow]] (*"each
  agent built independently... no cross-dependency"*). Redesenhado pra
  A10 recalcular tudo por conta própria a partir só do backlog que já é
  sua entrada, mesmo que o resultado se pareça com o que o A14 também
  calcula — redundância aceitável, dependência não.

  **Desenho final, validado ("faz sentido" — msilva)**: pesquisa rápida
  de frameworks de PMO (Tempo, Epicflow, P3M3) convergiu em poucas
  dimensões — semáforo de status, risco (+ tendência), utilização de
  recurso; financeiro e "benefit realization" ficam de fora (Linear não
  tem esse dado, isso é papel do A11 que nem existe). Mapeado pro que o
  A10 já tem disponível **sem query nova no Linear** — `Issue.project_id`
  já vem em todo `list_issues()`, `linear_client.list_projects()` já
  existe:
  - `projects_with_alerts` / `projects_clean` — reagrupar as sugestões
    (que já existem) por `project_id` em vez de por issue.
  - `risk_by_project` — mesma reagrupação, granularidade de contagem por
    projeto (concentrado num só vs. espalhado).
  - `workload_by_project` — mesma lógica de `assignee_workload`, trocando
    a chave de pessoa pra projeto (pega desequilíbrio que a visão por
    pessoa não vê).
  - `trend` — compara contagem de alertas desta rodada com a última,
    usando o **próprio** `cache.py` do A10 (não o `memory.py` do A14).
  **Custo: zero chamada nova de LLM** — tudo isso é `rules.py` puro,
  computado depois que o agente já rodou, mesmo padrão de
  `summarize_backlog`/`BacklogSummary` que já existe hoje. Único ajuste
  de fluxo necessário: `run_a10` hoje só lê o cache antigo pra decidir
  early-return; pra ter tendência, precisa também ler o valor antigo no
  caminho de recálculo, sem tocar no early-return em si (que é o que
  controla custo).
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

## Implementação iniciada 2026-08-28 — as duas candidatas promovidas

Fecha a tensão entre as duas seções acima ("nenhuma entra agora" vs.
"essa sai do backlog"): msilva confirma que, terminada a análise, **as
duas sugestões promovidas em "Aprofundado" entram no código agora**:

1. `portfolio_health` no A10 (`projects_with_alerts`, `risk_by_project`,
   `workload_by_project`, `trend` — puro `rules.py`, zero custo de LLM).
2. "Desvio de escopo" no A14, como julgamento do LLM evidenciado (mesmo
   padrão do `escopo_descontrolado` do A10).

O restante do feedback do Luís (desvio de prazo com causa, bloqueios com
dono, decisões pendentes, riscos com idade, veredito explícito, recorte
por audiência, e o item de A10 sobre redação "backlog de projetos")
continua no backlog pós-PoC, sem mudança — só essas duas saem daí.
Implementação segue no repo `livemode-fluxo-agentico`, não nesta wiki.

## Ideia registrada 2026-08-28 — A14 ler repositórios de código

msilva propôs, à parte do feedback do Luís: **A14 ter acesso aos
repositórios dos projetos**, pra enxergar evolução real de código (não
só o que o Linear registra) — resolveria o ponto cego de um projeto
marcado "em andamento" há semanas sem nenhum commit, ou o inverso.

Diferente de todo o resto do backlog acima: não é campo novo em cima do
que o A14 já lê do Linear, é uma **fonte de dado inteiramente nova**
(acesso a Git/GitHub, autenticação própria). Ressalvas levantadas em
chat:
- **Mapeamento projeto→repo não é trivial** — nem todo projeto do Linear
  tem repo 1:1; um repo pode servir vários projetos; alguns projetos
  (ex. iniciativas de processo) podem não ter repo nenhum.
- **Risco de virar métrica de vaidade** se reduzir a "contagem de
  commits" — mesma cautela já aplicada a "dias sem atualização" e à
  "subtask blindness" que este projeto já evitou antes.

**Registrado como item próprio do backlog pós-PoC**, separado das duas
promovidas acima (é categoria diferente — fonte de dado nova, não
julgamento novo sobre dado que já existe). Não aprofundado além disso
nesta sessão.

## What this changes elsewhere

- [[Agent Flow]] — noted as the concrete shape of the next build.
- [[2026-08-24 Start Agent Flow with A10 Portfolio]] — its open "A10+A14, one
  build or two" question is answered here: **one build, two specs.**
- [[Como implementar a PoC do A10+A14 (LangGraph, Skill, Agent SDK)]]
  (2026-08-26) — elabora o *como* desta decisão: a PoC virou três frentes
  leves de validação (LangGraph, Skill, Agent SDK), não mais LangGraph vs.
  Claude Code, e o objetivo deixou de ser "framework é necessário" pra virar
  "ter algo palpável + opções em mãos".
