---
type: system
status: active
updated: 2026-10-19
aliases: [golzinho, live content, goal-webhook, pipeline de gols]
tags: [opta, cazetv, supabase, realtime, live-content, n8n]
---

# Golzinho

Robô que acompanha todos os jogos transmitidos e, a cada gol, posta no Slack
(`#golzinho-alertas`, `C09HK7TLWBW`) o alerta já com a arte do jogador puxada do
Drive e a legenda gerada por IA. É a cara do projeto **Live Content**, idealizado
por Ana Zacour com Peçanha e João Kropf, depois com Roberta Rodrigues e Isabella
Lima (anúncio no `#general`, 2025-10-03).

O alvo do produto não é "ter dado de futebol" — é **chegar antes do replay**:

> Com a Opta, temos um delay de no máximo 30 segundos. Pensando em uma
> transmissão, o timing é basicamente ter tudo pronto quando o replay do evento
> está passando.
> — Peçanha, `#general`, 2025-10-03

Importa pra [[Opta]] porque o banco que ele alimenta é o que a [[Opta API]] expõe
como "dados próprios" em `/v1/ourGoals`, `/v1/ourMatches` e nos WebSockets — sete
meses depois de o banco já existir.

## A arquitetura

```
            GET a cada 30s, direto, sem passar pelo proxy
Opta ◄──────────────────────────────── n8n: [GOLZINHO] Upload de Gols da Opta
                                                │ upsert
                                                ▼
                                   Supabase: tabelas goals e matches
                                                │
          ┌──────────────┬──────────────────────┼──────────────┐
          ▼              ▼                      ▼              ▼
    goal-webhook    goal-clipper          live-content     Opta API
   (o robô em si)  (clipe de vídeo)        (telas)       (/ourGoals, /ws)
```

A tabela é o ponto de encontro: o gol entra uma vez e três sistemas reagem juntos,
cada um fazendo uma coisa. **Não é cache** — se fosse, espelharia o schema da Opta,
teria TTL e não teria canal de push. A persistência existe porque a Opta é pull
(nunca avisa nada) e o produto precisa de push.

## Quem escreve: o fluxo n8n

Identificado em 2026-10-19, quando msilva localizou o fluxo no n8n e exportou o
JSON. Nome: **`[GOLZINHO] Upload de Gols da Opta`**, `active: true`, 18 nós.
Resolve [[Quem escreve nas tabelas goals e matches]].

| Nó | O que faz |
| --- | --- |
| `Schedule Trigger` | Campo `seconds` sem intervalo explícito — o default do n8n é 30s, que bate com o *"delay de no máximo 30 segundos"* do anúncio |
| `Get bearer token` | Nó BigQuery com a **mesma query** dos dois serviços: `SELECT key FROM bronze_layer.opta_chaves_api ORDER BY insert_ts DESC LIMIT 1`, projeto `data-analytics-422617`, credencial OAuth2 própria |
| `Get Match` | `GET api.performfeeds.com/soccerdata/match/{outlet}` com `live=yes`, `lineups=yes`, `_pgSz=100` e `mt.mDt=[ontem 00:00Z TO amanhã 00:00Z]` |
| `Code` | Extrai `liveData.goal[]`, resolve o nome do time e inverte o contestant quando o tipo é `OG` |
| `Upsert Match` / `Upsert Goal HTTP` | `POST /rest/v1/{matches,goals}?on_conflict=id` no Supabase, com `Prefer: resolution=merge-duplicates`; há também uma Edge Function `upsert-match` |
| `Mark goals as disallowed` | Edge Function que recebe `match_id` + `allowed_event_ids` e marca como anulado todo gol que sumiu do feed |

O último nó é a origem do `confirmed: disallowed` e do `❌ Gol anulado` no Slack:
a Opta não avisa que anulou um gol, ela apenas para de listá-lo, e o fluxo compara
e deduz.

`settings.saveDataSuccessExecution: "all"` — diferente do default da instância,
este fluxo **salva execuções bem-sucedidas**. Existe histórico pra auditar e pra
montar alerta de parada.

