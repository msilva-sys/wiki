---
type: system
status: active
updated: 2026-10-16
aliases: [opta-api, opta api, proxy da opta, api interna da opta]
tags: [opta, cloud-run, fastapi, supabase, bigquery]
---

# Opta API

Porta única de acesso aos dados da [[Opta]] para sistemas, planilhas, dashboards
e automações. Repo `tech-livemode/opta-api`, Python 3.12 + FastAPI, no Cloud Run.
No ar desde 2026-04-14. Domínio `opta.livemode.space`.

> [!note] Verificado direto no código em 2026-10-16
> Tudo nesta página veio da leitura do repo (último commit `4c12238`) e da
> documentação interna em `livemode-opta-docs`, salvo onde marcado. Onde a doc e o
> código divergem, o código prevalece e a divergência está registrada.

## São dois produtos no mesmo serviço

Essa é a chave pra entender o serviço, e não está dita em lugar nenhum da doc:

| Rota | O que faz | Fala com a Opta? |
| --- | --- | --- |
| `/v1/{path}` | Proxy transparente: injeta a credencial do contrato e repassa | Sim, a cada chamada |
| `/v1/ourMatches`, `/v1/ourGoals` | Lê o Supabase do [[Golzinho]] | Não |
| `/v1/ws/goals`, `/v1/ws/matches` | Relay do Supabase Realtime para o cliente | Não |

O proxy é tempo de chamada; as rotas `our*` e os WebSockets são leitura de um
banco que **outro produto alimenta** e que já existia sete meses antes deste
serviço. O serviço nunca escreve nesse banco — não há `insert`, `update` nem job
em lugar nenhum do repo. Ver [[Golzinho]] e
[[Quem escreve nas tabelas goals e matches]].

Confirmado pelo próprio anúncio interno (Slack `#general`, 2026-05-11, Peçanha):
*"O serviço vira a porta de entrada única pra tudo da Opta, mais alguns recursos
nossos próprios (gols e partidas do nosso banco, inclusive com WebSocket pra
tempo real)."*

## Fluxo de uma chamada ao proxy

1. `verify_api_key` lê o header `X-API-Key`. Sem header → `401`; chave fora da
   lista → `403`.
2. A lista vem da variável `ALLOWED_API_KEYS`, **lida uma vez por instância** e
   guardada em memória (`main.py:40`). Emitir ou revogar chave exige revisão nova
   do Cloud Run.
3. Busca a credencial da Opta no BigQuery — **a cada chamada, sem cache**
   (`lib/secret.py`). O cache de 5 minutos existia e foi removido no commit
   `7164c9f` (2026-04-16). Rastreado em `PRO-693`.
4. Grava o log de auditoria sem esperar confirmação, **antes** da chamada à Opta —
   por isso a entrada não tem status nem latência (`PRO-695`).
5. Repassa método, query e corpo pra `OPTA_BASE_URL`, trocando o `Authorization`
   pelo Bearer do contrato e removendo os headers hop-by-hop. Timeout de 30s.
6. Devolve a resposta da Opta como veio — mesmo status, mesmos headers. Um `400`
   ou `403` da Opta chega ao cliente intacto, o que mantém a doc da Opta válida
   pra quem integra.

## WebSocket

`/v1/ws/{table}` é com o **cliente**, não com a Opta — a Opta não tem push, toda
chamada a ela é `GET` HTTP. O serviço valida a chave no upgrade, assina
`on_postgres_changes` no Supabase e repassa cada evento como JSON
(`main.py:145`). A chave vai no header, não na URL: por isso não funciona direto
do navegador, o que de quebra evita expor a chave no frontend.

É o único estado do serviço — o resto é stateless.

## Variáveis de ambiente

Valores ficam no Cloud Run do serviço; nenhum entra aqui.

| Variável | Para quê |
| --- | --- |
| `OPTA_BASE_URL` | Base da Opta (`api.performfeeds.com`) |
| `ALLOWED_API_KEYS` | Lista de chaves de cliente aceitas, separadas por vírgula |
| `GCP_SERVICE_ACCOUNT_JSON` | Service account pro BigQuery e Cloud Logging |
| `SUPABASE_URL` | Projeto Supabase das tabelas `goals`/`matches` |
| `SUPABASE_SERVICE_ROLE_KEY` | Service role — ignora o RLS, nunca sai do servidor |

## Auditoria

Grava em `logName="projects/data-analytics-422617/logs/opta-proxy"`, o **mesmo
log do [[Opta MCP]]** — o que separa os dois é o campo `apiKey` (chave real do
cliente no proxy, `"mcp-call"` no MCP). Um sink copia pro BigQuery em
`data-analytics-422617.logs.opta_proxy_*`, uma tabela por dia, e é a base dos
relatórios de uso do contrato.

Não entram nesse log: chamadas recusadas por chave inválida (ficam só nos request
logs do Cloud Run, 30 dias de retenção) e o status/latência das chamadas do proxy.

## Riscos

Consolidados em [[Opta]] § Riscos. Os específicos deste serviço:

- **28 chaves sem registro de dono.** A única fonte é a variável no Cloud Run, que
  guarda UUIDs soltos. Revogar qualquer uma é adivinhação. `PRO-694`.
- **As chaves aparecem em claro** no log e no BigQuery. Acesso ao dataset `logs`
  equivale a acesso às chaves.
- **Duas regiões em revisões divergentes** — `southamerica-east1` (`opta-api-00008`)
  e `us-east4` (`opta-api-00006`), serviços independentes em 2026-10-05. Uma
  revogação aplicada numa não vale na outra. Não está confirmado qual atende o
  domínio (unverified: levantado na doc interna, não checado no Cloud Run).
- **A credencial do contrato mora no BigQuery**, não no Secret Manager — mesma
  dívida que o [[Airtable Proxy]] já resolveu.
- **Falha silenciosa nas rotas `our*`**: se o produtor do [[Golzinho]] parar, elas
  seguem respondendo `200` com dado velho e o WebSocket fica mudo. Não há alerta.

## Histórico

| Data | O quê |
| --- | --- |
| 2026-04-14 | Reescrito de Next.js/Vercel para FastAPI/Cloud Run; nascem `/ourGoals`, `/ourMatches` e os WebSockets (`d7faed0`, por Peçanha) |
| 2026-04-16 | Prefixo `/v1`, remoção do cache da credencial, `/openapi.json` desligado (`7164c9f`) |
| 2026-05-11 | Anunciado no `#general` como porta única da Opta, com GPT customizado de docs |
| 2026-06-08 | Filtro `tournament_calendar` em `/ourMatches` (`4c12238`) |
| 2026-09-22 | Guia de integração publicado no repo |
