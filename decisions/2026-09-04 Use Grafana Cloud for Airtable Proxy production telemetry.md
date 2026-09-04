---
type: decision
status: stable
updated: 2026-09-04
date: 2026-09-04
decided_by: Luís Fernandez
source: "sources/Direcionamento PRO-84 PRO-87.md (raw/2026-09-04 Direcionamento PRO-84 PRO-87.html)"
tags: [proxy, observability, grafana, cloud-monitoring, gcp, otel]
aliases: [Grafana Cloud, telemetria de produção do proxy, PRO-87]
---

# Use Grafana Cloud for Airtable Proxy production telemetry

> [!warning] Resolve [[Grafana hospedado vs Cloud Monitoring para telemetria de produção]]
> Aquela síntese mapeou as opções e ficou bloqueada esperando resposta de
> Luís. Esta página é a resposta — a síntese fica arquivada como
> `status: superseded`, com o levantamento técnico (GMP, GCS, as três
> opções de hospedagem) que continua válido como referência.

**Decisão.** A telemetria de produção do [[Airtable Proxy]] vai para o
**Grafana Cloud (free tier)** — nem Cloud Monitoring puro, nem Grafana
self-hosted (VM própria ou em Cloud Run).

## Por que não Cloud Monitoring

O importador de painéis do Google só converte painéis PromQL. O dashboard
"Overview" (`PRO-102`, 5 painéis PromQL) passaria; mas "Usage &
Anti-patterns" (`PRO-103`) moveu deliberadamente a maior parte dos 16
painéis para Loki (LogQL) — foi a solução para o `increase()` subcontar em
tráfego baixo — e o painel de N+1 é TraceQL no Tempo. Isso teria que ser
refeito à mão em Log Analytics, com correlação métrica/log/trace menos
fluida. É pagar em dias de trabalho para chegar a algo pior do que já
existe.

## Por que não Grafana self-hosted

Precisa de compute (VM ou Cloud Run) e operação própria — versão, acesso,
uptime, backup. Exatamente a peça de infraestrutura que a decisão de deploy
público em Cloud Run ([[2026-09-04 Deploy Airtable Proxy publicly on Cloud
Run with per-app API keys]]) também elimina.

## Por que Grafana Cloud

Prometheus, Loki e Tempo hospedados, com endpoint OTLP. Os dois dashboards
importam como estão (só muda o UID dos datasources), o alerta de 429 é
recriado com contact point no Slack, zero infraestrutura. Custo: US$ 0.

Limites que importam: retenção de 14 dias, 3 usuários ativos, 10k séries,
50 GB de logs/mês. Se estourar, plano Pro é US$ 19/mês + uso, ainda sem
infra.

Dois pontos de atenção aceitos:
- Os logs (incluindo body de resposta) passam a ficar num SaaS de
  terceiro.
- O gateway OTLP do Grafana Cloud é **OTLP/HTTP**; os exporters do proxy
  são gRPC fixos no código.

> [!warning] Mudança de código necessária — contradiz o título atual da `PRO-87`
> O comentário no código diz que o protocolo vem de env var, mas os
> exporters atuais ignoram `OTEL_EXPORTER_OTLP_PROTOCOL` — é preciso trocar
> para `autoexport` para falar HTTP com o Grafana Cloud, mantendo gRPC no
> ambiente local. Isso é **mudança de código**, não só configuração — a
> `PRO-87` está titulada "Apontar OTLP para backend de produção (sem mudar
> código do proxy)". Ticket precisa de correção de escopo/título; ainda não
> feito no Linear.

## Ainda em aberto

- Migrar para o plano Pro (US$ 19/mês + uso) se os limites do free tier
  (14 dias, 3 usuários, 10k séries, 50 GB logs) forem estourados — sem
  gatilho definido ainda.
- Execução: criar a stack Grafana Cloud (região US East), importar os 2
  dashboards via API, recriar o alerta 429 com contact point no Slack,
  trocar os exporters para `autoexport`.
