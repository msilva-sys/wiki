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
  commits recentes necessários (`7570ee6`, API key) — mas carregava o peso de
  observabilidade que preocupava Luís.

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

**Achado colateral, não confirmado**: os 19 arquivos tocados incluem
`config.service.ts`, `narrator.service.ts` e `script-base.service.ts` — os
mesmos 4 arquivos que o achado de `PRO-96` (2026-08-26) tinha deixado como
gap aberto (chamadas REST hardcoded pra `api.airtable.com`, fora do alcance
do `AIRTABLE_ENDPOINT_URL` do SDK). Pode fechar esse gap por tabela, mas
**não testado** — só lido o diff, não rodado. Ver [[Airtable Proxy]].

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
