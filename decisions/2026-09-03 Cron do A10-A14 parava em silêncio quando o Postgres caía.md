---
type: decision
status: active
updated: 2026-09-03
date: 2026-09-03
decided_by: Matheus Silva
source: "chat with Claude, 2026-09-03"
tags: [agents, agent-flow, a10, a14, langgraph, cron, incident, reliability]
aliases: [cron falha em silêncio, ConnectionTimeout Supabase, ausência de alerta do cron]
---

# Cron do A10/A14 parava em silêncio quando o Postgres caía

## Resumo

Investigando um erro de login em produção (`auth_gate.py`), achado um problema
maior no caminho: o cron diário do A10/A14 tinha falhado de verdade nas
07:00 UTC de 2026-09-03, sem publicar nenhum digest — e sem deixar rastro
nenhum no dashboard, só nos logs brutos da Vercel. Causa: timeout de conexão
com o Postgres (Supabase) derrubando o app inteiro na inicialização.
Corrigido, testado (unitário + manual local + um teste ao vivo publicando
um alerta real no Linear) e commitado (`9587d9a`, branch `langgraph`, ainda
não commitado pro remoto).

## Origem

msilva reportou `auth_gate.py:161` ("Login inválido ou expirado") acontecendo
em loop reproduzível (5 tentativas seguidas falhando) — autorresolvido
depois, causa não confirmada (suspeita de cold-start race, não investigada a
fundo). No caminho da investigação, os logs da Vercel (`vercel logs`)
mostraram algo mais sério na mesma janela: `GET /api/cron/run-all` às
07:00:23 UTC — a hora exata do cron diário (`vercel.json`, `"0 10 * * *"`) —
falhou com:

```
psycopg.errors.ConnectionTimeout: connection timeout expired
host: aws-0-sa-east-1.pooler.supabase.com — timeout em duas tentativas de IP
Application startup failed. Exiting.
```

## Causa raiz — bem fundamentada no código, não confirmada do lado do Supabase

- **`db.py::get_connection()` não tinha `connect_timeout`** — sem isso,
  psycopg/libpq usa o timeout padrão do SO (bem mais longo que razoável); o
  log mostra duas tentativas de IP diferentes do pooler antes de desistir,
  batendo com esse padrão.
- **`init_db()` roda ~15 statements de DDL + setup do `PostgresSaver` + seed
  da SOUL numa conexão só, protegida por `pg_advisory_lock`, em TODO cold
  start** — não só na primeira vez. Cold starts concorrentes disputando o
  mesmo advisory lock abrem conexões simultâneas contra o pooler, plausível
  de esgotar o limite de clientes do plano.
- **Region mismatch**: a function roda em `iad1` (EUA), o pooler do Supabase
  em `sa-east-1` (São Paulo) — cross-region soma latência à mistura.

## Por que era silencioso

`gateway.py`'s lifespan chamava `init_db()` sem try/except — se falhasse, o
FastAPI inteiro não subia pra aquela invocação, **incluindo `/api/auth/*`**
(que nem usa banco). E `cron_history.save_run()` (que o dashboard lê pra
mostrar o histórico) também precisa do Postgres — quando o DB é o problema,
não há como nem registrar que algo deu errado. Só sobrava nos logs brutos da
Vercel.

## O que mudou (commit `9587d9a`, branch `langgraph`, local — não commitado pro remoto)

- **`db.py`**: `connect_timeout=10` em toda conexão (`get_connection()` e o
  `PostgresSaver`) — falha rápido e previsível em vez de travar até o limite
  de 300s da function.
- **`gateway.py`**: `init_db()` não derruba mais o app se falhar; guarda o
  resultado em `app.state.db_ready`/`db_error`. Rotas que não dependem de
  Postgres (`auth_gate`) continuam funcionando mesmo com o DB fora.
- **`cron.py`**: `run_all()` confere `db_ready` antes de rodar — se falso,
  publica um alerta no Linear (`projectUpdateCreate`, `health="offTrack"`,
  no projeto Fluxo Agêntico, que já posta automaticamente no Slack) e
  retorna 503, em vez de tentar rodar sobre um DB que não existe. Qualquer
  exceção inesperada durante a execução real também dispara o mesmo alerta
  antes de subir o erro — não só o caso de DB indisponível.
- **`test_cron.py`**: dois testes novos cobrindo os dois caminhos
  (`db_ready=False` → 503 + alerta sem rodar nada; exceção inesperada →
  alerta + re-raise).

## Validação

1. **Unit tests**: `test_cron.py` (5/5, incluindo os 2 novos) e
   `test_auth_gate.py` (3/3) passam.
2. **Timeout manual, isolado**: `db.get_connection()` contra um host
   inalcançável falhou em exatos **10.0s** com `ConnectionTimeout: connection
   timeout expired` — mesma classe de erro que apareceu em produção,
   confirmando que a correção ataca o problema real.
3. **App sobrevive a DB quebrado, local**: subindo o servidor local com
   `POSTGRES_URL_NON_POOLING` apontando pra um host inalcançável, o app
   completou o startup (`Application startup complete`) e `GET
   /api/auth/login` respondeu 302 normal pro Google — antes disso o app
   inteiro cairia.
4. **Teste ao vivo, ponta a ponta**: `POST /api/cron/run-all` contra o
   servidor local (DB quebrado, `CRON_SECRET` real) retornou **503** (`"db
   not ready"`) e publicou de verdade o alerta no projeto Fluxo Agêntico no
   Linear — confirmado por query direta na API do Linear, e visível no
   Slack do time (captura do próprio msilva). **Decisão explícita: não
   publicar esclarecimento de que era teste** — "o agente pode postar no
   Slack, mas não precisamos postar esclarecimento." O alerta de teste
   ficou no histórico do projeto sem contexto adicional.

## Em produção

**Mesmo dia**: commit pushed pra `origin/langgraph` e promovido a Production
na Vercel por msilva (`livemode-fluxo-agentico-n3f3ozljo`) — logs pós-deploy
sem erro. A correção vale antes do próximo disparo do cron (~07:00 UTC do
dia seguinte).

## Em aberto

- **Causa raiz não confirmada do lado do Supabase** — a hipótese (sem
  timeout + migração pesada em todo cold start + region mismatch) é bem
  fundamentada no código, mas ninguém olhou métricas/logs do pooler em si.
  Se voltar a acontecer com frequência, vale investigar direto no Supabase
  (limite de conexões do plano, região do projeto).
- **Login loop do `auth_gate.py`** — autorresolvido no mesmo dia, causa não
  confirmada (suspeita de cold-start race). Não tratado neste fix; fica pra
  observar se recorre.

## Ligação com o resto do projeto

Fecha, na prática, o item "gap de silêncio" que motivou a checagem das
pendências de [[2026-08-24 Build A10 and A14 together, PoC first]] (label
`production` no Langfuse continua pendente; limite de cron do plano Vercel
já tinha sido descartado como risco). Achado a partir de uma investigação
que começou como suporte a um erro de login pontual — o cron silencioso era
um problema maior, escondido atrás de um sintoma menor.
