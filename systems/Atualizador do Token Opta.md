---
type: system
status: active
updated: 2026-10-26
aliases: [atualizador do token, renovador do token da opta, "[OPTA] Atualizador do Token Opta", opta_chaves_api]
tags: [opta, n8n, bigquery, credencial]
---

# Atualizador do Token Opta

Fluxo n8n `[OPTA] Atualizador do Token Opta` (id `fQJG1KplUhoNkWiu`, ativo, 4 nós).
É a peça de que **todo consumidor da [[Opta]] depende** e que nenhuma página do
wiki tinha mapeado até 2026-10-26: ele produz o que todo mundo chama de "chave da
Opta" no BigQuery.

> [!note] Verificado em 2026-10-26
> Lido pelo MCP do n8n (`get_workflow_details`) e conferido contra a coluna
> `insert_ts` da tabela (só essa coluna; `key` não foi lida). Durante a
> investigação de [[Por que as automações da Opta pararam em 2026-10-22]].

## O que a "chave" no BigQuery realmente é

A tabela `data-analytics-422617.bronze_layer.opta_chaves_api` **não guarda uma chave
de API fixa**. Guarda um **bearer token de vida curta**, renovado a cada 4 horas:

```
Schedule (4h) ─► Code: hash SHA-512 de outlet + timestamp + segredo
              ─► POST oauth.performgroup.com/oauth/token/{outlet}
                   grant_type=client_credentials, scope=b2b-feeds-auth
              ─► BigQuery insert em opta_chaves_api (key = access_token, insert_ts = agora)
```

Os consumidores leem sempre a linha mais recente
(`SELECT key … ORDER BY insert_ts DESC LIMIT 1`):

| Consumidor | Como lê |
| --- | --- |
| [[Opta API]] | SA `opta-api@data-analytics-422617`, via `GCP_SERVICE_ACCOUNT_JSON` |
| [[Opta MCP]] | `GOOGLE_APPLICATION_CREDENTIALS_JSON` ou ADC; principal não identificado nos logs |
| [[Golzinho]] | credencial n8n "Tech - Conta de Servico" |

Consequência: **se este fluxo parar, a Opta inteira para algumas horas depois**,
para todos os consumidores ao mesmo tempo. A validade exata do token não foi
medida. Na falha de 2026-10-22, o último token gravado (2026-10-22 23:00 UTC) já
dava `401` na Opta às 11:36 UTC do dia seguinte. Isso dá entre ~12 e ~20 horas de
folga (unverified: a validade nominal está na resposta do OAuth, campo
`expires_in`, que não foi lida).

## Credencial

O nó BigQuery usa a credencial n8n **"Tech - Conta de Servico"** (id
`EAzjg2lmgcEVzqjn`). Até 2026-10-22 as escritas e leituras saíam como
`tech@livemode.com`. Desde 2026-10-23 21:45 UTC saem pela SA
`n8n-bigquery@data-analytics-422617`, criada no mesmo dia. Ver
[[Por que as automações da Opta pararam em 2026-10-22]].

## Riscos

- **O segredo do contrato está em texto claro no nó `Code`.** O `outlet_key` e o
  `secret_key` da Opta estão escritos no código Python do fluxo. Qualquer pessoa
  com leitura no n8n vê, e todo export vaza de novo. Nenhum valor foi registrado
  neste wiki. Correção: mover para uma credencial do n8n e avaliar a rotação com
  a Opta. Não tem issue no Linear (2026-10-26).
- **Sem alerta de renovação.** Em 2026-10-23 a tabela ficou ~23h sem linha nova e
  ninguém percebeu até as automações quebrarem. Bastaria um alerta quando o
  `insert_ts` mais recente tiver mais de ~5h.
- **Histórico de execução curto.** `saveDataSuccessExecution: "all"`, mas em
  2026-10-26 a execução mais antiga disponível era das 03:00 UTC do mesmo dia,
  apesar de a tabela provar execuções no fim de semana. Não dá para auditar
  falhas passadas pelo n8n; o rastro confiável é o `insert_ts`.
- **Ponto único de falha** para [[Opta API]], [[Opta MCP]] e [[Golzinho]] juntos.

## Histórico

| Data | O quê |
| --- | --- |
| 2025-10-23 | Fluxo criado |
| 2026-10-22 23:00 UTC | Última renovação com a credencial antiga (`tech@`) |
| 2026-10-23 21:44 UTC | Versão nova do fluxo; credencial passa a ser a SA `n8n-bigquery` |
| 2026-10-23 21:45 UTC | Primeira renovação depois de ~23h sem nenhuma |
| 2026-10-25 15:00 UTC | Única renovação perdida no fim de semana (motivo desconhecido) |
