---
type: synthesis
status: active
updated: 2026-09-01
date: 2026-09-01
aliases: [molde de agente, agent blueprint, agent template configurável]
tags: [agent-flow, agents, harness, soul, configuration]
---

# Como deve funcionar o molde de agente

## Origem da questão

msilva relatou em 2026-09-01 dois pontos levantados numa conversa com
[[Luís Fernandez]]:

1. permitir adicionar ou editar o comportamento dos agentes de forma não
   programática — com um dashboard semelhante ao Langfuse no qual seja possível
   configurar atributos da Soul;
2. ter uma forma de **molde de agente**.

Esta página registra somente a segunda questão. A Soul é a próxima investigação
e não está definida aqui por antecipação.

## Relação com o modelo existente

[[Agent Harness Template]] já descreve a anatomia genérica proposta por Luís:
Trigger + Trigger Channel → Input → Harness (`Soul.md`, Skills, Tools, MCP) →
Output. O molde parece ser a transformação dessa anatomia em um **contrato
reutilizável e instanciável**, não um segundo modelo concorrente.

A distinção inicial é:

- **molde** — define quais partes existem, quais campos são obrigatórios, quais
  variações são permitidas e quais invariantes toda instância deve preservar;
- **instância** — escolhe valores concretos para um agente, como objetivo,
  entradas, saídas, Soul e capabilities;
- **runtime** — executa a instância e conecta adapters, Tools, MCPs, memória e
  observabilidade.

Essa separação é uma hipótese de trabalho, não uma decisão tomada com Luís.

## Hipótese inicial de estrutura

Um molde poderia declarar, no mínimo:

```yaml
kind:
purpose:
non_goals:
trigger:
input_schema:
output_schema:
soul_schema:
capabilities:
permissions:
human_gates:
memory_policy:
success_criteria:
evaluation_dataset:
```

O ponto central é que o molde não contém apenas um prompt-base. Ele contém o
**contrato de produto e operação** do agente: o que o dispara, o que aceita, o
que entrega, o que pode fazer e como sua qualidade será verificada.

## Um molde universal ou uma família de moldes

Uma base comum pode coexistir com arquétipos mais específicos, por exemplo:

- leitor/analista;
- consultor;
- operador com permissão de escrita;
- monitor disparado por eventos;
- orquestrador.

Isso evita dois extremos ainda não avaliados: um molde universal tão genérico
que não impõe contrato nenhum, ou um molde diferente para cada agente que não
gera reaproveitamento real. [[Harness engineering for coding agent users]] já
registra ideia próxima — bundles de guias e sensores por topologia de serviço —,
mas não valida que esses sejam os arquétipos corretos para [[Agent Flow]].

## Restrição importante

O molde não deve transformar automaticamente toda capacidade em agente
independente. [[Agent Harness Template]] registra que parte dos 14 candidatos
pode existir como Skill ou Tool dentro do harness de outro agente. Portanto, o
primeiro campo conceitual talvez seja a própria natureza do componente:
`standalone_agent`, `skill`, `tool` ou `guardrail`.

## Relação com configuração não programática

O molde fornece o schema que torna um dashboard possível: a interface sabe
quais campos expor, quais valores validar e qual configuração materializar para
uma instância. Sem molde, “editar o agente sem código” tende a virar apenas um
editor de prompt livre; com molde, pode virar configuração estruturada,
versionada e testável.

[[Desenho do agente LangGraph para A10+A14]] já adotou Langfuse Prompt
Management para versionar prompts, promover versões por label e ligar cada
trace à versão executada. A
[[2026-09-01 Modelar SOUL em tabelas com composição plana|decisão da SOUL]]
respondeu a fronteira: a SOUL é fonte independente, persistida em
tabelas próprias; o Langfuse pode receber a SOUL efetiva e seus identificadores
para observabilidade, mas não é sua fonte de verdade.

## Decisão V1 da SOUL — 2026-09-01

msilva escolheu um modelo simples e composable: `soul_versions` guarda um bloco
de texto direto por versão e `soul_composition` é a join table ordenada entre um
profile e seus fragments. A composição é plana, sem YAML como fonte de verdade,
sem campos comportamentais aninhados e sem propagação automática de mudanças.
Ver [[2026-09-01 Modelar SOUL em tabelas com composição plana]] para o schema e
as regras.

## Paralelo com a camada de ontologia da Bossabox — 2026-09-01

