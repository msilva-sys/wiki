---
type: decision
status: stable
date: 2026-11-23
updated: 2026-11-26
aliases: [ADR 0049, ADR 0050, ADR 0048, sair da Vercel, API no Cloud Run, PRO-1617, PRO-1618]
tags: [agent-flow, liveagents, cloud-run, gcp, vercel, slack, infra, deploy]
---

# 2026-11-23 A API e o painel do LiveAgents vão para o Cloud Run

> [!warning] Correção (2026-11-26): o adiamento não durou, e a migração foi feita no mesmo dia
> Esta página ficou `deferred` em 2026-11-23 às 10:23 (BRT), quando msilva adiou a
> saída da Vercel. O wiki não registrou o que veio depois. Cerca de três horas mais
> tarde a migração foi retomada e concluída no mesmo dia, conferido no
> `livemode-fluxo-agentico` (`main` em `7807a0c`) e no Linear em 2026-11-26:
>
> - **ADR 0049** ("A API e o painel vão para o Cloud Run; a Vercel sai"), aceita às
>   13:30 (commit `7baede1`). É esta mesma decisão: a ADR 0048 da branch
>   `claude/cloudflare-migration-610wja` virou 0049 no `main`, porque o número 0048
>   ficou com a ADR do veredito do LiveStry.
> - **PRO-1618** concluída às 14:21 (PR #532): a API serve o painel, a rota do Slack
>   responde antes de processar onde a instância fica de pé (`work_after_response`), e
>   o CI publica no Cloud Run depois da migration (`.github/workflows/migrate.yml`).
> - **PRO-1619** concluída às 18:29 (PRs #538, #539 e #545): o domínio
>   `liveagents.livemode.space` chega à API por um projeto **Pages**. Ver a seção
>   [[#Domínio próprio pelo Pages (ADR 0050)]].
> - **PRO-1617** (épico: "... e a Vercel é desligada") em `Done` às 16:42.
>
> msilva, em 2026-11-26: *"a api já está na gcp e cloudflare"*, ou seja, a API no
> Cloud Run e o domínio pelo Pages da Cloudflare. O motivo da retomada não está
> registrado (unverified: o chat da decisão não foi relido). O `main` ainda tem o
> `vercel.json` e a flag `work_after_response` com padrão `False`, que o comentário em
> `settings.py` diz que "sai junto com a Vercel". Isso parece sobra da limpeza, não
> sinal de que a Vercel segue no ar (unverified: a conta da Vercel não foi consultada).

O painel, a API, o chat, o MCP e os webhooks do [[Agent Flow|LiveAgents]] saem da
Vercel e vão para o **Cloud Run**. A rotina continua na Cloudflare, sem mudança. Não há
Worker da Cloudflare na frente da API nem do Slack.

Decidido por msilva em chat (Claude Code), em 2026-11-23, no mesmo dia e em cima de
[[2026-11-23 A API e o painel do LiveAgents vão para a Cloudflare]], que esta página
substitui. Registrado na **ADR 0048** ("A API e o painel vão para o Cloud Run; a Vercel
sai"), reescrita no lugar porque ainda era só "Proposta". Ela está na branch
`claude/cloudflare-migration-610wja` do `livemode-fluxo-agentico` (commit `4fac72d`),
sem merge. *(Correção 2026-11-26: a versão que entrou no `main` é a ADR 0049, aceita
em 2026-11-23; ver o aviso no topo.)* Rastreado na
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

## Domínio próprio pelo Pages (ADR 0050)

Aceita em 2026-11-23 (PRO-1619), substitui em parte esta decisão: ela recusava qualquer
peça da Cloudflare na frente da API. O DNS de `livemode.space` é do TI, numa conta da
Cloudflare que não é a nossa, e o TI só aponta um nome desses para um projeto Pages. O
Cloud Run em `southamerica-east1` não tem ligação direta de domínio próprio, e o
balanceador de carga do Google tem custo fixo maior que o do serviço.

- O projeto Pages `livemode-liveagents` (conta "LiveMode - Projetos", código em
  `apps/pages`) recebe o domínio e repassa cada pedido ao Cloud Run, sem mudar caminho,
  método nem corpo. É o mesmo arranjo do LiveStry e do LivePanel.
- O destino é a variável `ORIGIN`. Trocar de destino é mudar a variável e publicar o
  Pages, sem depender do TI.
- O nome público vai em `X-Forwarded-Host`, e a API conhece o próprio endereço por
  configuração (`public_url`).
- Consequência: cada pedido passa pela Cloudflare do TI, pelo Pages e pelo Cloud Run. Se
  o Pages cair, o domínio para com a API saudável. O endereço `run.app` continua
  respondendo direto.
- Recusados: balanceador do Google, regra de origem na Cloudflare do TI, Firebase
  Hosting (corta pedidos em 60 s) e mudar de região.

Fonte: `docs/decisions/0050-dominio-proprio-por-um-pages.md` no `livemode-fluxo-agentico`.

## Em aberto

- ~~Qual projeto do Google Cloud usar e como o CI publica nele.~~ **Resolvido
  2026-11-23 (PRO-1618)**: projeto `livemode-liveagents`, região `southamerica-east1`,
  serviço `liveagents-api`. O job do `migrate.yml` publica depois da migration, com
  identidade federada do GitHub (sem chave guardada) e `--no-cpu-throttling` (a
  cobrança por instância do item 3).
- A medir: o tempo de partida da imagem no Cloud Run (unverified: o spike de
  2026-11-22 mediu 10 a 11 s num Cloudflare Container e 1,2 a 2,4 s para o app subir
  localmente), se a primeira entrega do Slack é processada quando o Slack desiste no
  meio, e o custo real.
- O que substitui a prévia por PR da Vercel (a ADR 0049 deixa em aberto).
- Os prazos do código que espelhavam o teto de 800 s da Vercel ficam a rever (ADR 0049).

## Relacionado

[[Agent Flow]], [[Plano de migração da API do Fluxo Agêntico para a Cloudflare]],
[[2026-08-17 A5 receiver runs on Cloud Run]] (outro caso de Cloud Run escalando a
zero no histórico do wiki).
