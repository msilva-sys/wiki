---
type: source
status: stable
updated: 2026-11-13
date: 2026-08-07
aliases: [rovo skill vs tool, skill vs tool]
source: "raw/Clippings/What's the difference between a skill and tool  Rovo.md"
url: "https://support.atlassian.com/rovo/docs/whats-the-difference-between-a-skill-and-tool/"
tags: [agents, skills, tools, harness, vocabulary]
---

# Rovo — "What's the difference between a skill and tool?"

Página de suporte da Atlassian sobre agentes do Rovo (Studio), publicada
2026-08-07, clipada 2026-11-13. Documentação de produto: define termos, não
traz medição nem caso real. Peso baixo como evidência; útil como vocabulário
de um vendor grande.

## O que a fonte diz

| | Tool | Skill |
|---|---|---|
| Papel | o que o agente pode fazer | como o agente faz |
| Resultado | uma ação, com input e output definidos | instruções reutilizáveis que combinam tools com raciocínio |

- **Tool**: ação única e estreita (transicionar issue no Jira, mandar
  mensagem no Slack, editar página no Confluence). *"Precise, reliable, and
  narrow in their role."*
- **Skill**: diz ao agente como fazer bem um tipo de tarefa, com
  consistência e sem recomeçar do zero. Pode chamar uma tool só ou orquestrar
  várias com raciocínio entre elas.
- *"The agent orchestrates, the skill guides, and the tools execute."*

## O que acrescenta ao wiki

- **Skill declara as tools de que depende.** Se a tool falta, o Rovo a
  adiciona sozinho. Toca a pergunta aberta de [[Agent Harness Template]]
  sobre agentes aninhados como Tool/Skill dentro de outro: aninhar cria
  dependência que precisa ser declarada.
- **Transparência como argumento para skill.** Dá para ver qual skill roda e
  em que passo, o que facilita confiar e investigar. Argumento que
  [[Packaging as skills]] ainda não tinha (lá: custo, distribuição,
  arquitetura).
- **Confirma por fora a divisão calcular vs. julgar** já usada no
  [[Vocabulário do Fluxo Agêntico]] e nas decisões de 2026-09-10 sobre
  critérios do A10/A14 julgados por skill. Não muda nenhuma delas.

## O que não diz

- Nada sobre custo de tokens nem distribuição de skills entre pessoas: não
  entra como fonte da tese de [[Packaging as skills]].
- Nada sobre agente chamando agente como tool
  ([[2026-09-10 Agentes expostos como tool uns para os outros]]).
