---
type: decision
status: active
updated: 2026-09-10
date: 2026-09-10
aliases: [fluxo_represado, upstream downstream A10, critérios A10 via skill, DETERMINISTIC_CRITERIA removido]
tags: [agent-flow, agents, a10, harness, skill, criteria]
---

# A10 — critérios julgados por skill, não determinísticos em Python

## Origem

msilva, 2026-09-10, mesma sessão de código em `livemode-fluxo-agentico`
(branch `langgraph`). Partiu de uma pergunta sobre o que Luís descreveu
como escopo do Portfolio (A10): avaliar qualidade do backlog, ler as
esteiras upstream/downstream, responder "qual projeto priorizar" e "vamos
ficar sem backlog". Comparado contra o código real (`a10/rules.py`,
`a10/contracts.py`), abriu uma lacuna que virou redesenho maior do que os
dois critérios novos que motivaram a conversa.

## Decisão

**Nenhum critério do A10 decide sozinho em Python.** `DETERMINISTIC_CRITERIA`
(hoje `iniciativa_estagnada`, `gargalo_de_capacidade`) deixa de existir como
conceito — os dois passam a ser julgados por LLM+skill, igual
`priorizacao_desalinhada`/`escopo_descontrolado` já eram.

**O que continua em Python: só o cálculo (aritmética/contagem), nunca o
veredito.** `days_since_last_delivery`, `capacity_share`, `by_state_type`
continuam computados em `rules.py` e expostos via
`list_initiative_summaries()` — o motivo original de existirem em Python
continua valendo (HANDOFF.md: "LLM erra aritmética de data"), só que agora
esse motivo cobre só o número, não a decisão de "isso é grave o
suficiente pra virar alerta?". Essa decisão sai do threshold hardcoded
(`CAPACITY_SHARE_ALERT = 0.30`, `STAGNANT_DAYS_THRESHOLD = 21` — o próprio
código já admitia "chute inicial, ainda não calibrado") e vira julgamento
de skill/prompt, ajustável no Langfuse sem redeploy.

## Critério novo: `fluxo_represado`

Matéria-prima: `by_state_type: dict[str, int]` por iniciativa (distribuição
crua `backlog`/`unstarted`/`started`/`completed`/`canceled`), mesmo padrão
que `BacklogSummary.by_state_type` já usa pro backlog inteiro
(`a10/rules.py::summarize_backlog`). **Classificação upstream/downstream é
comportamento de skill, não bucket calculado em Python** — nenhuma
lógica decide o que conta como "fila" vs. "em andamento", isso é
interpretação do agente, ajustável via prompt.

Cobre também a pergunta de "vamos ficar sem backlog": a mesma matéria-prima
(`by_state_type`) já respondia isso em parte no nível de portfólio antes
desta sessão — `BacklogSummary.by_state_type` já existia e já chega no chat
via `cached_context` (`a10/agent.py:202`). O que faltava era granularidade
por iniciativa, que `list_initiative_summaries()` ganha agora. Sem verdict
pré-computado (`runway_status`, threshold de "quase terminando") — isso foi
cogitado e descartado: a intenção é o agente **chegar** nessa conclusão a
partir do dado cru, não a gente decidir por ele.

## Correção de fronteira de tool no chat

`create_tools(..., include_issue_detail=False, ...)` (uso do chat) devolvia
só `[calculate_assignee_workload]` — empacotava duas coisas diferentes sob
uma flag só: agregado por iniciativa (N1, seguro pro chat, ver
[[Fronteira A10×A14 (informação e métricas)]]) e detalhe de issue (abaixo
de N1, correto ficar de fora do chat, regra do Luís de 2026-09-02).

**Correção**: `list_initiative_summaries()` passa a estar sempre disponível
(chat e batch) — é agregado N1, mesmo nível que já era permitido. Só
`list_initiative_issue_texts` e as tools de repositório continuam
gateadas por `include_issue_detail`. Chat passa a raciocinar iniciativa por
iniciativa (via essa tool) e no geral (via `summary`/`portfolio_health` já
injetados em `cached_context`).

## Skill no chat: sempre incluída, sem dispatch

O mecanismo de Skill do molde ([[Como deve funcionar o molde de agente]],
seção "Slot Skills") dispara por **critério em avaliação** — funciona no
batch (critérios fixos por execução), não no chat (conversa livre, sem
critério pré-definido pra disparar por). Decisão: no chat, as skills
relevantes entram sempre no prompt composto, sem mecanismo de dispatch —
simples, custo de token baixo, evita construir um roteador de tópico que
nada pediu ainda.

## Consequência: Skills sai do conceitual

"Slot Skills" no molde estava fechado como conceito, mas **nada codado** —
os dois candidatos existentes (`priorizacao_desalinhada`,
`escopo_descontrolado`) ainda vivem só no prompt principal, sem skill
dedicada. Esta decisão generaliza pra todo critério do A10 precisar de uma
skill própria — a tabela `skills` (desenho já registrado no molde) deixa de
ser hipotética, vira pré-requisito real desta mudança.

## Custo aceito, sem esconder

Dobra (em breve mais, com `fluxo_represado`) a superfície de raciocínio por
iniciativa — hoje só 2 dos 4 critérios passavam por LLM, os 4 passam a
passar. Mais token/latência por execução. Determinismo entre execuções
idênticas cai pros dois critérios que eram puros — mesmo risco que
`priorizacao_desalinhada`/`escopo_descontrolado` já aceitavam, agora
estendido. Testabilidade não quebra, só desce de nível: o número continua
testável sem LLM (`test_rules.py`); o veredito passa a exigir o nível
"agente com reader fake", igual os outros dois critérios julgados já
exigiam.

**Nenhuma implementação foi autorizada nesta sessão — só o desenho.**

## Relacionado

- [[Como deve funcionar o molde de agente]] — seção "Slot Skills"
- [[Fronteira A10×A14 (informação e métricas)]]
- [[2026-09-10 Memória de fatos do agente (agent_facts)]]
- [[2026-09-10 Agentes expostos como tool uns para os outros]]
- [[Agent Flow]]
