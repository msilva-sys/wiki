---
type: meeting
status: active
updated: 2026-08-27
date: 2026-08-26
attendees: [Luís Fernandez, Matheus Silva]
source: https://notes.granola.ai/t/89359f9a-b8ce-4b0f-b2e7-bf36c84c4864-008umkv4
transcription_confidence: medium
aliases: [luís 1:1 26/08, portfolio agent langgraph debate]
tags: [agents, agent-flow, a10, a14, langgraph, langfuse, trigger-input, linear]
---

# 1:1 Matheus - Luís — 2026-08-26 (arquitetura A10/A14 e debate LangGraph)

Título original do Granola: *"Portfolio agent — architecture, capabilities,
and LangGraph approach with Luís"*. Sem transcrição bruta: fonte é um link
de notas estruturadas do Granola (IA), não um transcript verbatim
diarizado — mesmo caso de
[[2026-08-25 Agent Flow discovery with Mafê]], daí `transcription_confidence:
medium`. Atribuição de fala segue os nomes que as próprias notas do Granola
já citam ("Luís pediu...", "Matheus reconheceu..."); onde a nota não nomeia
quem falou, isso é sinalizado abaixo em vez de assumido.

**Data**: rótulo relativo do Granola era "Yesterday" no momento da leitura
(2026-08-27) → 2026-08-26. *(unverified: sem timestamp absoluto na página;
inferido do rótulo relativo, não de um cabeçalho de data.)*

## Decisions

Nenhuma decisão fechada nesta reunião. O assunto central — LangGraph vs.
agente autônomo pra A10/A14 — ficou como **debate em aberto**: Luís
questionou o argumento de Matheus e pediu mais material antes de qualquer
lado se fechar (ver Commitments e Open questions). Isso é anterior à sessão
de design técnico do mesmo dia registrada em
[[Desenho do agente LangGraph para A10+A14]] — aquela página parece ser
exatamente a resposta ao pedido de Luís aqui (ver seção própria abaixo).

## Commitments

Todos de Matheus, roteados para o Linear por serem dele e caberem no time
[[Projetos-livemode]]/[[Fluxo Agêntico]]:

