---
type: synthesis
status: draft
updated: 2026-11-12
aliases: [migração da API pra Cloudflare, API na Cloudflare, sair da Vercel, apps/edge]
tags: [agent-flow, cloudflare, vercel, infra, deploy]
---

# Plano de migração da API do Fluxo Agêntico para a Cloudflare

Plano montado em chat com msilva em 2026-11-12, numa sessão do Claude Code no repo
`livemode-fluxo-agentico` (branch `msilva/pro-1523-…`). **Só registro**: não há ADR,
issue no Linear nem spike. msilva quer migrar a API também; a decisão ainda não foi
tomada formalmente.

## Ponto de partida (lido no código em 2026-11-12)

- Hoje a Cloudflare só roda a **rotina** dos agentes: o Worker `fluxo-agentico-rotina`
  (`apps/worker`) agenda as rodadas e sobe um Cloudflare Container por rodada, com
  `python -m cli cron <chave>` (ADR 0028, aceita em 2026-11-10). Motivo: o teto de
  800 s da função da Vercel não comportava a rodada, por causa do limite de chamadas
  ao modelo por minuto da conta.
- API, dashboard, chat, webhooks e MCP continuam na Vercel (`vercel.json` com os
  serviços `frontend` = `apps/web` e `backend` = `apps/api/gateway.py`).
- O repo tem `docs/future_migrations/cloudflare.md`, um estudo de migração completa
  sem data. Ele recomenda o **caminho B** (Containers, quase sem mudar código) em vez
  do caminho A (Python Workers nativo, que exige reescrever o I/O em `async` e
  depende de cada biblioteca rodar em WebAssembly).
- A imagem do container da rotina (`apps/worker/Dockerfile`) **já contém o código da
  API**: `uv sync --package fluxo-agentico-api`. Ela só não sobe servidor HTTP.

## O plano

### Estrutura: um Worker separado para a API

Decidido em chat que a API fica num Worker **próprio**, não no da rotina:

```
apps/
├── worker/   fluxo-agentico-rotina   agenda + container por rodada (sem mudança)
└── edge/     fluxo-agentico          domínio, frontend (static assets) + container da API
```

`apps/edge` é só um nome sugerido. Motivos para separar:
- **Funções diferentes**: a API é pública, com domínio e tráfego de usuários, Slack e
  Linear. A rotina é privada e só acorda pela agenda. Juntas, a rota manual
  `POST /run/<chave>` da rotina ficaria exposta no domínio público.
- **Rollback independente** de cada um.
- **Logs e métricas separados.**
- **Containers com comportamento diferente**: a rotina usa um container por rodada
  (`scheduling_policy: durable_object`, sobe, roda e para). A API precisa de
  containers sem estado que atendem requests e dividem a carga.
- Uma mudança no roteamento da API não consegue quebrar a agenda.

Custos aceitos:
- Mais um job no CI (`deploy-api` no `migrate.yml`, também depois da migration).
- A mesma imagem é construída duas vezes. Se incomodar, o CI publica a imagem uma vez
  e os dois Workers usam essa imagem.
- Segredos em dois Workers: `scripts/worker_secrets.py` ganha uma opção para escolher
  o Worker de destino. A API tem alguns segredos próprios (login, validação de
  webhook).
- As duas versões ficam diferentes por alguns minutos entre os deploys. A regra de
  migration aditiva (expand/contract) do `migrate.yml` já cobre esse caso.

### Passos

1. **ADR + issue no Linear antes de começar.** A ADR nova substitui o trecho da
   ADR 0028 que mantém a API na Vercel e atualiza `docs/agent-framework.md`
   (premissa "API… em execução serverless na Vercel" e o não-objetivo "mudar Vercel").
2. **Spike de uma tarde**: API num container num endereço `*.workers.dev`, com quatro
   testes de ponta a ponta: rota autenticada, chat com streaming, uma chamada de
   agente e o MCP.
3. **Adaptar o que é da Vercel** (pontos encontrados no código):
   - `Dockerfile`: o container da API sobe com `uvicorn gateway:app`. O da rotina
     continua recebendo o comando do Worker.
   - O frontend vira static assets em modo SPA, substituindo o rewrite
     `/(.*) → /index.html` do `vercel.json`. O Worker manda `/api/*`, `/mcp` e
     `/.well-known/*` para o container.
   - `core/infra/settings.py`: `mcp_resource_url` aponta fixo para
     `livemode-fluxo-agentico.vercel.app/mcp`. `vercel_project_production_url` e
     `vercel_git_commit_sha` vêm de variáveis injetadas pela Vercel. O SHA é o
     `release` do Langfuse (`core/infra/langfuse_tracer.py`,
     `core/infra/evaluation.py`), então o deploy passa a enviar esse dado.
   - `core/infra/ledger.py` e `apps/web/src/api/portfolio-analyst.ts` assumem o
     `maxDuration` de 800 s da Vercel. Esses valores precisam ser revistos.
4. **Rodar em paralelo e trocar os endereços externos.** É a parte delicada:
   domínio, cadastro do app no [[LiveAuth]] (login e o `resource` do MCP) e URLs de
   webhook no Slack e no Linear.
5. **Desligar a Vercel**: remover o `vercel.json` e o projeto.

### O que melhora

- O deploy da API passa a esperar a migration. Hoje o `migrate.yml` documenta a
  corrida entre o deploy da Vercel e a migration, sem garantia de ordem.
- Uma plataforma só e um lugar só para segredos. A Vercel não devolve variável
  sensível, e isso já obrigou a criar o `worker_secrets.py`.
- Acaba o teto de 800 s por request.

## Riscos para medir no spike

- **Partida a frio**: o container desliga quando ocioso, e subir Python com
  LangGraph leva alguns segundos (unverified: tempo real não medido). O Slack exige
  resposta a um evento em até 3 s, então a menção ao bot (Intake) pode falhar. A
  saída é manter um container sempre ligado, com custo fixo.
- **Conexões com o Postgres**: `core/infra/db.py` limita o pool a `max_size=2` por
  instância, por causa de um incidente no Fluid Compute da Vercel. Num container de
  vida longa dá para subir, mas é bom medir antes.
- **Streaming do chat** passando por Worker → container: primeiro teste do spike.
- O estudo `future_migrations/cloudflare.md` usa a documentação da Cloudflare de
  2026-10-08 e pede para revalidar antes de executar.

## Decisões em aberto (de msilva)

- **Domínio novo**: por exemplo, um `.livemode.space`.
- **Partida a frio**: container sempre ligado (custo fixo) ou desligando quando
  ocioso (mais barato, com risco no Slack).

Relacionado: [[Agent Flow]], [[O que falta para os agentes passarem no teste de portabilidade]].
