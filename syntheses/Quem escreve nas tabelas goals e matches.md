---
type: synthesis
status: superseded
updated: 2026-10-19
aliases: [produtor do golzinho, quem alimenta ourgoals]
tags: [opta, golzinho, supabase, investigação]
---

# Quem escreve nas tabelas goals e matches

> [!success] Respondida 2026-10-19 — é um fluxo n8n
> **`[GOLZINHO] Upload de Gols da Opta`**, `active: true`, 18 nós, localizado por
> msilva na instância do n8n. Schedule Trigger de 30s, credencial da Opta lida do
> BigQuery, `GET` em `api.performfeeds.com/soccerdata/match` com `live=yes`, e
> upsert em `matches`/`goals` via REST do Supabase mais duas Edge Functions.
> O desenho completo está em [[Golzinho]]; esta página fica como registro da
> busca. A hipótese abaixo estava certa.
>
> Dois achados que a pergunta original não previa: o fluxo chama a Opta **direto**,
> fora do proxy, então seu consumo não aparece em nenhum relatório de uso do
> contrato; e o export do fluxo carrega uma chave `service_role` do Supabase em
> texto claro.

Aberta 2026-10-16, durante a leitura dos três repos da [[Opta]]. Pergunta: qual
processo pergunta à Opta de forma contínua e grava os gols e partidas no Supabase
que o [[Golzinho]] e a [[Opta API]] consomem?

## Por que importa

É o único ponto do caminho sem dono conhecido, sem repositório e sem
monitoramento — e ele alimenta transmissão ao vivo. **Quem o construiu já saiu da
empresa e não está acessível** (confirmado por msilva, 2026-10-16, ver
[[Pessanha]]), então não existe atalho humano: o que não for reconstruído a partir
de logs e infraestrutura está perdido. Se parar:

- `/v1/ourGoals` e `/v1/ourMatches` continuam respondendo `200`, com o último dado
  que entrou. Nenhum cliente recebe erro.
- O WebSocket fica mudo, e silêncio é indistinguível de "nenhum gol ainda".
- O Golzinho para de postar e o clipper para de cortar, sem alerta.

Também bloqueia duas perguntas de governança: quem paga a conta dessas chamadas no
contrato da Opta (elas não aparecem identificadas no log `opta-proxy`), e quem é
acionado quando quebra.

## O que já foi descartado

Busca feita em 2026-10-16, sem achar o produtor:

- **Os três repos da Opta** (`opta-api`, `opta-mcp`, `livemode-opta-docs`),
  incluindo todo o histórico git. O `opta-api` só lê — nenhum `insert`, `update`
  ou job.
- **Os três consumidores** (`goal-webhook`, `goal-clipper`, `live-content`): todos
  leem ou reagem; nenhum escreve em `goals`/`matches`.
- **Os ~60 repos de `tech-livemode` e `livemode-org`** — nomes de todos, árvores
  dos candidatos plausíveis. Não existe repo de ingestão.
- **A wiki, o Slack e o Linear.** O Slack confirma o produto e o delay de ≤30s,
  mas nunca descreve o mecanismo.

## Hipótese

**É um fluxo n8n.** A favor: o projeto GCP que hospeda os serviços se chama
`n8n-automation-hub-448718`; a casa usa n8n para automação; e fluxo n8n não vive em
repositório, o que explicaria ele não aparecer em lugar nenhum.

Contra-indício considerado e **descartado em 2026-10-16**: numa queda do n8n em
2025-11-10, Peçanha estranhou que *"o Golzinho seguiu no ar no fim de semana"*, e
isso chegou a ser registrado aqui como evidência contra a hipótese. Não sustenta o
peso. A leitura do `#golzinho-alertas` confirma a parte factual — no domingo
2025-11-09 houve dezenas de alertas reais, com arte e legenda, incluindo um gol
anulado às 18:14, o que prova que o produtor estava inserindo **e atualizando**
linhas. Mas a premissa nunca foi verificada: ele próprio chamou de "hipótese" que o
n8n tivesse ficado fora o fim de semana todo, inferida de outro sintoma (o Artista
da LFU). Se o n8n caiu só na segunda, o domingo não prova nada.

**Confirmado em 2026-10-19**: era n8n mesmo. O episódio de 2025-11-10 continua sem
explicação — provavelmente o n8n não ficou fora o fim de semana inteiro.

## O que fecharia

1. **Logs no BigQuery.** `data-analytics-422617.logs.opta_proxy_*`, agrupando por
   `jsonPayload.apikey`: a chave que chamar `match`/`matchevent` em cadência de
   jogo é o produtor. Só funciona se ele passar pela [[Opta API]] — se usar a
   credencial direto, não aparece, e isso por si só já é uma resposta.
2. **Cloud Run, Cloud Scheduler e n8n** no `n8n-automation-hub-448718`. Exige
   `gcloud auth login` (o token de msilva estava expirado em 2026-10-16).
3. **Credenciais do Supabase.** O projeto que hospeda `goals`/`matches` guarda,
   do lado dele, quem escreve: chaves de serviço emitidas, logs de API e os
   Database Webhooks configurados. Quem tem acesso ao painel resolve em minutos o
   que a arqueologia de repo não resolveu.

O caminho humano **não existe**: Peçanha construiu as quatro peças e saiu sem
repassar isto a ninguém — não aparece na transição de Luís, que também está de
saída. Ver [[Pessanha]] e [[2026-10-07 Divisão de projetos na transição de Luís]].