- **Documentar e compartilhar a arquitetura dos agentes com o time**
  (conceito de trigger vs. input, arquitetura modular, direção geral) — sem
  issue prévia cobrindo isso; criada
  [PRO-430](https://linear.app/projetos-livemode/issue/PRO-430/documentar-e-compartilhar-arquitetura-dos-agentes-com-o-time).
- **Enviar a Luís uma referência resumida da decisão LangGraph vs. agente
  autônomo** (hipótese + critérios + referência), incluindo reflexão sobre
  se a escolha se sustenta num cenário conversacional — o conteúdo já existe
  (descrição de
  [PRO-392](https://linear.app/projetos-livemode/issue/PRO-392/implementar-poc-em-langgraph),
  escrita mais tarde no mesmo dia, ver [[Desenho do agente LangGraph para
  A10+A14]]), mas **o envio a Luís e a reflexão sobre o cenário
  conversacional não estão confirmados como feitos**.
- **Desenhar arquitetura mínima dos agentes, começando pelo de portfólio, e
  compartilhar com Luís** — já rastreado em
  [PRO-375](https://linear.app/projetos-livemode/issue/PRO-375/especificar-a10-portfolio)
  ("Especificar A10 Portfolio"), aberta desde 2026-08-24. Nenhuma issue nova
  necessária.
- **Destrinchar capacidades do agente de portfólio** (o que entrega, em que
  formato, para quem) — mesmo escopo de PRO-375 acima (já pede "inputs e
  outputs definidos", "critério de sucesso e limites"). Nenhuma issue nova.
- **Pegar a gravação da apresentação do sistema para Jasmin** (Luís vai
  compartilhar) — item pequeno, não virou issue Linear; registrado só aqui.

## Open questions

- **Se o agente precisar ser conversacional** (Slack, chat interno,
  Telegram), a escolha LangGraph vs. agente autônomo ainda faz sentido?
  Luís levantou; Matheus reconheceu não saber e Luís pediu reflexão antes da
  próxima conversa. Não respondida em
  [[Desenho do agente LangGraph para A10+A14]] — aquela página assume uso
  CLI single-shot, não conversacional.
- **Formato de saída do agente de portfólio** — dashboard, mensagem no
  Slack, ou relatório semanal — ainda em aberto nesta reunião. *(Parcialmente
  respondida depois, no mesmo dia: [[Desenho do agente LangGraph para
  A10+A14]] fechou em HTML gerado por invocação manual via CLI, pra essa
  fase de PoC — não necessariamente a resposta final de produto que Luís
  estava perguntando aqui.)*

## Facts stated

- **Luís**: distinção importante entre **trigger** (o que dispara o agente)
  e **input** (contexto/dados que ele consome) — mesma distinção que ele já
  tinha passado por Slack em 2026-08-21, ver [[Agent Harness Template]].
  Reforço independente, em conversa ao vivo, do mesmo conceito.
- **Luís**: questionou o argumento de Matheus de que "portfólio não tem
  etapas tão determinísticas" justificaria pular o LangGraph — apontou que
  o LangGraph também suporta arquiteturas zero-deterministic, não é
  exclusividade do agente autônomo.
- **Luís**: identificou funcionalidades no projeto sem eventos de analytics
  há 3+ meses *(não fica claro qual projeto — não nomeado nas notas)* —
  oportunidade de um agente disparando semanalmente pra analisar métricas e
  sugerir exclusão de funcionalidades mortas. A14 (ou um "agente de uso")
  já previsto pra cobrir isso.
- **Luís**, analogia: agente é como funcionário novo — precisa de objetivo
  claro e entrega de valor definida; sem isso, expectativas divergem e "o
  produto conversacional vira mágica que faz tudo."
- **Luís**: recomendou documentar e compartilhar com o time a evolução do
  entendimento sobre agentes — benefícios citados: visibilidade do avanço,
  abertura pra colaboração, validação do conceito.
- **Luís**: pediu mais desenho de arquitetura, não necessariamente formal —
  facilita troca e identifica gaps antes de implementar.
- **Luís**, feedback geral: o processo está indo bem; a intensidade das
  correções é parte do processo (não é sinal de problema).
- *(Sem falante nomeado explicitamente na nota)* Recomendação de
  ferramentas: planejar no Sonnet, executar no Haiku (conta de $200/mês) —
  argumento de que otimizar token sem necessidade é produtividade perdida.
  *(unverified: não fica claro se é recomendação de Luís ou nota própria de
  Matheus — atribuição não confirmada.)*
- *(Sem falante nomeado, e sem sistema identificado)* Uma versão foi
  liberada para Carol com roteiro de teste; Matheus foi adicionado a um
  canal pra acompanhar a dinâmica; método de triagem de bugs via LLM
  (pedir ordenação por criticidade, proposta de solução e impacto) —
  itens menos críticos aprovados rapidamente, foco nos bloqueadores reais.
  *(unverified: qual sistema/projeto é este — não é A10/A14, que ainda não
  foi construído nesta data; possivelmente [[Farol]] ou outro projeto do
  time, mas as notas não nomeiam. Não fanned out para nenhuma project page
  até confirmar.)*

## Notable quotes

Nenhuma — as notas do Granola vêm em formato de bullets estruturados
(paráfrase da IA), sem falas verbatim como uma transcrição bruta teria.

## Fan-out

- [[Agent Harness Template]] — reforço independente da distinção trigger
  vs. input (Luís, ao vivo, 2026-08-26).
- [[Agent Flow]] — callout novo linkando esta reunião: pedido de Luís por
  capacidades específicas por agente, e por documentação/compartilhamento
  com o time.
- [[Desenho do agente LangGraph para A10+A14]] — nota de que esta reunião é
  provavelmente a origem do pedido de Luís por "referência resumida da
  decisão com critérios claros", que aquela página parece resolver (mesmo
  dia, sessão posterior).
- [[Luís Fernandez]] — nova linha de atividade.
