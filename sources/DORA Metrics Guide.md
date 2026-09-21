---
type: source
status: active
updated: 2026-09-21
date: 2026-09-21
source: "live WebFetch"
url: "https://dora.dev/guides/dora-metrics/"
aliases: [DORA metrics, DevOps Research and Assessment metrics]
tags: [dora, metrics, agent-flow, a10]
---

# DORA — "DORA Metrics" guide

Página de referência (evergreen, sem data de publicação visível — data
acima é a de ingestão, `(unverified: página não traz data própria)`) do
site oficial da DORA (DevOps Research and Assessment, hoje mantido pelo
Google). Lida a pedido de msilva, motivada pela pergunta em aberto em
[[What Bossabox's Assessment suggests for Agent Flow]] sobre A10 Portfolio
adotar DORA/VSM como vocabulário de métrica.

## As cinco métricas

O framework atual da DORA tem **cinco métricas**, não as quatro
"clássicas" (deploy frequency, lead time, change failure rate, recovery
time) já citadas nesta wiki via o deck da Bossabox
([[Bossabox Assessment - institutional deck]]) — ver nota de correção
abaixo. Divididas em duas categorias:

**Throughput (3)**

1. **Change Lead Time** — tempo do commit no controle de versão até o
   deploy em produção.
2. **Deployment Frequency** — número de deploys num período, ou o tempo
   entre eles.
3. **Failed Deployment Recovery Time** — tempo pra recuperar de um deploy
   que falhou e exigiu intervenção imediata.

**Instability (2)**

4. **Change Fail Rate** — proporção de deploys que exigem intervenção
   imediata (rollback ou hotfix).
5. **Deployment Rework Rate** — proporção de deploys **não planejados**,
   feitos em reação a um incidente em produção. Essa é a métrica nova em
   relação ao framework clássico de 4 — antes não existia separada do
   Change Fail Rate.

## Sem faixas numéricas nesta página

Ao contrário do que eu esperava encontrar, a página **não define
thresholds** (Elite/High/Medium/Low) — não há benchmark quantitativo
citado aqui. Se esses números importarem pro A10, precisam vir de outra
fonte da DORA (o relatório anual "Accelerate State of DevOps", não esta
página-guia).

## Guidance central

- **"Speed and stability are not tradeoffs"** — throughput e instability
  juntos formam o quadro; um time "elite" se destaca nas cinco ao mesmo
  tempo, um time fraco falha nas cinco.
- **Medir por aplicação/serviço**, não agregado por time ou organização —
  agregar borra o sinal.
- **Evitar virar meta rígida** (Goodhart's Law) — as métricas descrevem,
  não devem virar alvo que se joga pra bater.
- **Propriedade cruzada** — as cinco devem ser compartilhadas entre
  dev/ops/release, não viram silo de um time só.
- Funcionam como **indicador antecedente** de performance organizacional e
  **indicador posterior** de prática de engenharia.
- Fluxo de implementação sugerido: baseline (Quick Check) → identificar
  atrito → comprometer-se com melhorias → plano mensurável → executar →
  acompanhar → repetir.

## Correção — framework clássico de 4 vs. atual de 5

[[Bossabox Assessment - institutional deck]] (lido antes desta página) cita
DORA como "frequência de deploy, lead time, taxa de falha, tempo de
recuperação" — o framework clássico de 4 métricas, amplamente conhecido.
Não é um erro de leitura daquele deck (é assim que a Bossabox o
apresentou) — é o framework oficial da DORA tendo evoluído pra 5,
adicionando **Deployment Rework Rate** como categoria própria. Vale citar
a versão de 5 daqui pra frente quando a fonte for a DORA diretamente;
manter a de 4 como o que a Bossabox especificamente citou, sem reescrever
aquela página.

## Relevância pro Agent Flow

Responde parte da pergunta em aberto em
[[What Bossabox's Assessment suggests for Agent Flow]] ("A10 Portfolio
deveria adotar DORA/VSM como vocabulário de métrica?") — agora há uma
definição oficial e precisa pra citar, não só o exemplo do deck de vendas
da Bossabox. Não decide a pergunta (ainda em aberto se o formato da
Livemode precisa de vocabulário próprio), só dá a base factual mais sólida
pra decidir.
