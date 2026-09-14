---
type: meeting-prep
status: active
updated: 2026-09-14
date: 2026-09-14
attendees: [Gabrielle Ferreira, Luís Fernandez, Carolina Bezerra, Maria Fernanda Lemos, Yasmin Macedo, Arthur Tavares, João Victor Andrade, Matheus Silva]
tags: [weekly, linear, proxy, agent-flow, a1-a2]
aliases: [Weekly 2026-09-14]
---
# Prep — Weekly - Projetos e Tarefas (2026-09-14)

Recorrente, segunda 14h — **prospectiva**: o que vou trabalhar essa semana, não status do que já foi feito (isso é o Recap de sexta, ver [[2026-08-14 Recap da Semana]]). Última ocorrência: [[2026-08-17 Weekly - Projetos e Tarefas]]. Primeiro prep gerado com a skill nova `/weekly-prep`.

## Resumo rápido da semana passada

- Épico de preparação do proxy pra produção fechado — auth por API key, Secret Manager, IaC Pulumi, telemetria Grafana Cloud, canal de notificação (PRO-553, 90, 91, 568, 117, 569).
- 1:1 com Luís (10/09): 6 decisões fechadas sobre o molde de agente (SOUL) do A10/A14 — ver [[Agent Flow]].

## No que vou trabalhar essa semana

| Item | Por quê está na fila | Ref |
|---|---|---|
| Validar o deploy do proxy em produção — consumidor real, uptime check sintético, alerta real, conferir dados vs. Airtable direto | Deploy já está no ar; falta a validação pra fechar de vez | PRO-88, 89, 95, 97 |
| Abrir e mergear o PR de `feature/airtable-proxy-observability` | Código já pronto e pushado (`7570ee6`); fecha a issue | [[Airtable Proxy]], PRO-587 |
| Acompanhar o início do dev de A1 & A2 | Começa amanhã, 15/09 | Projeto A1 & A2, milestone M0 - Discovery |

## Pontos rápidos pro grupo

- Trial do Linear (bloqueava criação de issue desde 09/09) foi resolvido — upgrade feito. Ver [[Linear Project Structure]].
- Proxy já está no ar em produção (Cloud Run), mas nenhum serviço aponta pra ele ainda.
