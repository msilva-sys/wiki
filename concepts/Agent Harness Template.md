---
type: concept
status: draft
updated: 2026-10-20
aliases: [harness template, trigger input harness output, agent template (Luís)]
tags: [agent-flow, architecture, harness, claude-agent-sdk]
---

# Agent Harness Template

Formato genérico para especificar qualquer agente, proposto por Luís Fernandez
em uma mensagem no Slack para msilva, em 2026-08-21 (sem arquivo em `raw/` —
colado diretamente no chat de trabalho, não veio de uma transcrição):

```
Trigger (Human | Machine)
Trigger Channel (Slack | Webhook | Cron | etc)
Input
====================
AGENT PROCESS
- Harness
---- Soul.md (como deve se comportar, falar etc)
---- Skills
---- Tools
---- MCP
---- etc
====================
Output (result, changed systems, etc)
```

## Como se conecta ao que já existe na wiki

**Refina o método actor→input→output.** Em
[[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]], Luís propõe que
cada entidade do [[Agent Flow]] seja
especificada somente como actor → input → output, sem desenhar antecipadamente
o grafo entre agentes. Este template é a mesma ideia com mais resolução: o lado
do “actor” se divide em **tipo de Trigger** (Human ou Machine) e **Trigger
Channel** (Slack, Webhook, Cron etc.). Entre Input e Output fica a parte que o
método anterior deixava deliberadamente como caixa-preta: o **Harness**.

**Dá um formato concreto a “harness” pela primeira vez.** A palavra havia
aparecido antes em dois sentidos diferentes:

- [[2026-08-19 1-1 Matheus - Luís]]: o desenho dos subagentes de desenvolvimento
  é *“100% harness de projeto”* — configuração do próprio projeto-alvo
  (`CLAUDE.md`, Skills, tooling), decidida por projeto, não algo que o diagrama
  dos 14 agentes precisa especificar.
- [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]: A12 (Data Gov) e
  A13 (Deduplication) são chamados de *“harness”* — uma camada de aplicação de
  regras/guardrails, mais próxima de um gate automatizado do que de algo que
  raciocina.

O template reconcilia os dois usos num conceito: **Harness = Soul.md + Skills +
Tools + MCP**, o substrato em que qualquer agente ou projeto roda,
independentemente de quem ou do que o dispara. O primeiro uso aplicava o
conceito ao projeto; a mensagem o generaliza para cada agente da arquitetura.

> [!tip] Leitura candidata de A12/A13 como “harness” — síntese de msilva, não confirmada com Luís
> Se Harness = Soul.md + Skills + Tools + MCP, chamar A12/A13 de “harness” em
> vez de agente pode não ser apenas linguagem imprecisa: talvez rodem
> principalmente sobre Skills + Tools, com pouca ou nenhuma Soul.md — sem uma
> camada autônoma de raciocínio/comportamento. Isso combina com a heurística
> **agir/informar** de msilva ([[2026-08-20 1-1 Matheus - Luís]]): algo que
> apenas aplica uma regra, em vez de agir ou raciocinar sobre ela, parece um
> componente Skill/Harness, não um agente independente. A leitura ainda não foi
> testada contra os 14 agentes nem confirmada com Luís.

**Substrato provável:** [[Claude Agent SDK]], apresentado por Luís em 2026-08-19
como forma de rodar Claude Code via API/CLI sem IDE — mecanismo pelo qual um
agente poderia invocar o harness programaticamente. Essa ligação ainda não foi
feita explicitamente por nenhum dos dois.

**Nem todo agente ganha um harness independente.** msilva (`[!msilva]` de
2026-08-21): alguns dos 14 agentes talvez não precisem receber este template;
podem ser chamados como Tool/Skill dentro do harness de um agente-pai, sem
Trigger/Input/Output próprios. Isso confirma o sinal de “talvez seja só uma
Skill” já presente no A4 Teacher e no grupo transversal: eles talvez não sejam
entradas de primeiro nível em [[Agent Flow]], e sim componentes aninhados na
camada Skills/Tools de outro agente.

