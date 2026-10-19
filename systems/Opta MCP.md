---
type: system
status: active
updated: 2026-10-19
aliases: [opta-mcp, mcp da opta, conector da opta]
tags: [opta, mcp, cloud-run, typescript]
---

# Opta MCP

Servidor MCP que expõe os dados da [[Opta]] como ferramentas pro Claude e outros
agentes. Repo `tech-livemode/opta-mcp`, Node 22 + TypeScript com o SDK oficial do
MCP, no Cloud Run desde 2026-03-19. O relatório de uso de 2026-08-25 aponta tráfego de
agente como principal origem do que passa pelo log `opta-proxy` — o que **não** o
torna o maior consumidor do contrato: o fluxo n8n do [[Golzinho]] chama a Opta por
fora e não é medido ali (ver [[Opta API]] § Auditoria).

> [!note] Verificado direto no código em 2026-10-16
> Leitura do repo (último commit `9d17349`). Onde a doc interna e o código
> divergem, está marcado.

## Como funciona

- **Stateless por decisão.** Cada `POST /mcp` descarta qualquer `mcp-session-id`
  que o cliente mande, cria um `McpServer` novo e um transporte sem sessão
  (`sessionIdGenerator: undefined`). Combina com scale-to-zero: qualquer instância
  atende qualquer request. Custo: zero cache, duas perguntas iguais viram duas
  chamadas à Opta.
- **Config-driven.** `competitions.json` é lido no boot, vira um `z.enum` com os
  nomes das competições, e a lista é interpolada na descrição de cada ferramenta —
  é assim que o Claude acerta o nome exato sem chamar `list_competitions`.
- **Tradução de domínio** é onde está o valor real. `get_schedule` converte
  `from`/`to` em `mt.mDt=[início TO fim]`, formato que a Opta exige;
  `get_top_performers` corta cada ranking em 10 jogadores pra resposta não estourar
  o contexto do agente.
- **Erro vira texto, não HTTP.** Falha de ferramenta devolve `isError: true` com
  HTTP `200` — quem lê o erro é o agente, que pode se recuperar.
- **Credencial da Opta cacheada 5 minutos** em memória por instância
  (`lib/bigquery.ts`) — diferente da [[Opta API]], que lê do BigQuery a cada
  chamada.

## Ferramentas

São **seis**: `list_competitions`, `get_schedule`, `get_standings`,
`get_match_details`, `get_squads`, `get_top_performers`.

> [!warning] O README do repo está desatualizado
> Ele anuncia uma sétima ferramenta, `opta_raw`, removida no commit `4671fc4`
> (2026-03-19). Quem lê o README pra configurar um agente erra. A doc interna já
> diz seis.

## Autenticação

Uma **única** chave, `MCP_AUTH_KEY`, aceita em `Authorization: Bearer` ou na query
`?key=` — a URL do conector distribuída às pessoas já leva a chave no `?key=`.

Três limitações estruturais disso:

1. Não dá pra revogar uma pessoa sem rotacionar pra todo mundo.
2. Não dá pra saber quem chamou o quê: toda chamada do MCP loga
   `apiKey="mcp-call"`.
3. **Se a variável não estiver definida, o servidor sobe sem autenticação
   nenhuma** (`server.ts:318`, `if (MCP_AUTH_KEY)`). A proteção é procedural:
   conferir `hasAuthKey: true` na linha `Server started` depois de cada deploy.

Sugestão registrada na doc, não implementada: lista de chaves rotuladas nos moldes
do proxy, com o rótulo indo pro campo `apiKey` do log.

## Limites de propósito

- Só a **edição corrente** de cada competição — um `tmcl` fixo por entrada do
  `competitions.json`. Histórico só pela [[Opta API]].
- Só **18 competições**, fixas em arquivo, atualizadas à mão direto em produção.
- **Sem lances ao vivo, xG, posse ou estatística de partida** — são feeds do
  contrato que o MCP não expõe.
- **Sem cache e sem estado**: cada pergunta vai à Opta na hora.

## Bugs conhecidos

- `get_schedule` com **51% de sucesso em 246 chamadas** no relatório de
  2026-08-25. Causa provável mapeada na doc: `status` (em `get_schedule`) e
  `includeLineups` (em `get_match_details`) só são aceitos pela Opta junto com
  `live=yes`, que o servidor não envia. Correção pendente — `PRO-680`, `PRO-682`.
- ~88 respostas `401` em 30 dias, de origem não identificada — só os request logs
  do Cloud Run as registram, e sem identificar o cliente.

## Histórico

| Data | O quê |
| --- | --- |
| 2026-03-19 | Primeiro commit e entrada no ar; `opta_raw` removido no mesmo dia |
| 2026-03-25/26 | Stateless de verdade, logging estruturado, corte de 10 em top performers |
| 2026-03-26 | Anunciado no `#live-innovation` como MCP ativo no Claude corporativo |
| 2026-06-16 | Chamadas à Opta passam a ir pro mesmo log de auditoria do proxy (`ef8a289`) |
| 2026-09-21 | Competições atualizadas pra 2026/27 e correção do filtro de datas, por [[Luís Fernandez]] |