> [!danger] O export do fluxo carrega segredo em texto claro
> O JSON exportado traz, embutidas nos headers dos nós HTTP, uma chave
> **`service_role`** do Supabase (duas ocorrências) e uma `anon`. `service_role`
> ignora o RLS: é escrita total no banco que alimenta transmissão ao vivo.
> Qualquer export do fluxo vaza de novo enquanto a chave estiver inline em vez
> de numa credencial do n8n. Nenhum valor foi registrado nesta wiki. Ação
> pendente: migrar pra credencial Header Auth e rotacionar a chave — a rotação
> derruba este fluxo **e** a [[Opta API]] (`SUPABASE_SERVICE_ROLE_KEY`) ao mesmo
> tempo, então precisa ser coordenada e fora de horário de jogo.

> [!warning] Bug candidato no placar ao vivo (não verificado)
> O `Upsert Match` testa `liveData.matchDetails.scores.total.home !== undefined`
> mas grava `scores.ft.home` — idem no away. Em partida em andamento `ft` (full
> time) provavelmente não existe, enquanto `total` existe. Se confirmado,
> `home_goals`/`away_goals` ficam nulos ou errados em `/ourMatches` **durante o
> jogo**, que é quando importam. Confirmável por uma execução salva de jogo ao
> vivo.

## Volume medido (2026-10-19)

Projeto Supabase identificado: **`live-content`**, organização **LiveMode Tech**,
plano Pro, ref `cegrhqontjltmfqfrkqr`, branch `main` marcada como `PRODUCTION`.

Amostra de 100 registros dos logs de API, janela de 27s (13:59:39 → 14:00:06):

| Origem | Rota | Chamadas |
| --- | --- | --- |
| `n8n` | `POST /rest/v1/goals` | 64 |
| `n8n` | `POST /rest/v1/matches` | 35 |

**Todo o tráfego de escrita é n8n** — nenhuma outra origem na janela. Confirma pelo
lado do banco que o fluxo é o único produtor. Duas rajadas, de 49 e 50 escritas,
com início separado por 24s, batendo com o Schedule Trigger.

**O fluxo não detecta mudança: ele reescreve a janela inteira a cada tick.** São
~50 escritas por tick (~32 em `goals`, ~18 em `matches`), tenha mudado alguma coisa
ou não — da ordem de 150 mil escritas por dia no Postgres de produção para
refletir algumas dezenas de eventos reais. O log traz o checkpoint correspondente
(`wrote 479 buffers […] write=47.876 s`).

**Efeito em cascata.** Existe um Database Webhook, `CLOUD_RUN_Webhook`, **só na
tabela `goals`** (eventos `INSERT` e `UPDATE`), apontando para o `goal-webhook` no
Cloud Run. Como todo upsert com `merge-duplicates` conta como `UPDATE`, cada gol
reescrito invoca o serviço: ~64 a 80 invocações por minuto, da ordem de 3 milhões
por mês, quase todas descartadas. `matches` não tem webhook — mudança de placar não
dispara nada.

O que impede isso de virar flood no `#golzinho-alertas` é uma única condição no
`goal-webhook`, que só publica se `scorer` ou `confirmed` mudaram. A proteção
contra flood não está no banco nem na configuração do webhook: está numa
comparação de campo dentro do handler.

## Quem lê (verificado nos repos, 2026-10-16)

| Repo | Papel | Como é acionado |
| --- | --- | --- |
| `tech-livemode/goal-webhook` | O Golzinho propriamente dito. Legenda com Gemini, arte do Drive, post no Slack | Database Webhook do Supabase: `POST` a cada `INSERT`/`UPDATE` em `goals` |
| `tech-livemode/goal-clipper` | OCR do placar na transmissão + FFmpeg; corta clipe de 14s (9s antes, 5s depois do gol), calibrando o atraso do broadcast | Assinatura Realtime em `goals` |
| `tech-livemode/live-content` | Next.js: telas de gols, partidas, captions e tweets | Leitura sob demanda |
| `tech-livemode/opta-api` | Expõe leitura e repassa o Realtime adiante | Assinatura Realtime |