**Trigger Channel convive com os quatro canais de entrada.** msilva
(`[!msilva]` de 2026-08-21): Trigger Channel (Slack, Webhook, Cron etc.) é um
eixo distinto dos quatro canais de entrada de [[Agent Flow]] (`Bug sistema` ·
`Bug manual` · `Tarefa` · `Consultoria`); ambos coexistem no modelo.

**Reforçado ao vivo, não só por escrito.** Em
[[2026-08-26 1-1 Matheus - Luís (agentes A10-A14)]], Luís retoma a distinção
trigger/input especificamente
sobre A10/A14. Não é um conceito isolado da mensagem de 2026-08-21, mas uma
forma consistente de pensar os agentes.

## Molde e instância

A conversa de msilva com Luís em 2026-09-01 acrescenta uma questão de produto:
além de usar este template para descrever um agente, deveria existir um **molde
reutilizável** a partir do qual novas instâncias de agentes possam ser criadas e
configuradas. O desenho inicial, ainda não decidido, está em
[[Como deve funcionar o molde de agente]].

O molde não substitui este template: ele transforma a anatomia conceitual acima
em um contrato configurável. A fronteira entre aquilo que pertence ao molde, à
instância, à Soul e ao runtime ainda precisa ser fechada.

**SOUL ganhou um desenho próprio em 2026-09-01.** A
[[2026-09-01 Modelar SOUL em tabelas com composição plana|decisão da SOUL]]
estabelece que ela é uma fonte comportamental
independente do `system_prompt` da tarefa, persistida em `soul_versions` e
composta de forma plana por `soul_composition`. O `Soul.md` deste template
continua representando uma camada conceitual do Harness; não implica que a
fonte de verdade precise ser um arquivo Markdown.

## Leituras externas relacionadas (ingeridas em 2026-08-24)

Duas clippings do mesmo lote (`raw/Clippings/`, capturadas em 2026-08-21) usam
“harness” de formas complementares a este template, não idênticas:

- [[Harness engineering for coding agent users]] (Böckeler/Martin Fowler)
  descreve o *ciclo de controle* — guias feedforward + sensores feedback,
  computacional vs. inferencial —, não a composição do harness. Sua categoria
  “behaviour harness”, o comportamento funcional do sistema, é o problema mais
  difícil e é onde A7→A8→A9 realmente vive.
- [[How to Build a Custom Agent Harness]] (Runkle/LangChain) descreve **como
  construir** um harness via middleware plugável. `HumanInTheLoopMiddleware` e
  `SubAgentMiddleware` são infraestrutura já nomeada para duas perguntas ainda
  abertas no desenho dos 14 agentes: onde ficam os gates humanos e como A3/A9
  cria subagentes sob demanda.

Nenhuma das duas resolve qual substrato roda o Harness de Luís — Claude Agent
SDK ou LangChain `create_agent`. Servem como vocabulário e comparação, não como
resposta.

**msilva, lendo
[[Inside the multiplayer AI setups at Mintlify, LangChain, and Buffer]]
(2026-09-25)**, nota a mesma distinção por conta própria: a
frase da LangChain *"we're not solving a repo of skills management
challenge, we're solving a production agent challenge"* aponta, na leitura
dele, pro problema do harness (onde o agente roda, como se comunica) como
categoria diferente de gerenciar skills — mesmo eixo desta pergunta em
aberto, não resposta nova.

## Um harness de codificação real, mapeado no eixo guias/sensores — 2026-10-20

[[Rasmic - The 4-Step Agentic Engineering Workflow]] descreve um harness de
engenharia em produção (`AGENTS.md` + quatro skills) que cai limpo no eixo
feedforward/feedback de [[Harness engineering for coding agent users]]:

