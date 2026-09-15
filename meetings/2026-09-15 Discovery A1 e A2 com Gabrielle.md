---
type: meeting
status: stable
updated: 2026-09-15
date: 2026-09-15
attendees: [Matheus Silva, Gabrielle Ferreira]
transcription_confidence: low
aliases: [discovery a1 a2 gabi, reunião gabrielle a1 a2]
tags: [agent-flow, agents, a1, a2, gabrielle, discovery, portfolio, airtable, priorizacao]
---

# Discovery A1 e A2 com Gabrielle — 2026-09-15

Continuação de [[Meeting prep - Discovery A1 e A2 com Gabrielle - 2026-09-15]].
Objetivo duplo: discovery do projeto **A1 & A2** no Linear (PRO-543/544) e,
aproveitando a conversa, PRO-595 (regras de priorização de portfólio que
Gabrielle já tem, levantadas por Carolina em
[[2026-09-14 Carolina - Matheus (critérios A10-A14)]]).

**Confiança de transcrição baixa** — mesmo problema de diarização da
reunião com Carolina (2026-09-14): quase toda fala vem rotulada
"Gabrielle Ferreira", incluindo trechos que pelo conteúdo são claramente
do msilva (ex.: *"Não, eu já vou te pedir, eu preciso desse cálculo"*,
*"Eu acho que o projeto seria tranquilo"*). Atribuição abaixo por
inferência de conteúdo, não por rótulo confiável.

## Decisions

Nenhuma decisão formal fechada — reunião de discovery, sem fechamento.

## Commitments

- msilva vai marcar a reunião com Carolina, Luís e Gabrielle pra definir os
  critérios novos de priorização de portfólio — PRO-595.
- msilva vai pensar em formatos de exposição do A1 receptor (bot num canal,
  menção em DM, encaminhamento) e compartilhar com Gabrielle.

## Open questions

- Quais critérios de priorização de portfólio a área vai usar daqui pra
  frente. Os antigos do GPT de Gabrielle (valor pro negócio peso 2,
  complexidade de desenvolvimento, escalabilidade, economia de tempo)
  caíram em desuso segundo ela mesma e não devem servir de base — PRO-595.
- Vale manter um cálculo ponderado (fórmula com pesos por critério) ou
  simplificar? Só faz sentido manter pesos diferentes se o time ainda
  quiser isso — em aberto, decisão da reunião futura com Carol e Luís.
- Como demandas soltas (que não são um projeto) entram no Linear sem
  forçar todo pedido a virar "Projeto"? Gabrielle aponta a aba/status
  **Triagem** como candidata — não testada, não confirmada.
- Ferramenta própria da Gabrielle (a que ela chama de app/"Wikly") ainda
  cria tarefa no Airtable em vez do Linear quando alguém abre uma tarefa
  em seu nome — bug/gap identificado na própria conversa, não corrigido.
- Por que a conta do GPT Business consome crédito ao ser acessada via
  Codex — pendência tangencial, checar com TI.

## Facts stated

- Gabrielle: hoje a área recebe demanda por duas fontes que ainda não
  passam pelo Linear — tarefas soltas continuam sendo criadas no Airtable
  (inclusive pela ferramenta dela, que aponta pra lá e não pro Linear), e
  mensagem direta no Slack, sem registro em lugar nenhum.
- Gabrielle: todo o histórico de projetos anterior ao "marco zero" da
  migração pro Linear ([[2026-08-14 Migrate project management from Jira
  to Linear]]) — backlog nunca tocado, projetos concluídos, abandonados —
  vive só no Airtable, nunca foi migrado.
- Gabrielle: o board de priorização de portfólio (antigo) tinha uma coluna
  de "hold" com motivo explícito registrado (dependência de projeto
  anterior, falta de gente disponível, ou pedido do próprio solicitante
  pra não priorizar agora).
- Gabrielle: ela mesma construiu um GPT customizado — **"priorizador de
  projetos"**, https://chatgpt.com/gpts/editor/g-6967b8ab3140819197cda61702e3a006
  — com um prompt de contexto da empresa, critérios com peso (valor pro
  negócio tinha peso 2) e regras extra: sugeria validar com um teste
  pequeno antes de virar projeto corporativo, avaliava risco/complexidade,
  sugeria quebrar em sub-projetos. Rodava toda quinta antes da reunião de
  priorização (ela, Carol, Luís, Arthur); trazia nota sugerida por
  critério, o grupo validava/ajustava.
- Gabrielle: esses critérios antigos caíram em desuso — não devem servir
  de base pra conversa nova com Carol e Luís.
- Gabrielle: canal oficial hoje é `#resolveaqui-livemode` ("resolve
  aqui"), mas é minoria do volume real — maioria ainda manda mensagem
  direta no Slack. Existiu um formulário antigo na plataforma "Fill",
  removido de propósito quando as áreas passaram a resolver mais sozinhas
  (pra reduzir pedido indiscriminado) — isso, sem querer, também apagou o
  conhecimento de qual é o canal oficial hoje.
- Gabrielle: ideia inicial pra expor o A1 receptor — bot hospedado na
  Vercel, conectado a uma skill do Claude que acessa o Slack. Formato
  ainda não decidido (canal que só encaminha vs. bot mencionável em DM).

## Notable quotes

- Gabrielle: *"A gente não tem um local meio que unificado para receber
  essas demandas."*
- Gabrielle, sobre os critérios antigos: *"isso daqui meio que caiu em
  desuso, então não acho que a gente deveria usar isso daqui como base."*

## Depois da reunião

Achados aqui ainda não foram levados pro Linear — msilva revisa antes de
qualquer atualização em PRO-543/544/595.

## Relacionado

- [[Meeting prep - Discovery A1 e A2 com Gabrielle - 2026-09-15]]
- [[A10 avalia saúde, não prioriza portfólio]]
- [[Agent Flow]]
- [[Gabrielle Ferreira]]
