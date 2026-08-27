---
type: concept
status: active
updated: 2026-08-27
aliases: [glossário do Fluxo Agêntico, sinal, contrato de agente, ticket graph]
tags: [agents, agent-flow, vocabulary, architecture]
---

# Vocabulário do Fluxo Agêntico

Termos que emergiram organicamente ao longo do desenho técnico da PoC A10+A14
e do mapeamento de contrato dos demais agentes (2026-08-26/27), registrados
num lugar só depois que ficou claro que estavam sendo usados sem definição
explícita em vários lugares ao mesmo tempo.

- **Trigger vs. Input** — o que dispara um agente (canal, evento: Slack,
  cron, webhook) separado do que ele processa (o dado em si). Distinção de
  Luís Fernandez, ver [[Agent Harness Template]].
- **Entrada / Saída** — a fronteira de cada agente; o contrato em si,
  definido por formato, independente de quem chama. Padrão **ports and
  adapters** (arquitetura hexagonal), ver [[Desenho do agente LangGraph
  para A10+A14]].
- **Adapter** — o componente que traduz um canal específico (Slack, CLI,
  webhook...) pro formato da Entrada. O agente não sabe nem precisa saber
  qual adapter o chamou.
- **Sinal** — observação bruta ou semi-processada que ainda não é decisão;
  entra como parte da Entrada, o agente aplica julgamento em cima dela, e o
  que sai (Saída) é o veredito, com o sinal como evidência anexada — não o
  sinal em si. Generalização, pro sistema inteiro, da divisão
  determinístico-vs-julgamento já usada no toolset do A10 (tool calcula o
  sinal objetivo; o agente julga). Onde a memória do sistema agêntico
  (ideia levantada durante o mapeamento do A4, ainda não desenhada a fundo)
  se encaixa: é onde sinal se acumula ao longo do tempo, pra outros agentes
  consultarem depois (ex.: A7 consultando sinais já acumulados de A5/A10/A11/A12
  em vez de fazer discovery do zero a cada projeto novo).
- **Workflow** — padrão de passos fixos, decidido no código antes de rodar.
  Termo técnico, não traduzido pra "fluxo" — ver a nota de vocabulário
  abaixo.
- **Agent** (padrão) — loop onde o próprio modelo decide, a cada passo,
  qual tool chamar e quando parar. Contraposto a *workflow* — distinção da
  [doc oficial do LangChain](https://docs.langchain.com/oss/python/langgraph/workflows-agents).
- **Ticket graph / DAG** — decomposição de um projeto em unidades menores
  (tickets), com dependência explícita entre elas (`blockedBy`, nunca
  inferida do título). Modelo do [[Gabriel Packer - DAG-driven agent
  orchestration]], adotado como saída do A7 Discovery.
- **Contrato** — a especificação Entrada→Saída de um agente. Regra
  central: definido por **formato**, nunca por **quem chama** — "vem do
  A8" não é contrato, é nota de uso típico.

## Como isso se relaciona com o vocabulário de nomes de projeto

Distinto da convenção de nomes registrada em [[Agent Flow]] (2026-08-27):
"Fluxo Agêntico" (nome próprio do projeto) vs. "sistema agêntico" (a
arquitetura toda, em prosa) vs. "workflow" (termo técnico daqui). Essa
página é sobre os termos técnicos de desenho; aquela é sobre não confundir
o nome do projeto com um deles.

## Onde isso é usado

Aplicado nos contratos mapeados agente-a-agente — ver o artifact
"Contratos dos Agentes" (compartilhado com o time, referência externa).
Espelhado no Linear como Document próprio, "Glossário do Fluxo Agêntico"
(projeto Fluxo Agêntico), pra não duplicar a definição em cada Document
que usa esses termos ("Arquitetura dos Agentes A10 + A14", "Contratos dos
Agentes").

## Open questions

- "Sinal" e "memória do sistema agêntico" ainda não foram desenhados a
  fundo — onde essa memória mora, como é indexada/buscada. Mesma lacuna já
  registrada em [[Agent Flow]] (Luís, 2026-08-20: organizar memória bem é
  "talvez a coisa mais importante do projeto todo", mas ninguém é dono
  disso ainda).
