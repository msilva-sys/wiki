---
type: synthesis
status: active
updated: 2026-09-04
aliases: [grafana vs cloud monitoring, telemetria de produção do proxy, PRO-87]
tags: [airtable-proxy, observability, grafana, cloud-monitoring, gcp, otel]
---

# Grafana hospedado vs Cloud Monitoring para telemetria de produção

Investigação pedida por Luís na [[2026-09-02 1-1 Matheus - Luís]], rastreada em
[PRO-87](https://linear.app/projetos-livemode/issue/PRO-87/apontar-otlp-para-backend-de-producao-sem-mudar-codigo-do-proxy)
(`In Progress`, sub-issue de `PRO-84`). Luís deu um palpite, não uma decisão:
*"Cloud Monitoring provavelmente mais barato; Grafana hospedado exige mais
preocupação de infraestrutura."* Esta página é o aprofundamento prometido no
comentário de 2026-09-02 da issue, antes de fechá-la.

## Dependência com PRO-84 — ainda sem resposta do Luís

Esta análise está acoplada à decisão de deploy do proxy em si, ainda em aberto:
[[Airtable Proxy]] tem uma [análise de custo VM vs. Cloud Run](https://linear.app/projetos-livemode/issue/PRO-84/deploy-em-producao-cloud-run)
publicada em 2026-09-04 (números reais do GCP Pricing Calculator,
`southamerica-east1`):

| | VM (`e2-small`, sem HA) | Cloud Run (`min=1 max=3`, com HA) |
|---|---|---|
| Compute | US$ 20,91/mês | US$ 15,00/mês |
| Load Balancer interno | — | US$ 55,84/mês |
| **Total** | **US$ 20,91/mês** | **US$ 70,84/mês** |

Essa análise termina com uma pergunta em aberto: o Load Balancer interno
(exigido pela arquitetura VPN) seria dedicado só ao proxy, ou compartilhado
com outro serviço privado? **O Grafana em Cloud Run é uma resposta candidata
a essa pergunta** — ver seção de opções de hospedagem abaixo. Nenhuma das
duas decisões (deploy do proxy, hospedagem do Grafana) está fechada; o Luís
ainda não respondeu.

## As três opções de hospedagem, se "Grafana hospedado" for o caminho

Mapeadas contra a tabela de custo acima:

1. **Grafana na VM do proxy** — só existe se o proxy for pra VM. Custo
   marginal ≈ zero: sem Load Balancer (VM tem IP interno acessível direto
   via VPN), só disco extra pra retenção do LGTM e talvez subir de
   `e2-small` pra `e2-medium` pela memória. A mais barata das três, mas
   presa à decisão do `PRO-84`.
2. **Grafana em VM própria** — independe da decisão do proxy. Mesma
   arquitetura (sem LB), mas paga uma VM inteira só pra isso — outros
   ~US$ 20,91/mês de compute + disco.
3. **Grafana em Cloud Run** — só é barato se o proxy também estiver em
   Cloud Run (compartilha o mesmo Load Balancer/backend service via URL
   map; custo marginal de LB ≈ zero). Se o proxy for pra VM, paga sozinho
   os US$ 55,84/mês de LB — a mais cara das três nesse cenário.

## Banco de dados e durabilidade — decidido

Ponto levantado em conversa: o que precisa sobreviver a destruição de VM ou
troca de infra, e como. Duas coisas distintas, resolvidas separadamente:

- **Configuração do Grafana** (dashboards, alertas, datasources) — já
  resolvido de graça pelo padrão *dashboards as code* que o projeto já
  pratica (`PRO-93`, `grafana/dashboards/*.json` versionados, ver
  [[Airtable Proxy]]). O banco interno do Grafana (SQLite local, nas opções
  1 e 2; teria que ser Cloud SQL na opção 3, ~US$ 50-70/mês só pra isso) fica
  descartável — reprovisiona do zero a partir do código, em qualquer uma das
  três opções.
- **Dados de telemetria** (as métricas/logs/traces em si) — não moram no
  banco do Grafana, moram no storage de cada componente do LGTM:
  - Loki e Tempo suportam Google Cloud Storage nativamente como backend —
    [Loki storage docs](https://grafana.com/docs/loki/latest/configure/storage/),
    [Tempo GCS docs](https://grafana.com/docs/tempo/latest/configuration/hosted-storage/gcs/).
    Resolve durabilidade sem depender de qual VM roda o processo.
  - Prometheus (o que vem no bundle `otel-lgtm` usado localmente) **não**
    suporta GCS — dado morre com o disco local. **Decisão de msilva**: usar
    o **Google Cloud Managed Service for Prometheus (GMP)** em vez de
    Prometheus self-hosted ou de rodar Mimir/Cortex/Thanos por conta própria
    — [doc do GMP](https://cloud.google.com/stackdriver/docs/managed-prometheus).
    Compatível com PromQL/`remote_write`, dado gerenciado pelo Google, zero
    operação extra.

Essa parte (GMP + GCS + dashboards-as-code) **não depende da resposta do
Luís** sobre VM vs. Cloud Run — vale nas três opções de hospedagem.

## A pergunta real, uma vez que GMP entra em cena

**GMP é, tecnicamente, parte do Cloud Monitoring** — guarda dado no mesmo
backend (Monarch) que o Cloud Monitoring usa pras próprias métricas. Isso
esvazia a dicotomia original ("qual backend é mais barato") — as duas opções
pagam a mesma coisa pra guardar métrica. A pergunta que sobra é outra:

> Vale pagar a infra extra de rodar um Grafana (US$ 21 a 71/mês + operação),
> só pra manter os dashboards e a UX de correlação que já existem, em vez de
> usar o console nativo do Google de graça?

### Cloud Monitoring puro (sem Grafana rodando)

**Prós**
- Zero infraestrutura — nem VM, nem Cloud Run, nem LB, nem banco. É
  literalmente config no exportador OTLP, como o design original sempre
  previu.
- Totalmente gerenciado — sem patch, upgrade de versão, ou uptime próprio
  pra garantir.
- Controle de acesso via IAM do GCP — sem autenticação separada pra quem
  olha os dashboards.
- Custo por volume ingerido, não infraestrutura fixa — combina com a
  filosofia "observability-first, medir antes de decidir" do projeto.
- Painéis são configuráveis e podem ser código: [visão geral de painéis](https://cloud.google.com/monitoring/dashboards)
  confirma dashboards personalizados via console/CLI/API, e
  [`google_monitoring_dashboard`](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/monitoring_dashboard)
  (Terraform) confirma dashboard-as-code.
- O [Metrics Explorer aceita PromQL nativamente](https://cloud.google.com/monitoring/promql)
  contra dados do GMP — as queries de métrica dos dashboards atuais rodam
  sem reescrever a sintaxe, só muda o formato do painel ao redor.
- Existe [importador de painéis do Grafana pro Cloud Monitoring](https://cloud.google.com/monitoring/dashboards/import-grafana-dashboards)
  — converte o JSON automaticamente, reduzindo parte do retrabalho.
- [Log Analytics](https://cloud.google.com/logging/docs/log-analytics) (SQL
  sobre logs), [Cloud Trace](https://cloud.google.com/trace/docs/finding-traces)
  (waterfall + relatórios de latência) e [correlação nativa trace→log](https://cloud.google.com/trace/docs/trace-log-integration)
  cobrem boa parte do que os dashboards fazem hoje.
- [Alerting policies do Cloud Monitoring](https://cloud.google.com/monitoring/alerts)
  cobrem o que o `airtable_429_alert` faz, com Slack como canal.

**Contras**
- O importador de painéis **só converte painéis com PromQL + datasource
  Prometheus** — os painéis que dependem de Loki (LogQL) ou Tempo (TraceQL)
  não são importados automaticamente, teriam que ser recriados à mão.
- UX de correlação ad-hoc entre sinais (trace → logs → métrica, tudo numa
  view) ainda é um pouco menos fluida que o Grafana Explore, mesmo com a
  correlação trace-log nativa.
- Quebra a paridade com o ambiente local, que já usa Grafana (`otel-lgtm`)
  — dev e prod passam a usar ferramentas diferentes.
- Mais lock-in na camada de visualização (formato de dashboard e MQL são
  do GCP) do que PromQL/LogQL/TraceQL, que são padrões usados fora do GCP
  também.

### Grafana híbrido (self-hosted + GMP + GCS)

**Prós**
- Preserva o investimento já feito — os dois dashboards existentes
  ("Overview" e "Usage & Anti-patterns", 16 painéis, já pegando anti-padrão
  real em teste) e o padrão dashboards-as-code continuam valendo sem
  retrabalho.
- Melhor UX de correlação entre métricas/logs/traces — relevante
  especificamente pra missão de detecção de anti-padrão que é a razão de
  ser do projeto.
- Mesma ferramenta e linguagens de consulta que o ambiente local.
- PromQL/LogQL/TraceQL são padrões de mercado — menos lock-in na camada de
  consulta, mesmo com o storage (GMP) sendo do GCP.
- Durabilidade já resolvida (GMP + GCS), sem o risco de dado sumir com a VM.

**Contras**
- Ainda precisa de compute pra rodar o processo do Grafana — a única linha
  de custo que a opção pura não tem (US$ 21 a 71/mês, ver tabela acima,
  dependendo de qual das três opções de hospedagem e de compartilhar ou não
  o LB com o proxy).
- Continua sendo dono da operação do Grafana em si — versão, upgrade,
  controle de acesso próprio (login separado do IAM do GCP).
- Mais peças móveis = mais superfície de algo quebrar (rede/VPN até o
  Grafana, versão, wiring com GMP/GCS) comparado a um único console
  gerenciado.
- Paga a ingestão de métrica (GMP) **e** a infra extra — nunca menos que a
  opção pura nesse eixo, já que o backend de métrica é o mesmo dos dois
  lados.

## Estado atual

- **Decidido** (não depende do Luís): métricas no GMP, logs/traces em GCS
  via Loki/Tempo, config do Grafana como código — vale em qualquer cenário
  de hospedagem.
- **Em aberto**: se vale manter Grafana self-hosted (qualquer uma das três
  opções de compute) versus ir de Cloud Monitoring puro. O palpite original
  do Luís ("Cloud Monitoring provavelmente mais barato") se confirma em
  termos de infraestrutura pura — o contra-argumento é o retrabalho parcial
  dos dashboards (painéis de Loki/Tempo não importam automaticamente) e a
  UX de correlação.
- **Bloqueado** por `PRO-84`: qual das três opções de hospedagem do Grafana
  é viável depende de o proxy ir pra VM ou Cloud Run — resposta do Luís
  ainda pendente.

## Referências

- [PRO-87](https://linear.app/projetos-livemode/issue/PRO-87/apontar-otlp-para-backend-de-producao-sem-mudar-codigo-do-proxy) —
  issue que rastreia esta decisão.
- [PRO-84](https://linear.app/projetos-livemode/issue/PRO-84/deploy-em-producao-cloud-run) —
  análise de custo VM vs. Cloud Run do proxy (comentário de 2026-09-04),
  fonte dos números de compute/LB usados acima.
- [Visão geral de painéis — Cloud Monitoring](https://cloud.google.com/monitoring/dashboards)
- [Importar painéis do Grafana para o Cloud Monitoring](https://cloud.google.com/monitoring/dashboards/import-grafana-dashboards)
- [`google_monitoring_dashboard` (Terraform)](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/monitoring_dashboard)
- [PromQL para Cloud Monitoring](https://cloud.google.com/monitoring/promql)
- [Google Cloud Managed Service para Prometheus](https://cloud.google.com/stackdriver/docs/managed-prometheus)
- [Migrar regras de alerta e receptores do Prometheus](https://cloud.google.com/monitoring/promql/promql-migrate)
- [Log Analytics — Cloud Logging](https://cloud.google.com/logging/docs/log-analytics)
- [Encontrar e analisar traces — Cloud Trace](https://cloud.google.com/trace/docs/finding-traces)
- [Vincular entradas de registro a traces](https://cloud.google.com/trace/docs/trace-log-integration)
- [Visão geral de alerta — Cloud Monitoring](https://cloud.google.com/monitoring/alerts)
- [Storage — Grafana Loki docs](https://grafana.com/docs/loki/latest/configure/storage/)
- [Google Cloud Storage — Grafana Tempo docs](https://grafana.com/docs/tempo/latest/configuration/hosted-storage/gcs/)
- [Preços do Cloud SQL](https://cloud.google.com/sql/pricing) — usado pra
  estimar o custo do banco externo do Grafana na opção Cloud Run (rates de
  Iowa/`us-central1`, não verificado ainda contra `southamerica-east1`).
