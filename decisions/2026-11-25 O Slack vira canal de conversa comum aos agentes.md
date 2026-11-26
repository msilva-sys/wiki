---
type: decision
status: stable
date: 2026-11-25
updated: 2026-11-26
aliases: [ADR 0056, ADR 0058, entrada compartilhada do Slack, porta do Slack, Slack comum, PRO-1705, PRO-1674]
tags: [agent-flow, liveagents, slack, a10, a14, memory, confirmation]
---

# 2026-11-25 O Slack vira canal de conversa comum aos agentes

O Slack deixa de ser só a porta do intake e passa a ser **uma segunda porta para a
mesma conversa do painel** do [[Agent Flow|LiveAgents]]: um robô só para todos os
agentes, com a mesma sessão e a mesma memória do chat. Quem decide de qual agente é a
mensagem é a conversa em que ela chega, não o conteúdo.

Fontes: **ADR 0056** (aceita em 2026-11-25,
[PRO-1705](https://linear.app/projetos-livemode/issue/PRO-1705), `Done`) e **ADR 0058**
(aceita no mesmo dia, PRO-1674), em `docs/decisions/` do `livemode-fluxo-agentico`. A
0056 substitui o item 2 da ADR 0020 e detalha o item 10 da ADR 0053. O código foi lido
no `main` em `7807a0c` (2026-11-26). Decisões de msilva, registradas como ADR aceita.

## Por que

- Antes, o Slack só recebia mensagens para o intake: menção virava demanda e mensagem
  em canal de alerta virava issue. O A10 e o A14 só publicavam (cobrança por DM, avisos
  em grupo), e a resposta da pessoa não chegava a agente nenhum.
- Aprovar o que o agente aprendeu (fatos e ações, ADR 0053) só dava pelo painel, e só
  o admin aprovava. Quem conhece os projetos não abre o painel: em 2026-11-25 havia
  **1 fato e 91 ações pendentes, nenhuma aprovada** (ADR 0056, Contexto).

## O que ficou decidido (ADR 0056)

1. **Um robô para todos os agentes.** A entrada assinada sai do intake e vai para
   `agents/common`, com o segredo da assinatura. A mensagem é entregue à entrada de
   chat que o manifesto do agente já declara. Agente novo com chat ganha o Slack sem
   trabalho de canal.
2. **A conversa escolhe o agente.** Resposta na thread de uma mensagem de agente vai
   para o agente que a publicou, sobre o projeto ou a iniciativa dela. Na DM com o robô,
   a mensagem solta continua a conversa do último agente que escreveu ali. Por isso
   cada publicação guarda o agente e o assunto.
3. **Mensagem nova continua com o intake**: menção fora de conversa vira demanda, e
   canal de alerta vira issue.
4. **O agente só lê o que é dirigido a ele**: respostas às mensagens dele e a DM com o
   robô.
5. **A pessoa é o e-mail que o Slack informa**, o mesmo do painel. Não há cadastro
   próprio de contas do Slack.
6. **Quem confirma**: o responsável pelo projeto no Linear confirma o que é do projeto
   dele, e o admin confirma qualquer coisa. Vale igual no painel e no Slack.
7. **Confirmação por texto.** O agente repete o que vai gravar e pergunta sim ou não.
   Só resposta clara grava. O registro guarda quem respondeu, a frase e o link da
   mensagem, e pode ser desfeito. Vale para fatos e ações; sugestões da auto-melhoria
   seguem desenho próprio.
8. **A resposta cabe numa mensagem.** Tabela e tela ficam no painel, por link.

## A porta pergunta de qual assunto (ADR 0058)

No mesmo dia, a ADR 0058 trocou a última frase do item 2. A cobrança do A14 junta numa
DM todos os projetos da pessoa, e o aviso do A10 cobre vários itens. Na 0056, quando a
resposta não dizia de qual projeto era, **o agente** perguntava. Na 0058 **quem pergunta
é a porta comum**, antes de a mensagem chegar ao agente:

- a pergunta traz uma lista fixa e numerada com os nomes dos assuntos da última
  mensagem do agente, sem usar o modelo; a pessoa responde com o número ou o nome;
- a mensagem original da pessoa espera a escolha e chega ao agente já com o assunto;
- o agente só recebe conversa com assunto definido, e cada agente novo não precisa
  tratar o caso.

Recusado: deixar o agente perguntar. A pergunta sairia mais natural, mas o chat do A14 é
sempre sobre um projeto, e o caso se repetiria em cada agente.

## Como está no código (`main` em `7807a0c`)

Em `packages/agents/src/agents/common/infra/slack/`:

- `events.py`: a rota `/slack/events`. Confere a assinatura HMAC, ignora mensagens do
  próprio app (os bots de observabilidade passam, porque deles vem o alerta), trava
  reentrega por `event_id` na tabela `slack_events` e passa o evento por uma lista de
  handlers, em ordem. O primeiro que aceita faz o trabalho. Com
  `work_after_response` ligado (Cloud Run), responde 200 e processa depois.
- `conversations.py`: cada mensagem publicada pelo envio comum vira um registro na
  memória do agente que publicou, na etiqueta `slack_message`, com o canal ou a thread
  e as unidades de que fala, com nome.
- `threads.py`: o handler das conversas de agente. Aceita a resposta numa thread de
  agente ou a mensagem solta na DM, acha o dono pela última mensagem de agente na
  conversa e faz a pergunta da 0058 quando há mais de um assunto. Cada unidade tem
  conversa própria em cada thread ou DM.
- A montagem fica em `apps/api/src/livemode.py`: primeiro as conversas de agente,
  depois o intake.

Só entra no Slack o agente que declara a entrada de chat (`CONVERSE`) e a etiqueta
`slack_message` no manifesto.

## Alternativas recusadas (ADR 0056)

- Só um aviso com botões para aprovar: a pessoa não conversa nem ensina pelo Slack.
- Um robô por agente: um app a manter por agente, e a pessoa precisa saber qual chamar.
- Um robô que encaminha pelo conteúdo: pode errar o agente. Adiado até haver pedido
  real.
- Confirmar por botão: exige receber cliques, o que não existe. Adiado enquanto o texto
  não errar.

## Consequências e riscos aceitos

- O modelo interpreta o "sim" e pode gravar algo que a pessoa não quis. A repetição
  antes e o desfazer depois reduzem o dano. Se acontecer, o botão volta numa ADR nova.
- A regra da DM pode levar a resposta ao agente errado quando dois agentes escrevem
  para a mesma pessoa em dias próximos. A conferir nas primeiras semanas.
- Na 0058, uma conversa que muda de assunto no meio continua no assunto escolhido até a
  próxima mensagem de agente com vários assuntos.
- O desenho do Slack na auto-melhoria (confirmar por botão, perguntar o projeto na
  mensagem solta) deixa de valer para fatos e ações.

## Em aberto

- Resposta a mensagem antiga: segue o prazo da sessão do painel ou vira conversa nova?
- Pessoa cujo e-mail não tem conta.
- Começar conversa do zero com um agente que não seja o intake.
- A [PRO-1674](https://linear.app/projetos-livemode/issue/PRO-1674) (a pessoa conversa e
  confirma pelo Slack) estava `In Progress` em 2026-11-26. Commits de 2026-11-25
  desligam a menção e a mensagem de canal pelo Slack no intake "até ser validada"
  (unverified: se já foram religadas).

## Relacionado

[[Agent Flow]], [[2026-11-25 Estágio do projeto e retrato do portfólio na memória do A10]],
[[2026-11-23 A API e o painel do LiveAgents vão para o Cloud Run]] (é onde a rota passa a
responder antes de processar), [[Design Doc — Intake]].
