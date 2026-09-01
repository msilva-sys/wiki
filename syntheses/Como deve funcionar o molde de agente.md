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
