---
type: decision
status: active
updated: 2026-08-24
date: 2026-08-24
aliases: [deprioritize watcher, A5 not first, watcher deprioritized]
tags: [agents, a5, agent-flow, decision]
---

# Deprioritize A5 Watcher as first-agent candidate

**Decisão de msilva, 2026-08-24**: A5 Watcher é só mais um dos 14 agentes do
[[Agent Flow]] — não deve receber foco especial, nem como candidato a
primeiro agente, nem como design ativo por ora.

## O que isso muda
- **[[Which agent should be built first]]** e **[[Comparing the first-agent
  candidates]]**: toda a análise de A5 ali (arquitetura orientada a eventos,
  Path A/B, riscos, plano de build) permanece como registro do raciocínio,
  mas **deixa de ser a recomendação ativa** — mesmo sob o critério de "menor
  risco" que a gerou em 2026-08-17. Isso já vinha sob pressão da
  convergência de três vozes independentes (Carol, Luís, Gabrielle) para
  **A10 Portfolio + A14 PM Agent**; esta decisão fecha explicitamente o
  capítulo A5 em vez de deixá-lo em aberto por omissão.
- **[[How to implement A5 Watcher]]**: plano de build concreto, mas não é
  trabalho ativo agora — marcado `status: deferred`.
- **[[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]**: o
  tema Watcher/A5 já tinha sido rebaixado a mais uma linha da tabela, não o
  item de abertura — esta decisão confirma essa mudança.

## O que isso não muda
- Nenhum raciocínio técnico é invalidado — só deixa de ser prioridade.
- Se A5 voltar a ficar relevante (proxy com tráfego real de produção, GC-5
  concluído), o design já existe e pode ser retomado sem refazer o
  trabalho.

## Por quê
Não detalhado por msilva além de *"é só mais um agente, não precisamos
focar nele"* — registrado como a razão dada, sem inferir motivação
adicional.

> [!tip] Reforçado pela Carol em primeira mão, mesmo dia
> [[2026-08-24 Agent Flow discovery with Carol]]: a convergência citada
> acima vinha até então de segunda mão (relatada por msilva). Nesta
> conversa, Carol confirma diretamente sua própria opinião: tudo que é
> "mais voltado para monitoramento" tende a ser menos prioritário pra ela,
> porque os problemas normalmente ainda são alcançáveis e corrigíveis na
> mão, rápido o suficiente, sem um watcher automatizado. Não muda a
> decisão — só troca uma voz relatada por uma voz direta.
