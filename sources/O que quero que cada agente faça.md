---
type: source
status: stable
updated: 2026-10-22
date: 2026-10-01
source: "raw/O que quero que cada agente faça.md"
aliases: [quatro agentes, visão dos 4 agentes]
tags: [agent-flow, agents, intake, a10, a14, prioritizer]
---

# O que quero que cada agente faça

Nota curta do próprio msilva (`raw/O que quero que cada agente faça.md`)
com o objetivo e o canal de cada agente do [[Agent Flow]]. Sem data no
texto; *(unverified: 2026-10-01 vem do mtime do arquivo, pode ser a data
de criação local)*.

## Os quatro agentes

| Agente na nota | Objetivo | Canal | No repo hoje |
|---|---|---|---|
| **Portfólio** | Acompanhar o progresso das **iniciativas**; apontar problemas e sintomas por critério (iniciativa estagnada, fluxo represado) | Chat e status update no Linear; talvez Slack | A10, `portfolio_analyst` |
| **PM** | Acompanhar o progresso dos **projetos**; apontar problemas por critério (milestone atrasado, lead time alto) | Chat e status update no Linear; talvez Slack | A14, `project_reporter` |
| **Taquinho** | Receber demandas, normalizar, enriquecer com contexto, classificar e rotear (técnico ou não técnico) | Chat, menção no Slack ou captura automática de chamado aberto pra Projetos | Intake — "Taquinho" é a persona dele (PRO-546) |
| **Priorização** | Sugerir os valores da expressão de prioridade para projetos novos ou existentes | Chat; talvez pipeline quando um projeto novo for cadastrado | `prioritizer` (commit `da31ea5`, 2026-10-01) |

## Por que importa

- É a visão de **4 agentes**, não dos 14 do desenho original
  ([[Fluxo Agêntico diagram]]). Bate com o que o repo tem hoje e explica
  a contagem "4 agentes reais" em
  [[Como deve funcionar o molde de agente]] e
  [[O que falta para os agentes passarem no teste de portabilidade]].
- Portfólio e PM dividem o trabalho por **unidade** — iniciativa contra
  projeto —, a mesma fronteira que o código adotou em 2026-09-04, quando o
  A10 passou a julgar por iniciativa.
