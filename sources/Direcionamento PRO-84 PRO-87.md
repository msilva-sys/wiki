---
type: source
status: stable
updated: 2026-09-04
aliases: [direcionamento Luís PRO-84 PRO-87]
tags: [airtable-proxy, gcp, cloud-run, grafana, decision-memo]
---

# Direcionamento PRO-84 PRO-87

Memo de Luís Fernandez para msilva (raw/2026-09-04 Direcionamento PRO-84
PRO-87.html), respondendo às duas análises em aberto que ele mesmo pediu em
[[2026-09-02 1-1 Matheus - Luís]]: custo VM vs. Cloud Run (`PRO-84`) e Grafana
hospedado vs. Cloud Monitoring (`PRO-87`).

## Premissa

Nesta fase o proxy atende só os 3 apps internos do time. Caminho mais simples
e barato para testar em produção de verdade; adicionar peças só quando o uso
real mostrar necessidade.

## Decisões

- **Hospedagem**: Cloud Run `min=1 max=3`, público (`ingress=all`). Sem Load
  Balancer interno, NEG, VPN ou VM. Ver [[2026-09-04 Deploy Airtable Proxy
  publicly on Cloud Run with per-app API keys]].
- **Exposição/segurança**: proxy aberto na internet, fronteira de segurança
  passa da rede (VPN) para uma API key por app — mesmo raio de estrago da PAT
  atual, mas com visibilidade e revogação por app. Aceito só enquanto o proxy
  atender apps internos.
- **API key**: pré-requisito de go-live, não opcional. `Authorization: Bearer
  <key>`; mecanismo de injeção já existe no `Director` do proxy, só falta
  validar em vez de descartar.
- **Região**: `us-east4` (Virgínia), ao lado do Airtable (AWS us-east-1) — não
  São Paulo.
- **URL**: `*.run.app`, sem domínio custom (domain mapping é preview/sem SLA;
  LB externo custaria ~US$ 18/mês sem necessidade agora).
- **Telemetria**: Grafana Cloud free tier, não Cloud Monitoring nem Grafana
  self-hosted. Ver [[2026-09-04 Use Grafana Cloud for Airtable Proxy
  production telemetry]]. Exige trocar exporters gRPC → HTTP
  (`autoexport`) — **isso é mudança de código**, o que contradiz o título
  atual da `PRO-87` ("sem mudar código do proxy").
- **`min=1 max=3`**: não escala com número de apps — Cloud Run escala por
  requests simultâneas. `max=3` é trava de segurança (~2.000 req/s de teto,
  bem acima do que o Airtable aceita).

## Tickets citados no memo

- `PRO-84` → Cloud Run (decisão registrada).
- `PRO-90` (memo chama de "Load Balancer interno") e `PRO-93` (painéis no
  Cloud Monitoring) → memo pede fechar ou adiar.
- `PRO-87` → Grafana Cloud.
- Ticket novo: API key por app, "substituindo o item X-Api-Key do backlog".

> [!warning] Mapeamento de tickets do memo não bate com o Linear real
> Conferido em 2026-09-04: `PRO-90` no Linear é o épico **"IaC (Pulumi)"**
> inteiro (inclui `PRO-91` Cloud Run+Secret Manager, ainda necessário, e
> `PRO-92` alertas em Pulumi) — não um ticket específico de "Load Balancer
> interno". O trecho do LB nunca virou issue própria; ficou registrado como
> escopo adiado dentro do `PRO-90` em
> [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] ("Still out of
> scope, left for a later PRO-90 sibling"). Fechar o `PRO-90` de verdade
> cancelaria trabalho ainda válido. Também não existe, hoje, um ticket aberto
> literalmente chamado "X-Api-Key" no backlog — o mais próximo é a `PRO-79`
> (Done, "Validar app por X-App-Id (X-Api-Key adiado)"). Ajuste no Linear
> ainda não feito — pendente de decisão do msilva sobre como tratar essas
> duas discrepâncias.

## Referências

- raw/2026-09-04 Direcionamento PRO-84 PRO-87.html
- [[Airtable Proxy]]
- [[2026-09-02 1-1 Matheus - Luís]] (pedido original)
- [[Grafana hospedado vs Cloud Monitoring para telemetria de produção]] (síntese resolvida por este memo)
