---
type: decision
status: stable
date: 2026-11-23
updated: 2026-11-23
aliases: [ADR 0048, sair da Vercel, liveagents-worker, migração pra Cloudflare, PRO-1617, PRO-1618]
tags: [agent-flow, liveagents, cloudflare, vercel, infra, deploy]
---

# 2026-11-23 A API e o painel do LiveAgents vão para a Cloudflare

O painel, a API, o chat, o MCP e os webhooks do [[Agent Flow|LiveAgents]] saem da
Vercel e vão para a Cloudflare. O destino é **um Worker só, o `liveagents-worker`**,
com o painel, a API e a rotina. Depois disso, a Vercel é desligada.

Decidido por msilva. Registrado na **ADR 0048** ("A API e o painel vão para a
Cloudflare; a Vercel sai") e no épico
[PRO-1617](https://linear.app/projetos-livemode/issue/PRO-1617/o-painel-a-api-e-o-chat-do-liveagents-passam-a-rodar-na-cloudflare-e-a).
A ADR substitui o trecho da ADR 0028 que mantinha a migração completa fora do plano.

**Estado formal em 2026-11-23** (lido no repo e na conta da Cloudflare): a ADR 0048
está com status **"Proposta"**, no commit `6c2cfdb` da branch
`msilva/pro-1618-o-painel-e-a-api-do-liveagents-sobem-na`, ainda **não mergeada** em
`main`. A PRO-1617 e as filhas estão em Backlog. Nenhum `liveagents-worker` está
publicado na conta "LiveMode - Projetos": os Workers publicados são
`fluxo-agentico-rotina`, `livestry`, `liveauth` e `vitrine-digest-cron`. As decisões
abaixo já estão listadas como "Decisões já tomadas" na PRO-1617.

Substitui o rascunho [[Plano de migração da API do Fluxo Agêntico para a Cloudflare]]
(2026-11-12), que agora está `superseded`.

## O que ficou decidido

Fonte: ADR 0048, seção "Decisão", e PRO-1617.

1. **A API roda num Cloudflare Container** com o mesmo código e a mesma imagem da
   rotina. Só muda o comando de partida: o container sobe o servidor HTTP (`uvicorn`)
   em vez de executar uma rodada. O código não é reescrito.
2. **O painel são arquivos estáticos** do Worker que fica na frente da API. Painel e
   API continuam na **mesma origem**, porque a sessão depende disso.
3. **No fim, um Worker só** (`liveagents-worker`) com o painel, a API e a rotina. O
   Worker é criado do zero porque o nome do Worker da rotina (`fluxo-agentico-rotina`)
   não pode ser trocado. A agenda é a **última** coisa a mudar, num horário sem
   rodada.
4. **Um container só.** A plataforma não escala sozinha; subir mais de um fica para
   quando a carga pedir.
5. **O container desliga depois de um tempo sem pedido** (30 min no código da
   PRO-1618). O pedido seguinte espera a partida.
6. **O Worker lê o corpo do pedido antes de esperar a partida.** Assim o pedido chega
   à API mesmo que quem chamou desista no meio, como o Slack faz aos 3 s.
7. **Publicar uma imagem nova reinicia o container.** Os pedidos em andamento têm um
   prazo curto para terminar.
8. **A Vercel e a Cloudflare rodam em paralelo**, no mesmo banco, até a virada. A
   virada é a troca do domínio, e voltar atrás é apontar o domínio de volta. A Vercel
   só é desligada depois de alguns dias estável.
9. **O banco não muda.**
10. **Domínio próprio: `liveagents.livemode.space`** (PRO-1617). Ele começa
    apontando para a Vercel (PRO-1619) e só depois vira para a Cloudflare.
11. **Slack continua como hoje.** O evento é reentregue, e a trava por `event_id`
    impede a issue duplicada.

## Alternativas recusadas (ADR 0048)

- **Python Workers**: pede reescrever o I/O para assíncrono e que toda dependência
  rode em WebAssembly. Mesma conclusão do estudo `docs/future_migrations/cloudflare.md`
  (caminho A contra caminho B).
- **Container sempre ligado**: cerca de US$ 27/mês para tirar 10 s do primeiro
  acesso depois de um tempo parado.
- **Worker responder ao Slack antes de repassar o pedido**: a conferência de
  assinatura ficaria em dois lugares. Tratado como melhoria à parte, fora do escopo
  da migração.
- **Dois Workers para sempre, um da rotina e um da API**: os dois levam a mesma imagem
  e já são publicados juntos, então seriam dois lugares de segredo e dois deploys.
  *Correção do rascunho de 2026-11-12, que tinha escolhido justamente essa forma (ver
  a página do plano).*
- **Painel e API em origens diferentes**: quebra a sessão.

## Medições do spike (2026-11-22)

Fonte: notas da PRO-1617. O teste rodou num Worker descartável,
`liveagents-api-spike`, com chaves falsas e sem banco.

- Primeira resposta com o container parado: **10 a 11 s**, ou 19 s na primeira vez,
  com a imagem fora do cache. O tempo é o mesmo em `standard-1`, `standard-2` e
  `standard-4`.
- Resposta com o container ligado: **0,18 s**.
- O app sozinho, rodando local, sobe em 1,2 s com CPU cheia e 2,4 s com meia CPU.
  Usa 190 MiB de memória parado. A imagem tem 177 MB.
- Se o Worker não lê o corpo na chegada e quem chamou desiste, o repasse falha com
  *"Can't read from request stream because client disconnected"*. Isso originou a
  decisão 6.
- A rota do Slack já leva cerca de 15 s hoje, e toda menção ao bot é reentregue.
- **Não medido**: rota autenticada e chamada de agente de ponta a ponta dentro do
  container, e o custo real na fatura.

## Consequências (ADR 0048)

- O primeiro acesso ao painel, ao chat ou ao MCP depois de um tempo parado leva cerca
  de 10 s. Nesse caso, o bot do Slack leva cerca de 25 s, contra 15 s hoje.
- Uma chamada longa em andamento durante um deploy pode ser cortada.
- **Acaba a prévia por PR da Vercel.** Ainda não há nada definido para substituí-la.
- Só dá para publicar com Docker. Sem Docker na máquina, só o CI publica.
- Voltar uma versão do Worker volta o painel, a API e a rotina juntos.
- Acaba o teto de 800 s por pedido. Os prazos do código que copiavam esse teto
  precisam ser revistos.

## Etapas (PRO-1617)

1. **Painel e API na Cloudflare, ao lado da Vercel**:
   [PRO-1618](https://linear.app/projetos-livemode/issue/PRO-1618/o-painel-e-a-api-do-liveagents-sobem-na-cloudflare-ao-lado-da-vercel).
   O código está pronto na branch citada acima (`apps/edge/`, ADR 0048, ajuste no
   `Dockerfile`), mas ainda não foi mergeado nem publicado.
2. **Domínio próprio ainda na Vercel**:
   [PRO-1619](https://linear.app/projetos-livemode/issue/PRO-1619/o-liveagents-passa-a-responder-em-liveagentslivemodespace-ainda-na).
   Inclui migrar quem usa o endereço antigo: conectores MCP de cada pessoa,
   [[LiveAuth]], app do Slack, webhook do Linear e os links que os agentes escrevem.
3. Tirar do código, dos segredos e da publicação o que depende da Vercel. Os pontos
   do código estão listados no
   [[Plano de migração da API do Fluxo Agêntico para a Cloudflare|rascunho de 2026-11-12]].
4. Virar o domínio para a Cloudflare.
5. Depois de alguns dias estável, desligar o projeto da Vercel e limpar o que cita a
   Vercel no repositório.
6. Levar a agenda e o código da rotina para o `liveagents-worker` e apagar o
   `fluxo-agentico-rotina`.

## Contexto

- Diretriz de migrar da Vercel para a Cloudflare por custo, registrada em
  [[2026-10-14 Workflow de Git + Linear Status]].
- [[LiveAuth]] e o LiveStry já rodavam na Cloudflare. Com esta mudança, a Vercel
  ficaria só como segunda fatura e segundo lugar para publicar, guardar segredos e
  observar (ADR 0048, "Contexto").
- A rotina já estava na Cloudflare desde 2026-11-10 (ADR 0028, PRO-1481).
