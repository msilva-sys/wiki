---
type: decision
status: stable
date: 2026-11-23
updated: 2026-11-23
aliases: [registro de propostas, rota verdict, veredito do curador, PRO-1621]
tags: [livestry, skills, liveagents, curadoria]
---

# 2026-11-23 LiveStry guarda o status das propostas de skill

O LiveStry passa a anotar cada proposta de skill e o que acontece com ela, mesmo
enquanto a proposta está no LiveAgents, sendo julgada pelo `skill_curator`. O
LiveAgents avisa o veredito numa rota só. Quando o PR fecha, o GitHub avisa o
LiveStry, que repassa o desfecho ao LiveAgents.

Decidido por msilva em chat (Claude Code), em 2026-11-23, logo depois de
[[2026-11-23 Separar no LiveStry o registro das interfaces]]. Rastreado na
[PRO-1621](https://linear.app/projetos-livemode/issue/PRO-1621/acompanhar-no-livestry-em-que-pe-esta-cada-proposta-de-skill)
(registro e veredito) e na
[PRO-1615](https://linear.app/projetos-livemode/issue/PRO-1615/avisar-quem-propos-quando-o-pr-da-skill-e-mergeado-ou-fechado)
(PR fechado), ambas filhas da PRO-1616.

## O que ficou decidido

- **Registro de status no D1 do LiveStry** (a base `livestry`, que já existe). É um
  registro de status, não uma fila de trabalho. A fila que processa a proposta
  continua sendo a `skill_curator_events` do LiveAgents.
- **Só a impressão digital do conteúdo (sha256), não o conteúdo.** A ADR 0003 do
  livestry continua valendo ("o servidor não guarda nada"). Quando a proposta
  aprovada volta, o LiveStry confere que o que vai para o PR é o que foi julgado.
- **Rota única `POST /proposals/{id}/verdict`**, para aprovação e reprovação.
  Substitui `POST /proposals/approved` (PRO-1611). Quem decide o status é sempre o
  LiveStry.
- **Status:** `em_curadoria` → `reprovada` ou `em_revisao` (PR aberto) → `publicada`
  ou `recusada`.
- **PR fechado: o GitHub avisa só o LiveStry** (`POST /webhooks/github`). O LiveStry
  acha as propostas pelo número do PR, atualiza o status e repassa ao `skill_curator`
  (`POST /api/skill_curator/outcomes`), que manda as DMs.
- **As DMs no Slack continuam saindo do LiveAgents** (PRO-1613). Os contribuidores
  continuam vindo da fila do LiveAgents.

## Recusado

- **O LiveAgents escrever direto no D1 do LiveStry** (proposto por msilva). Recusado
  em chat. Motivos:
  - as regras de status ficariam repetidas em Python;
  - o schema viraria contrato entre dois repositórios, em duas linguagens, sem teste
    que pegue a quebra;
  - fora de um Worker, o D1 só é acessível com um token de API da Cloudflare. Não
    verifiquei se esse token pode ser restrito a uma única base;
  - escrever no banco de outro produto foge do efeito declarado da ADR 0014 do
    LiveAgents.

  O ganho também era pequeno: na aprovação, a chamada ao LiveStry existe de qualquer
  jeito, porque só ele escreve no GitHub.
- **O GitHub avisar os dois** (um segundo webhook no repositório, apontando para o
  LiveAgents). Recusado porque duas entregas independentes podem divergir, e a App só
  tem uma URL de webhook.

## Por quê

Com a curadoria, a proposta passa um tempo no LiveAgents antes de virar PR, e nesse
intervalo o LiveStry não sabia que ela existia. Quando o curador reprovava, nunca
ficava sabendo. O design doc admitia isso ("Proposta só na fila, sem PR: o LiveStry
não a enxerga"). Com o status no registro, o ciclo de vida da proposta passa a ser do
registro. O GitHub fica só como onde a skill é publicada, e o LiveAgents só como quem
julga e avisa.

Outros ganhos:
- abre caminho para uma tool que mostre à pessoa as propostas dela;
- deixa visível a proposta parada em `em_curadoria`;
- a impressão digital resolve a pendência da PRO-1616 sobre a mesma proposta mandada
  de novo sem mudança.

## Em aberto

- Status quando a validação recusa uma proposta já aprovada pela curadoria, porque a
  base mudou nesse meio-tempo.
- Status de proposta parada em `em_curadoria` por muito tempo, quando o curador falha.
- Quem alerta quando o repasse do LiveStry ao LiveAgents falha. O LiveStry não fala
  com o Slack. A saída proposta é responder erro ao GitHub, para reenviar à mão (é
  seguro porque o status e a DM não se repetem).

## Relacionadas

- [[Design Doc — Curadoria de skills pelo LiveAgents]]: esta decisão corrige os
  blocos 4 e 5 e a decisão "O LiveStry continua sem estado".
- [[2026-11-23 Separar no LiveStry o registro das interfaces]]
