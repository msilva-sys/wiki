---
type: source
status: stable
updated: 2026-10-22
date: 2026-10-13
source: "raw/relatório de riscos e melhorias sobre o proxy.md"
aliases: [riscos do proxy, leaky bucket]
tags: [airtable-proxy, airtable, rate-limits, pat]
---

# Relatório de riscos e melhorias sobre o proxy

Duas perguntas do msilva sobre o [[Airtable Proxy]], anotadas como começo
de um relatório de riscos (`raw/relatório de riscos e melhorias sobre o
proxy.md`). *(unverified: 2026-10-13 vem do mtime do arquivo.)* As duas já
têm resposta na wiki.

## Perguntas e respostas

- **"Ao gerar um PAT, especificamos as bases que essa chave acessa. Essas
  bases são tabelas?"** Não. Uma base contém tabelas; o PAT é limitado por
  base, mais os escopos de permissão. Controlar acesso por esse escopo, e
  não por lógica no proxy, é decisão registrada em
  [[2026-08-24 Control base access via PAT scoping, not proxy logic]].
- **"Um PAT, quando estressa o Airtable, derruba a base pra todos?"** Sim,
  na prática. O limite de **5 req/s é por base**, independente de quem
  chama: um consumidor pesado esgota a cota e todo cliente daquela base
  recebe 429 por cerca de 30 s. E os 50 req/s somam todo o tráfego de PATs
  do mesmo emissor. Detalhe em [[Airtable Rate Limits]].
- **"Cabe uma estratégia de leaky bucket no proxy?"** A wiki registra o
  rate limiting com token bucket como trabalho real, mas de fase 5, depois
  da observabilidade ([[Airtable Proxy]]). O proxy também pretende absorver
  os 429 com retry/backoff, ainda não construído.