msilva perguntou, em chat, como replicar a lógica de "camada de ontologia" a
que a [[Bossabox Engagement]] chegou com a OS V2: uma camada de sustentação
tool-agnostic — um vocabulário comum ("sistema de inteligência") por baixo de
qualquer ferramenta que o time já use (Jira, GitHub, Linear, Notion), em vez de
travar a lógica dentro de uma delas. Cruzando essa pergunta com o que já existe
nesta página e em páginas vizinhas:

**Peças que já são essa lógica, em miniatura:**

- **Contrato / ports-and-adapters** ([[Vocabulário do Fluxo Agêntico]],
  [[Desenho do agente LangGraph para A10+A14]]) — regra central já registrada:
  *"Contrato: definido por formato, nunca por quem chama."* O `team_id` foi
  tirado de dentro do agente e virou campo de `Entrada` justamente para o
  agente não precisar saber qual ferramenta o alimenta. Hoje existe só para o
  Linear.
- **Agent Harness Template** ([[Agent Harness Template]]) — Trigger/Trigger
  Channel (Slack, Webhook, Cron) separado do Input generaliza o mesmo
  princípio para qualquer canal.
- **O próprio molde** (esta página) é o candidato mais direto: um contrato
  reutilizável e instanciável, independente da implementação — a mesma
  ambição da ontologia da Bossabox, hoje ainda com escopo mais estreito
  (comportamento/contrato do agente, não vocabulário de domínio entre
  sistemas de registro).

**Duas lacunas concretas que faltam para virar de fato uma camada de
ontologia, não só contratos de agente:**

1. **O mapeamento DTO≠domínio só existe para o Linear.** `linear_client.py`
   já separa o shape cru do GraphQL de um modelo de domínio estável
   (`Issue`, `Project`, `Milestone`). A Bossabox generaliza exatamente esse
   padrão para Jira/GitHub/Confluence/Notion ao mesmo tempo. O GitHub já foi
   integrado ([[2026-08-31 A10 e A14 ganham acesso real ao GitHub]]), mas via
   `repo_tools.py` separado — não está claro se compartilha vocabulário de
   domínio com o Linear ou se é um segundo silo.
2. **"Sinal" e "memória do sistema agêntico" não têm dono** — já registrado
   como lacuna aberta em [[Vocabulário do Fluxo Agêntico]] e em
   [[Agent Flow]] desde 2026-08-20 (Luís: organizar essa memória bem é
   "talvez a coisa mais importante do projeto todo"). É exatamente a peça que
   a camada de sustentação da Bossabox resolve — contexto que sobrevive entre
   ferramentas e entre agentes. Hoje a memória que existe (`a14/memoria.py`)
   é por agente e por projeto, o oposto de uma camada compartilhada.

**Caminho prático de replicar, ainda não decidido com Luís:**

- Quando o molde for fechado, garantir que ele carregue não só o contrato
  comportamental do agente, mas também um vocabulário de domínio
  compartilhado — hoje não está claro se essas são a mesma peça ou duas
  questões distintas sendo tratadas como uma.
- Estender o padrão DTO-vs-domínio do `linear_client.py` para os demais
  sistemas de registro conforme forem entrando.
- Dar dono explícito à "memória do sistema agêntico" — a lacuna que mais se
  parece com o que falta para replicar a lógica da Bossabox, mais do que os
  contratos por agente (que já andam bem).

Ver também o ponto 7 de [[What Bossabox's Assessment suggests for Agent
Flow]]: ainda em aberto se compensa usar o tier gratuito da Bossabox para
validar/acelerar essa camada em vez de reconstruí-la do zero.

## Questões em aberto

- Qual problema concreto o molde precisa resolver primeiro: criação de novos
  agentes, padronização dos existentes, delegação da configuração para pessoas
  não técnicas ou comparação entre implementações?
- Existe um molde-base único com módulos opcionais ou uma família de arquétipos?
- Quais campos pertencem ao molde e quais só aparecem na instância?
- Quais mudanças podem ser feitas sem código, e quais exigem revisão técnica?
- Como versões de molde e de instância se relacionam quando o molde evolui?
- Quais invariantes não podem ser sobrescritas por uma instância?
- Como o molde representa componentes que não são agentes independentes?
- ~~O que exatamente compõe a Soul e como seus atributos viram comportamento
  observável?~~ **Parcialmente respondida em 2026-09-01:** o V1 abandona
  atributos estruturados em favor de texto direto, composição plana e versões
  rastreáveis; o conteúdo real dos fragments ainda precisa ser definido.
- **Novo, 2026-09-01**: o molde deve incluir vocabulário de domínio
  compartilhado entre sistemas de registro (Linear, GitHub, futuros), ou essa
  é uma camada separada da ontologia do agente em si? Ver seção acima.
