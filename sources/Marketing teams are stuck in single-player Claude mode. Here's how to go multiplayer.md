---
type: source
status: active
updated: 2026-09-25
date: 2026-07-27
aliases: [multiplayer claude, 4 cs framework, mkt1 parte 1, single-player claude mode]
source: "raw/Fichamento da newsletter de mkt1 - primeira parte.md"
url: "https://newsletter.mkt1.co/p/multiplayer-claude-1"
tags: [agents, skills, capabilities, agent-flow, claude]
---

# MKT1 — "Marketing teams are stuck in single-player Claude mode..."

Newsletter da MKT1, por **Emily Kramer**, publicada **2026-07-27**. Parte 1
de uma série de 3 sobre "multiplayer AI" em times de marketing. Lida e
anotada por msilva em 2026-09-25 — a fonte crua é o fichamento dele
(`raw/Fichamento da newsletter de mkt1 - primeira parte.md`), não um clip
verbatim do artigo. A **Parte 2** ([[Inside the multiplayer AI setups at
Mintlify, LangChain, and Buffer]] — ainda não ingerida) aplica esse mesmo
framework a três empresas reais.

## A tese central

Times de marketing usam Claude no "modo single-player": um indivíduo fica
muito mais eficiente, mas isso não se propaga pro time. *"The big
productivity leap will come from turning what your most AI-fluent
teammates build and learn into a system the whole team runs on."*

msilva, lendo: bate direto com a própria experiência — tem uma wiki pessoal
com informação que agregaria se fosse compartilhada com o time, hoje não é.
Reação mais forte à mesma ideia: *"acho que é um desperdício de
oportunidade nós não estarmos avançando juntos com nossas IA's
integradas."*

David Johnson-Igra (Scribes), citado no artigo, sobre por que contexto
estático falha: *"our voices aren't stagnant. They evolve."* msilva,
lendo: *"as fontes acabam ficando defasadas rápido e em um contexto de
time, isso é terrível."*

## Framework: os 4 Cs de um sistema Claude multiplayer

| C | Definição | Detalhe |
|---|---|---|
| **Claude** | O modelo + o app onde ele roda | Tipicamente Claude Code ou Cowork, mas pode ser qualquer plataforma agêntica |
| **Context** | A informação que você alimenta | De docs, tools e sessões passadas, puxada pra uma fonte de verdade viva única |
| **Capabilities** | Os trabalhos que ele faz por você | Skills, agents, workflows, MCPs e repos que fazem Claude produzir |
| **Collaboration** | O sistema do time | Processos e hábitos que fazem todo mundo usar, aprender e melhorar o sistema junto |

## Capacidades

msilva, lendo: *"seriam as coisas que construímos uma vez pra não nos
repetirmos. o AGENTS.md é isso."*

## O que conta como "capability" — taxonomia da MKT1

| Capability | Definição | Exemplo |
|---|---|---|
| **Skill** | Instrução salva que Claude puxa e aplica com raciocínio pra fazer uma tarefa | Skill de copidesque que confere erros e voz de marca |
| **Agent** | "Programa" de IA que trabalha autônomo em direção a um objetivo pré-definido | Agente deployado em LangChain que roda uma motion inteira de outbound |
| **Sub-agent** | Agente que outro agente chama pra resolver um pedaço de um trabalho maior | Sub-agente que verifica dado enquanto a sessão principal segue redigindo |
| **Workflow** | Receita fixa numa ferramenta (Zapier, Make, n8n) que segue passo a passo | Workflow no Zapier que enriquece lead novo e posta no Slack |
| **MCPs & APIs** | Conexão entre Claude e outra ferramenta | MCP de website builder que deixa Claude atualizar o site |
| **Plugin** | Pacote de skills+conectores+comandos instalado de uma vez | Plugin de time instalado por todo mundo a partir de um repo GitHub compartilhado |

## Collaboration: o C esquecido

