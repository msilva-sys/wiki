---
type: decision
status: stable
date: 2026-11-23
updated: 2026-11-23
aliases: [ADR 0048, sair da Vercel, API no Cloud Run, PRO-1617, PRO-1618]
tags: [agent-flow, liveagents, cloud-run, gcp, vercel, slack, infra, deploy]
---

# 2026-11-23 A API e o painel do LiveAgents vão para o Cloud Run

O painel, a API, o chat, o MCP e os webhooks do [[Agent Flow|LiveAgents]] saem da
Vercel e vão para o **Cloud Run**. A rotina continua na Cloudflare, sem mudança. Não há
Worker da Cloudflare na frente da API nem do Slack.

Decidido por msilva em chat (Claude Code), em 2026-11-23, no mesmo dia e em cima de
[[2026-11-23 A API e o painel do LiveAgents vão para a Cloudflare]], que esta página
substitui. Registrado na **ADR 0048** ("A API e o painel vão para o Cloud Run; a Vercel
sai"), reescrita no lugar porque ainda era só "Proposta". Ela está na branch
`claude/cloudflare-migration-610wja` do `livemode-fluxo-agentico` (commit `4fac72d`),
sem merge. Rastreado na
[PRO-1617](https://linear.app/projetos-livemode/issue/PRO-1617/o-painel-a-api-e-o-chat-do-liveagents-passam-a-rodar-no-cloud-run-e-a)
(épico) e na
[PRO-1618](https://linear.app/projetos-livemode/issue/PRO-1618/o-painel-e-a-api-do-liveagents-sobem-no-cloud-run-ao-lado-da-vercel)
(primeira etapa), as duas reescritas no mesmo dia.

## Como se chegou aqui

1. msilva pediu para seguir com a PRO-1618 (Cloudflare Container atrás do
   `liveagents-worker`). Ao perguntar se dava para chamar a API sem passar pelo
   Worker, soube que **não**: na Cloudflare o container não tem endereço próprio, e
   todo pedido (painel, API, MCP, webhooks) passa por um Worker.
2. msilva: *"a ideia é o worker resolver apenas o problema do timeout do webhook"*.
   Para isso, a API precisa de endereço próprio, ou seja, sair do container. Escolhido
   em chat: **Cloud Run**, com o painel servido pela própria API, e um Worker que só
   guarda o corpo do webhook, responde 200 e repassa.
3. A pergunta seguinte de msilva (*"o gcp tem o problema de timeout tb?"*) desfez a
   necessidade do Worker. O prazo de 3 s é do Slack, não da plataforma, mas no Cloud
   Run a rota pode responder antes de processar: a instância não congela depois da
   resposta, como congela na Vercel (é o motivo registrado na docstring da rota
   `/intake/slack/events`). A única exceção seria a partida a frio.
4. Instância sempre ligada para evitar a partida a frio: recusada por msilva pelo
   **custo**. Fechado: escala a zero, e na partida a frio o Slack reentrega uma vez e a
   trava por `event_id` segura.

## O que ficou decidido (ADR 0048)

1. A API roda no **Cloud Run**, com a imagem e o código da rotina. Muda só o comando de
   partida: `uvicorn` no lugar da rodada.
2. **A própria API serve o painel** como arquivos estáticos, no mesmo endereço (a
   sessão depende disso).
3. **Escala a zero, com cobrança por instância.** Na cobrança por pedido, a CPU é
   cortada depois da resposta, e o processamento em segundo plano não termina.
4. **A rota do Slack confere a assinatura, responde 200 e processa a menção em
   segundo plano.**
5. Se o serviço estiver parado, a primeira entrega pode passar de 3 s. A instância sobe
   e processa; o Slack reentrega, e a trava por `event_id` impede a issue duplicada.
6. **A rotina não muda**: continua no Worker e no container `fluxo-agentico-rotina`
   (ADR 0028).
7. Vercel e Cloud Run rodam em paralelo no mesmo banco até a virada do domínio
   (`liveagents.livemode.space`). Voltar é apontar o domínio de volta.
8. O banco não muda.

## Alternativas recusadas

- **Cloudflare Container** (o desenho de
  [[2026-11-23 A API e o painel do LiveAgents vão para a Cloudflare|horas antes]]): um
  Worker fica na frente de todo pedido.
- **Worker na frente só do webhook do Slack**: mais uma peça, com 30 s para terminar o
  repasse, só para tirar a reentrega do log do Slack na partida a frio.
- **Instância sempre ligada**: custo fixo para evitar uma reentrega que a trava já
  absorve.
- **Python Workers**: reescrita para assíncrono e dependências em WebAssembly.
- **Painel e API em origens diferentes**: quebra a sessão.
- **Ficar na Vercel**: a rota continua sem poder responder antes de processar.

## Consequências

- Com o serviço de pé, o Slack para de reentregar. Com ele parado, reentrega uma vez,
  como hoje faz em toda menção.
- O primeiro acesso ao painel, ao chat ou ao MCP depois de um tempo parado espera a
  partida.
- **Uma menção em processamento quando a instância é desligada ou trocada por um
  deploy se perde.** Fica sem issue, e o Slack não reentrega.
- Acaba a prévia por PR da Vercel, e não há nada definido para substituí-la.
- Continuam duas plataformas, Cloudflare (rotina) e Google Cloud (API), mas a Vercel
  sai.
- Acaba o teto de 800 s por pedido.

## Em aberto

- Qual projeto do Google Cloud usar e como o CI publica nele.
- A medir: o tempo de partida da imagem no Cloud Run (unverified: o spike de
  2026-11-22 mediu 10 a 11 s num Cloudflare Container e 1,2 a 2,4 s para o app subir
  localmente), se a primeira entrega do Slack é processada quando o Slack desiste no
  meio, e o custo real.
- O `apps/edge/` da branch `msilva/pro-1618-…` ficou sem função, e o **PR #521** do
  `livemode-fluxo-agentico`, ainda ligado à PRO-1618, propõe esse desenho antigo.

## Relacionado

[[Agent Flow]], [[Plano de migração da API do Fluxo Agêntico para a Cloudflare]],
[[2026-08-17 A5 receiver runs on Cloud Run]] (outro caso de Cloud Run escalando a
zero no histórico do wiki).