| Passo | Skill | Guia ou sensor |
|---|---|---|
| Isolate | `new-feature` (git worktree) | Guia — restringe o espaço antes de agir |
| Build | `code-structure` (service layer) | Guia — prescreve a arquitetura em vez de deixar o agente inventar uma por feature |
| Prove | `evidence-driven-testing` / `before-and-after` | Sensor computacional — vídeo, screenshot ou número do antes e depois |
| Ship | `grep-loop` (Greptile) | Sensor inferencial — outro LLM julga o PR, com critério de parada numérico (5/5) e teto de iteração |

Por que interessa a este template: é a primeira fonte do vault que mostra um
harness completo **em que a composição e o ciclo de controle são a mesma
coisa**. O `AGENTS.md` dele não lista o que o harness tem (Soul.md, Skills,
Tools, MCP — a leitura do template de Luís); lista **em que ponto do fluxo
chamar qual skill**. As duas taxonomias param de ser só complementares: aqui a
ordem das peças *é* a especificação do agente.

Consequência pro desenho dos 14 agentes: o passo 4 é um caso concreto de gate
inferencial com critério de parada explícito — exatamente a peça que falta nos
gates de A12/A13, discutidos até aqui como gate determinístico. Mas o próprio
autor não confia nele sozinho: mantém review manual depois do 5/5, o que é o
*behaviour harness* de Böckeler reaparecendo como problema não resolvido, não
como peça pronta pra copiar.

## A arquitetura é peça do harness, não contexto do arquiteto — 2026-10-20

Três fontes independentes convergem num ponto que nenhuma delas chama assim:
**a arquitetura só regula o agente se estiver disponível como artefato que ele
lê, não como entendimento que o humano carrega.**

| Fonte | Como aparece |
|---|---|
| Luís, [[2026-10-20 Matheus - Luís (arquitetura dos agentes, ADR e tokens)]] | *"Como é que ele vai seguir a arquitetura que você, como arquiteto, quer que ele siga, se você não tá disponibilizando a arquitetura para ele?"* — diagnóstico de que a arquitetura do repo dos agentes vive no *brain* de msilva, não versionada |
| [[Rasmic - The 4-Step Agentic Engineering Workflow]] | A skill `code-structure` **é** exatamente isso: a arquitetura prescrita e nomeada dentro do harness, invocada no passo Build |
| [[Harness engineering for coding agent users]] | A categoria *architecture fitness* e o eixo **guias (feedforward)** — regular antes de agir, não corrigir depois |

A convergência é não planejada: Luís não leu o Rasmic, e o Rasmic chegou ao
vault no mesmo dia por outro caminho. O que ela acrescenta a este template é
que **Skills não é um slot neutro de capacidades** — é onde a arquitetura
precisa morar para ter efeito regulatório. Um harness com Soul.md, Tools e MCP
bem definidos, mas sem a arquitetura escrita em lugar nenhum, deixa a decisão
estrutural para o modelo a cada tarefa.

Luís vai além e torna a regra inegociável: a decisão vira **ADR**, e
*"a partir daquele momento, aquilo ali é sagrado e ele não pode fugir
daquilo"*. Onde a arquitetura de fato não serve, a saída é um puxadinho
marcado em ADR própria — nunca abandonar a ADR em silêncio. Isso é o
equivalente, no slot Skills, do critério de parada numérico que o passo Ship do
Rasmic dá ao slot de sensores.

**Não testado neste vault.** Nenhum dos agentes do [[Agent Flow]] tem hoje a
arquitetura como arquivo no repositório — o diagnóstico de 2026-10-20 é
justamente a ausência disso.

## Questões em aberto

- Quais dos 14 agentes ficam no primeiro nível, com Trigger/Input/Harness/Output
  próprios, e quais ficam aninhados como Tool/Skill no harness de outro agente?
  Para os aninhados, a qual harness pertencem? Ainda não discutido com Luís.
- A fronteira da Soul foi parcialmente fechada na
  [[2026-09-01 Modelar SOUL em tabelas com composição plana|decisão da SOUL]];
  continuam abertas as fronteiras completas
  entre molde, instância e runtime em [[Como deve funcionar o molde de agente]].
