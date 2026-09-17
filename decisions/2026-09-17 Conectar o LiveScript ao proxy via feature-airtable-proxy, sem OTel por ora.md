---
type: decision
status: active
updated: 2026-09-17
aliases: [decisão de branch do proxy, feature/airtable-proxy]
tags: [airtable-proxy, livescript, git]
---

# Conectar o LiveScript ao proxy via `feature/airtable-proxy`, sem OTel por ora

**Decidido 2026-09-17, msilva.** Resolve a "confusão de branch" registrada em
[[2026-09-15 Proxy e Fluxo Agêntico com Luís]] e verificada no repo real em
2026-09-16: nenhuma das duas branches reais do `livemode-roteiros-nextjs`
estava pronta como estava —

- `feature/airtable-proxy`: limpa, sem OTel, mas também sem a autenticação
  por API key (anterior a `PRO-96`/`PRO-587`);
- `feature/airtable-proxy-observability`: tinha OTel de verdade **e** os
  commits recentes necessários (`7570ee6` API key, `0af8bc4` fix REST da
  `PRO-96`) — mas carregava o peso de observabilidade que preocupava Luís.

## O que foi feito

Não foi um cherry-pick pontual do `7570ee6` — em 2026-09-16, Matheus (com
Claude Code) fez uma **reimplementação completa** do roteamento pelo proxy
em cima da `feature/airtable-proxy` limpa: um helper novo
(`lib/services/airtable-proxy-env.ts`) substitui
`AIRTABLE_PERSONAL_ACCESS_TOKEN`/`api.airtable.com` por
`AIRTABLE_PROXY_KEY`/`AIRTABLE_PROXY_URL` em **19 arquivos**
(`83d1a7f feat(airtable): route all Airtable calls through the LiveMode
proxy`), seguido de um ajuste nos scripts de manutenção
(`84cea6f fix(scripts): migrate maintenance commands to proxy credentials`).

**Reimplementação completa e paralela do fix da `PRO-96`, confirmado
2026-09-17** — não um gap fechado por acaso. A `PRO-96` já mapeava, com
arquivo e linha exatos, os mesmos 7 pontos REST hardcoded pra
`api.airtable.com` (`airtable-helpers.ts`, `airtable.service.ts`,
`script-base.service.ts`, `config.service.ts`) e já tinha um fix real,
testado e validado no Grafana, comentado em 2026-08-26: `0af8bc4`, na
branch `-observability` — mas resolvia os 7 pontos de forma **centralizada**,
reescrevendo a URL dentro dos wrappers compartilhados
(`fetchAirtableWithMonitoring`/`requestAirtableJsonWithMonitoring`) via uma
função `resolveAirtableUrl()` lendo `AIRTABLE_ENDPOINT_URL`.

O `83d1a7f` resolve **o mesmo problema, os mesmos 7 pontos**, mas editando
cada call site diretamente pra usar `getAirtableRestBaseUrl()`, com env vars
renomeadas (`AIRTABLE_PROXY_KEY`/`AIRTABLE_PROXY_URL`, não mais
`AIRTABLE_ENDPOINT_URL`/`AIRTABLE_PERSONAL_ACCESS_TOKEN`). Como a
`-observability` fica descartada como fonte, isso não é regressão: é o
mesmo trabalho refeito do zero, do jeito novo, sem depender do fix antigo.
`PRO-96` segue correta como `Done` — só o comentário dela referenciando
`0af8bc4`/`-observability` fica órfão de uma branch que não vai mais ser
usada; comentário novo adicionado na issue linkando pro `83d1a7f`. Ver
[[Airtable Proxy]].

## O que fica decidido

`feature/airtable-proxy` é a branch a usar daqui pra frente pra conectar o
LiveScript ao proxy — inclusive pra Yasmin testar. `feature/airtable-proxy-
observability` fica descartada como fonte desse trabalho; observabilidade do
lado do LiveScript (OTel) fica **deferida, não descartada** — reavaliar
quando fizer sentido, sem compromisso de quando.

O item de "Things to actually do" da página do projeto ("abrir e mergear o PR
de `feature/airtable-proxy-observability`, fecha `PRO-587`") fica obsoleto —
o PR real agora sai da `feature/airtable-proxy`, ainda não aberto.

## Relacionado

- [[Airtable Proxy]]
- [[LiveScript]]
- [[2026-09-15 Proxy e Fluxo Agêntico com Luís]]
- [[Resolver rollback do LiveScript com checagem de variáveis de ambiente]]