Nenhum deles escreve em `goals`/`matches` — conferido linha a linha. O
`goal-webhook` só grava `captions`, `assets` e um gol-dummy de teste.

O `goal-webhook` só republica quando `scorer` **ou** `confirmed` muda: é o
mecanismo de corrigir no Slack um gol anulado depois do alerta.

## As tabelas

- **`matches`** — calendário, placar, estádio, flag `live`, e os horários **reais**
  de início de cada tempo (`first_half_start`, `second_half_start`), que são o que
  permite ao clipper achar o gol na gravação.
- **`goals`** — minuto e `minute_sec` (precisão de segundo), autor, tipo
  (`G`/`OG`/`PG`), placar logo após o gol, `confirmed` (`yes`/`no`/`disallowed`) e
  `additional_comments`.

Os IDs são os mesmos da Opta: `id` de partida serve como `fx` nos feeds, e
`tournament_calendar` é o `tmcl` da edição.

## Riscos

- **Chave `service_role` em texto claro no export do fluxo** — ver o callout
  acima. É o risco mais grave do conjunto da [[Opta]].
- **O consumo da Opta é invisível no contrato.** O fluxo chama
  `api.performfeeds.com` direto, com a credencial do BigQuery, sem passar pela
  [[Opta API]] — logo não entra no log `opta-proxy`. Um `GET` a cada 30s, 24/7,
  é da ordem de 86 mil chamadas/mês, e **nenhuma** aparece nos relatórios de uso.
  Toda leitura de custo do contrato feita até hoje está subestimada.
- **Amplificação de escrita e de invocação** — ver § Volume medido. ~150 mil
  escritas/dia e ~3 milhões de invocações/mês do `goal-webhook` para refletir
  dezenas de eventos reais. Corrigível no fluxo (filtrar por `lastUpdated` antes
  de escrever) ou no webhook (condicionar por coluna).
- **A proteção contra flood do Slack é um `if` no handler.** Quem mexer nessa
  condição sem saber o volume que chega transforma o `#golzinho-alertas` em
  dezenas de mensagens por minuto.
- **Produtor sem alerta.** Identificado em 2026-10-19, mas nada avisa se ele
  parar: `/ourGoals` e `/ourMatches` seguem respondendo `200` com dado velho e o
  WebSocket fica mudo — silêncio indistinguível de "nenhum gol ainda". Com
  `saveDataSuccessExecution: "all"` ligado, o histórico pra construir esse alerta
  já existe.
- **Três clientes acessam o Supabase direto**, com credencial do banco, fora do
  log de auditoria `opta-proxy`. O controle de acesso que a [[Opta API]] constrói
  não alcança esse caminho.
- **Há um arquivo `service-account.json` versionado na raiz do `goal-webhook`.**
  Só o nome foi observado na árvore do repo; o conteúdo **não** foi lido de
  propósito. Se for chave real, é credencial em histórico de git. Verificar.

## Histórico

| Data | O quê |
| --- | --- |
| 2025-09-22 | Primeiro commit do `goal-webhook` |
| 2025-09-26 | Canal `#golzinho-alertas` criado |
| 2025-10-03 | Live Content anunciado no `#general` |
| 2026-02-05 | Volta depois de uma parada (*"OOOOOOOO GOLZINHO VOLTOOOOOU"*) |
| 2026-04-14 | A [[Opta API]] passa a expor as mesmas tabelas por HTTP e WebSocket |
| 2026-04-24 | Último commit do `goal-webhook` (troca do modelo Gemini) |
| 2026-10-16 | Em operação — alertas de Libertadores, Ligue 1, Serie A e Bundesliga no canal |
| 2026-10-19 | Produtor identificado: o fluxo n8n `[GOLZINHO] Upload de Gols da Opta` |
