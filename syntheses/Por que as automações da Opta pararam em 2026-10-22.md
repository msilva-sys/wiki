---
type: synthesis
status: active
updated: 2026-10-26
date: 2026-10-26
aliases: [incidente da opta, quebra das automações da opta, troca de credencial da opta]
tags: [opta, n8n, bigquery, incidente, credencial]
---

# Por que as automações da Opta pararam em 2026-10-22

Pedido de msilva em 2026-10-26: *"alguém trocou a credencial e algumas automações
do n8n relacionados a opta quebraram. eu queria entender se a opta-api e o mcp
foram afetados"*. A investigação foi feita na mesma data, só leitura, com a conta
`tech@livemode.com` passada por `--account` em cada comando. A conta pessoal de
msilva não tem leitura de logs em nenhum dos dois projetos (`PERMISSION_DENIED`),
e o acesso foi pedido em `PRO-1213`.

Horários em UTC; Brasília é UTC-3.

## Conclusão

- **[[Opta API]]: não foi afetada no fim de semana.** Em `southamerica-east1` só
  respondeu `200` em 24 e 25/10, sem nenhum `401`, `403` ou `5xx`. Ninguém fez
  deploy depois de 2026-10-20, e ela lê o BigQuery com uma SA própria
  (`opta-api@`) que não mudou.
- **[[Opta MCP]]: afetado pouco, só na sexta.** 9 respostas `HTTP_401` da Opta em
  23/10 (11:36, depois de 18:45 a 20:45), todas antes da renovação das 21:45. No
  fim de semana teve só `400`/`404` em `get_schedule`, no nível dos dias úteis.
- **[[Golzinho]]: parado de 22/10 23:53 até 26/10 16:17**, ~3,7 dias sem ler a
  chave e, portanto, sem atualizar gols e partidas. Ele chama a Opta direto, fora
  da [[Opta API]].
- **A quebra começou na quinta à noite, não no fim de semana.**

## Causa provável

O n8n acessava o BigQuery com a credencial "Tech - Conta de Servico" autenticada
como **`tech@livemode.com`**. Essa autenticação parou de funcionar entre 22/10
23:00 e 23:53. Daí vieram duas falhas encadeadas:

1. O [[Atualizador do Token Opta]] parou de renovar o token e a tabela
   `opta_chaves_api` ficou ~23h sem linha nova, de 22/10 23:00 a 23/10 21:45.
2. O token venceu no meio de 23/10, e o MCP recebeu `401` da Opta.

Na sexta à tarde o time migrou a credencial para uma service account nova. A
renovação voltou às 21:45, mas o [[Golzinho]] só voltou a ler a chave em 26/10
16:17.

(unverified: **por que** a credencial `tech@` parou. Não há rastro nos logs do
GCP. Compatível com troca de senha ou encerramento de sessão da `tech@`: em
2026-10-26 o `gcloud` local também exigiu login novo nessa conta. Confirmável no
log de auditoria do Google Workspace Admin.)

## Linha do tempo (UTC)

| Quando | O quê | Fonte |
| --- | --- | --- |
| 22/10 23:00 | Última renovação do token com a credencial antiga | `insert_ts` |
| 22/10 23:53 | Última leitura da chave pelo n8n como `tech@livemode.com` (user agent `n8n`). Em 22/10 foram 2957 leituras | auditoria BigQuery (`data_access`) |
| 23/10 ~01:00 | O volume do proxy na chave de cliente dominante cai de centenas para ~24 por hora | log `opta-proxy` |
| 23/10 03:00–19:00 | Cinco renovações não acontecem | `insert_ts` |
| 23/10 11:36, 18:45–20:45 | `opta-mcp` com `HTTP_401` da Opta | log `opta-proxy` |
| 23/10 17:24–17:26 | `atavares@livemode.com` cria as SAs `n8n-fluxos` e `n8n-bigquery`, dá a `n8n-bigquery` os papéis `bigquery.dataEditor`, `dataViewer` e `jobUser`, e cria uma chave para ela | auditoria IAM |
| 23/10 19:10 | `atavares@` dá `roles/owner` a `cbezerra@` no `data-analytics-422617` | auditoria IAM |
| 23/10 21:01–21:44 | Todos os fluxos `[OPTA]` do n8n editados; o Atualizador ganha versão nova | n8n `updatedAt` e histórico |
| 23/10 21:45 | Renovação volta, agora pela SA `n8n-bigquery` | `insert_ts` |
| 24–25/10 | Renovação a cada 4h; só a das 15:00 de 25/10 falta | `insert_ts` |
| 26/10 15:50–15:52 | 6 leituras da chave como `tech@`, via `bq query` por um agente Claude Code (não desta investigação) | auditoria BigQuery |
| 26/10 16:16 | [[Golzinho]] editado (versão nova, autor "TI Tech" no n8n) | histórico n8n |
| 26/10 16:17 | Primeira leitura da chave pela SA `n8n-bigquery` | auditoria BigQuery |

Nenhuma permissão foi **removida** de 22 a 26/10: todas as alterações de IAM
foram `ADD`. `atavares@` é provavelmente [[Arthur Tavares]] e `cbezerra@`,
[[Carolina Bezerra]] (unverified: e-mail × pessoa não conferido).

## O que não ficou provado

- **O motivo exato da parada do [[Golzinho]].** Não houve nenhuma tentativa de
  leitura, nem com erro, entre 22/10 23:53 e 26/10 16:17. Ou o fluxo estava
  desligado, ou falhava antes do nó BigQuery. O n8n não guarda histórico
  suficiente para dizer qual: a execução mais antiga do Golzinho é de 26/10
  16:17, e a do Atualizador é de 26/10 03:00.
- **Os outros fluxos `[OPTA]`**, em especial `Dados dos Jogos BigQuery`
  (`46GjD3BxFZ0Z5sZW`) e `Tempos dos Jogos` (`bUVKdVvdv309ixPB`). Os dois estão
  ativos e não têm nenhuma execução registrada desde 22/10. Não verificado.
- **Os `401` da [[Opta API]] em 23/10.** Foram 16 às 13h em `/standings` e alguns
  às 14h em rotas de teste (`/v1/rota-inexistente-zzz`). Parecem chave de cliente
  errada ou teste, não token vencido. Mas o log não grava o status devolvido pela
  Opta (`PRO-695`), então não dá para separar os dois casos.

## Recomendações

1. Conferir os demais fluxos `[OPTA]`: se estão ativos e usando a credencial nova.
2. Criar alerta de "token não renovado há mais de ~5h" sobre `insert_ts`.
3. Tirar o segredo do contrato do nó `Code` do [[Atualizador do Token Opta]] e
   avaliar a rotação.
4. Aposentar a credencial antiga da `tech@` no n8n.
5. Aumentar a retenção do histórico de execuções dos fluxos da Opta.

Nenhuma delas virou issue ainda (2026-10-26).