msilva, lendo: *"o esforço de colaboração é o mais importante. É o que
norteia o sistema multiplayer."*

## As 12 perguntas pra montar um sistema multiplayer

Agrupadas em 5 blocos — **Storage** (onde contexto/capabilities moram, onde
o time constrói), **Ownership** (quem é dono do sistema, quem atualiza
contexto, quem aprova ferramenta), **Adoption** (como o time usa o que os
outros constroem, como o melhor material sobe pro repo compartilhado),
**Upkeep** (como automatizar manutenção, como pegar correção/ideia nova de
sessões individuais), **Analysis** (como saber se o sistema funciona vs. se
é só um problema de adoção).

## Skills que mantêm o sistema (a resposta da MKT1 pro problema de manutenção)

Pipeline em 3 camadas:
- **Primary** (Build → Review → Publish): cria a skill com a estrutura certa,
  testa com evals, confirma e publica pro time.
- **Helper** (Dupe Check ↔ Update): Dupe Check confere se a skill já existe
  em algum lugar (local, repo do time, MCPs) antes de criar outra; Update
  varre a sessão, sugere atualização e empurra pra skill certa.
- **Audit** (Maintain → Repo Stats): Maintain roda varredura agendada,
  local ou no repo do time; Repo Stats lê o histórico do git do repo,
  sinaliza skill ativa vs. defasada (por edição, não por uso).

**msilva, lendo**: achou o conceito interessante — padroniza criação de
skill e já direciona pra uma base comum. Mas levantou a pergunta que o
artigo não responde: **como a skill de manutenção (Update) teria o
contexto da skill que está mantendo?** O diagrama diz que ela "varre sua
sessão, sugere atualizações", mas não explica o mecanismo — fica em aberto.

## Por que importa pro Fluxo Agêntico

- **A taxonomia de capability (skill/agent/sub-agent/workflow/MCP/plugin)
  é mais fina que o [[Vocabulário do Fluxo Agêntico]] atual**, que define
  *workflow* vs. *agent* mas não usa "sub-agent" nem "plugin" como termos
  próprios — candidato a emprestar vocabulário, não decisão tomada aqui.
- **A pergunta de manutenção que msilva levantou é exatamente a lacuna já
  registrada em [[Packaging as skills]]** ("o que packaging não resolve").
  O pipeline Build/Review/Publish + Dupe Check/Update + Maintain/Repo Stats
  é um candidato externo a resposta — mas não testado, e o próprio artigo
  não detalha o mecanismo de como a skill de manutenção enxerga o conteúdo
  da skill mantida. Registrado como pista, não como solução.
- **O repo `livemode` que Carol está construindo** (ver
  [[Packaging as skills]]) é exatamente o padrão "capabilities num único
  lugar compartilhado, GitHub repo + plugin" da taxonomia acima (linha
  Plugin). *Nota de correção*: a versão anterior desta página citava o
  caso Buffer como "modelo funcional" desse padrão — Buffer é exemplo da
  **Parte 2** da série (não ingerida ainda), não desta Parte 1. Tirado
  daqui; a comparação com Buffer cabe na página da Parte 2 quando ela for
  ingerida.
- **Ponto pessoal de msilva, não resolvido aqui**: esta própria wiki é hoje
  um "Context" C single-player — pensada pra uso dele, não pra o time (ver
  Audience em `CLAUDE.md`). O artigo não muda essa decisão, só deixa a
  tensão mais nítida; fica registrado como coisa a repensar, não como
  synthesis aberta por enquanto.

## Open questions

- Como uma skill de manutenção teria acesso ao contexto da skill que
  mantém, sem reprocessar tudo do zero? Pergunta de msilva, sem resposta
  no artigo.
- O framework dos 12 questions nunca foi rodado contra o setup real da
  Livemode (Fluxo Agêntico, repo da Carol) — só lido, não aplicado.
