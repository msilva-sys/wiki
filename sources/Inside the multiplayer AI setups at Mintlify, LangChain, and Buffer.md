---
type: source
status: active
updated: 2026-09-25
date: 2026-09-02
aliases: [mkt1 parte 2, buffer langchain mintlify, multiplayer ai case studies]
source: "raw/Fichamento da newsletter de mkt1 - segunda parte.md"
url: "https://newsletter.mkt1.co/p/multiplayer-ai-mintlify-langchain-buffer"
tags: [agents, skills, capabilities, agent-flow, claude]
---

# MKT1 — "Inside the multiplayer AI setups at Mintlify, LangChain, and Buffer"

Newsletter da MKT1, por **Emily Kramer**, publicada **2026-09-02**. Parte 2
de 3 — aplica o framework dos 4 Cs de [[Marketing teams are stuck in
single-player Claude mode. Here's how to go multiplayer]] a três empresas
reais (Buffer, LangChain, Mintlify). Lida e anotada por msilva em
2026-09-25 — a fonte crua é o fichamento dele (`raw/Fichamento da
newsletter de mkt1 - segunda parte.md`); o clip verbatim do artigo já
existe à parte em `raw/Clippings/`, usado aqui só como grounding.

## Cultura antes de solução

Caso Buffer: Simon Heaton descreve o time como pequeno, bootstrapped, sem
procurement pesado — *"high agency, high ownership [...] we can build and
ship and deprecate as needed."* O sistema multiplayer deles funciona
porque o processo se encaixa na cultura que já existia, não o contrário.

msilva: *"A livemode é meio assim."* E generaliza pra princípio de
design: *"Acho que o Multiplayer Claude e todo esse esforço de
compartilhamento de contexto, capacidades, etc, só faz sentido se
inserido na cultura da organização. Por exemplo, a cada pessoa na
livemode tem agência de fazer as coisas de sua maneira. A ideia é que
qualquer solução que cheguemos tenha isso em mente."*

## Distribuição de skills — pergunta em aberto

*"Skills reach everyone's Claude through a Github plugin: one push to the
shared GitHub repo and every team member's Claude picks up the update."*

msilva: *"Como conseguimos fazer isso? Uma action no repo triggaria o
update local pra cada colaborador?"* — sem resposta ainda; toca direto o
repo `livemode` que Carol está construindo (ver [[Packaging as skills]]).

## Context vive fora do Claude e do GitHub

Buffer mantém contexto em Notion — cada dono funcional codifica sua
estratégia em doc, todos a partir do mesmo template, e a skill relevante
lê o doc certo antes de rodar (ex.: a skill de relatório AEO lê o doc de
estratégia AEO primeiro).

msilva: *"Muito interessante. Cada ponto desses esbarra em ideias que já
tive e problemas que enfrentamos no dia a dia como time."* (reação geral,
sem especificar quais ideias/problemas.)

Do FAQ da parte 2: *"Context can and should live outside of Claude and
GitHub [...] Notion, Asana, Airtable, or Softr [...] that Claude reads via
MCP or CLI works great."*

msilva: *"Acho que ferramentas como notion são mais flexíveis do que
github."*

## Capacidades em dois baldes

*"Buffer categorizes their capabilities into 2 buckets: capabilities that
do the work and capabilities that keep the system up to date."*

msilva: *"Divisão em duas categorias facilita o entendimento e execução
dos processos. Um exemplo de capacidade que 'faz o trabalho' são as
skills de criação de skills, e o agente curador seria uma capacidade que
mantém o sistema atualizado."* — mapeamento direto pro Fluxo Agêntico;
reforça [[A6 Curador deve padronizar e sinalizar defasagem de skills]]
com uma segunda leitura independente chegando na mesma divisão.

*"I think about capabilities to maintain your system as covering 3 jobs:
building new skills the right way, catching fixes and duplicates as you
work, and checking the whole system on a schedule."*

msilva: *"Boa."*

## Harness é um problema mais amplo que gerenciar skills

LangChain, Danny Lambert: *"Our team is not trying to solve a repo of
skills management challenge. We're solving a production agent
challenge."*

msilva: *"Acho que aqui é mais amplo: a questão que eles atacam é mais do
harness, agente, onde irá rodar, como se comunicar, etc, do que um
gerenciamento de skills."* — distinção que toca a pergunta ainda aberta
em [[Agent Harness Template]] sobre qual substrato roda o harness do Luís
(Claude Agent SDK vs. LangChain `create_agent`).

## Preocupação real e não resolvida com o Fluxo Agêntico

*"You need an easy way for agents to reach your team's context, wherever
it lives."*

msilva: *"Isso é importantíssimo. É uma das preocupações que tenho com o
fluxo-agêntico. Atualmente eu entendo que está muito ligado ao dashboard
que desenvolvi. A ideia é termos mais flexibilidade."* — preocupação
nova, fanned out como open question em [[Agent Flow]].

## Por que importa pro Fluxo Agêntico

- **Restrição de design nova, ainda não confrontada com nenhum desenho
  existente**: qualquer solução de compartilhamento de contexto/skills
  precisa respeitar a agência individual do time, não impor um processo
  rígido — ver open question fanned out em [[Agent Flow]].
- **Confirma, por leitura independente, a divisão de A6** já registrada
  em [[A6 Curador deve padronizar e sinalizar defasagem de skills]] e em
  [[Agents read primary sources]] (retrieval/tool vs. curadoria/agente).
- **A distinção harness vs. skill management** é outro ângulo da mesma
  pergunta em aberto de [[Agent Harness Template]] sobre substrato
  (Claude Agent SDK vs. LangChain).
- **A preocupação de msilva sobre acoplamento ao dashboard** é nova,
  ainda sem página própria além do open question fanned out em
  [[Agent Flow]] — relacionada mas distinta do open question já existente
  ali sobre comunicação agente-a-agente.

## Open questions

- Como replicar mecanicamente "um push atualiza o Claude de todo mundo"
  (GitHub Action + trigger local)? Levantado por msilva, sem resposta no
  artigo.
- O que exatamente, nos "problemas que já enfrentamos como time", a
  seção de Context da Buffer evocou? msilva não especificou — vale
  perguntar diretamente se isso importar depois.
