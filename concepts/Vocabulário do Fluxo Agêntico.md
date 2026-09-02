---
type: concept
status: active
updated: 2026-09-02
aliases: [glossário do Fluxo Agêntico, sinal, contrato de agente, ticket graph, gateway, soul, profile, fragment]
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
- **Gateway** — a camada de infra pura que recebe o trigger bruto de
  qualquer canal (webhook do Slack, payload de cron, chamada HTTP) e
  normaliza antes de entregar pro sistema agêntico. Não é um agente, não
  tem lógica de negócio — só transporte e normalização, exatamente pra
  não "contaminar" o sistema agêntico com preocupação de infra (parsing
  de payload, assinatura, autenticação). A fronteira do sistema agêntico
  começa em **A1 Receptor Universal**, não no gateway: A1 já interpreta a
  demanda como conceito de domínio (o que é, quem pediu, que forma tem) —
  isso é raciocínio, não infra. **Correção registrada aqui** (msilva,
  2026-08-28): duas leituras erradas foram cogitadas e descartadas na
  mesma conversa — equiparar A1 ao gateway, e traçar a linha entre A1 e
  A2. A linha real é **gateway (fora do sistema, puro transporte) vs. A1
  em diante (tudo já é domínio)**.
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
- **SOUL** — fonte comportamental de um agente, independente do prompt de
  tarefa (que continua vivendo no Langfuse). Não é "sinal" nem "memória do
  sistema agêntico" no sentido em aberto abaixo — é config de como o agente
  se porta entre tarefas/canais, versionada e implementada em A10/A14. Ver
  [[2026-09-01 Modelar SOUL em tabelas com composição plana]].
- **Fragment / Profile** — vocabulário interno da SOUL. Fragment é um
  bloco de comportamento reutilizável (ex. um tom, uma postura); profile é
  a composição ordenada de fragments + texto próprio que um agente de fato
  usa. Um agente aponta pra uma versão exata de um profile, nunca "pro
  profile" em geral.
- **Promoção** — trocar qual versão de profile está ativa pra um
  `agent_key`+`environment`; é também o mecanismo de rollback (promover de
  volta uma versão anterior), não uma operação separada.

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

- "Sinal" e a **acumulação de sinal entre agentes diferentes** (A7
  consultando o que A5/A10/A11/A12 já observaram, sem refazer discovery do
  zero) ainda não foram desenhados a fundo — onde essa memória mora, como é
  indexada/buscada. Mesma lacuna registrada em [[Agent Flow]] (Luís,
  2026-08-20), ainda sem dono. **Corrigido, auditado no código, 2026-09-02**:
  isso não é mais toda a "memória do sistema agêntico" em aberto — SOUL
  (memória comportamental/config) e a memória de domínio por agente
  (snapshot do A14, histórico do A10) já existem e estão implementadas; só
  a acumulação de sinal *entre* agentes segue sem desenho.
