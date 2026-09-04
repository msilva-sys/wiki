---
type: meeting-prep
status: active
updated: 2026-09-04
date: 2026-09-04
attendees: [Matheus Silva, Carolina Bezerra]
tags: [agent-flow, a10, a14, métricas]
---
# Prep — Carolina × Matheus (2026-09-04)

Prep pontual para a reunião "Carolina / Matheus" às 17h (Sala Handebol). Pauta:
métricas de saúde de portfólio (A10), métricas de progresso de projeto (A14), e
onde essas entregas de insight acontecem hoje (Linear? Slack?). Puxada de
[[Agent Flow]], [[Fronteira A10×A14 (informação e métricas)]] e das decisions
de 2026-09-01/02/03.

## Já resolvido (não precisa perguntar)

| Pergunta | Resposta já auditada |
|---|---|
| Como o A10 calcula capacidade por iniciativa? | `capacity_share` = issues ativas da iniciativa ÷ total de issues ativas do backlog do time — não usa Estimate. Código: `a10/rules.py:87-118`. |
| Como o A10 calcula prioridade média? | Média aritmética simples do campo `priority` bruto do Linear (0=No priority...4=Low), sem tratar o `0`. |
| Onde o A10 publica hoje? | Status update nativo do projeto no Linear (`projectUpdateCreate`) — revertido de comentário por issue em 2026-09-01 por reclamação do Luís. |
| Onde o A14 publica hoje? | Também via `projectUpdateCreate` — mas o log de 2026-08-31 registrava "A14 em comentário na issue", que parece defasado; confirmar contra o código antes de afirmar pra ela. |
| O Slack já recebe algo? | Indiretamente: projetos do Linear podem ser plugados a canais do Slack, e o cron do A10/A14 já publica alertas de falha que aparecem automaticamente lá (confirmado ao vivo em 2026-09-03). Não há push de insight dedicado além disso. |

## Perguntas reais para a Carol

**A10 — saúde de portfólio**
- A `avg_priority` bruta (sem tratar `0 = sem prioridade` como indefinido) é confiável pra ela hoje, ou é um viés que ela já conhece pelas issues mal preenchidas?
- O painel mais amplo que está desenhado (capacidade alocada, concentração de risco, iniciativas paradas há N ciclos) ainda é o que ela esperaria ver, dado o que ela disse em 2026-08-24 sobre priorização ser "inteligência transversa"?
- Falta o loop A14→A10 (efeito medido de uma entrega não volta pro A10). Ela já sente falta disso na prática?

**A14 — progresso de projeto**
- As métricas propostas (lead time aprovação→uso, aderência a prazo, retrabalho, "efeito medido na área") fazem sentido pra ela, ou falta/sobra alguma?
- Granularidade é entrega/tarefa dentro da iniciativa — é o nível que ela acompanha, ou ela olha mais fino (subtask) ou mais grosso (iniciativa)?

**Entrega dos insights**
- Status update nativo do Linear já é suficiente pra ela, ou ela realmente quer um canal do Slack dedicado (não só o alerta de falha do cron)? Ficou em aberto — "disponível, não configurado" em [[Agent Flow]].
- Se quiser Slack dedicado: canal específico, ou o canal geral do time resolve?
- Discrepância comentário-vs-status-update do A14 — ela tem preferência, já que foi reclamação do Luís que mudou o formato do A10?

## Contexto de fundo (lembrar, não perguntar)
- Carol diverge do Luís sobre "transversalidade": pra ela priorização é transversa, análise de uso não é (2026-08-24) — pode colorir a resposta dela sobre o painel do A10.
- Foi ela quem confirmou que o status readout atual do Linear não cobre priorização cross-projeto, só dá visibilidade do que cada um está fazendo.
