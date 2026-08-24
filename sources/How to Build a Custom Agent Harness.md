---
type: source
status: active
updated: 2026-08-24
date: 2026-06-03
source: "raw/Clippings/How to Build a Custom Agent Harness.md"
url: "https://www.langchain.com/blog/how-to-build-a-custom-agent-harness"
aliases: [create_agent, langchain middleware, task-harness fit]
tags: [agents, harness, langchain, prior-art]
---

# Sydney Runkle — "How to Build a Custom Agent Harness"

Publicado em **2026-06-03** no blog da LangChain.

## O que descreve

Um guia prático (não conceitual, ao contrário do artigo da Böckeler — ver
[[Harness engineering for coding agent users]]) de como construir harness com
o `create_agent` da LangChain — uma primitiva minimalista (modelo + tools +
system prompt) que expõe **middleware** como o mecanismo de customização em
cada ponto do loop do agente (antes/depois de chamar o modelo, antes/depois
de chamar uma tool, no início/fim da execução).

Middleware cobre quatro alavancas: **lógica determinística** (regras de
negócio, troca de modelo por complexidade da tarefa), **tools** (ciclo de
vida completo — setup/teardown/registro, não só registrar diretamente no
agente), **estado customizado** (contadores, flags persistentes entre hooks)
e **stream handlers** (interceptar/transformar a saída para consumidores
diferentes — UI, log de auditoria, monitoramento).

Tabela de capacidades → middleware inclui: prevenir overflow de contexto
(`SummarizationMiddleware`), memória (`FilesystemMiddleware`,
`SkillsMiddleware`), delegação a sub-agentes (`SubAgentMiddleware`,
`TodoListMiddleware`), retry/fallback, gates de aprovação humana
(`HumanInTheLoopMiddleware`), e controle de custo
(`PromptCachingMiddleware`, limites de chamada).

Cunha **"task-harness fit"**: o harness certo depende inteiramente da
tarefa — um agente de atendimento e um agente de código de longa duração
precisam de harnesses muito diferentes.

## Por que interessa ao [[Agent Flow]]

- **`HumanInTheLoopMiddleware` é a peça de infraestrutura mais direta para
  "onde ficam os gates humanos"** — pergunta em aberto no próprio
  [[Agent Flow]] ("Where do the human gates sit?"). O artigo descreve isso
  como algo que já existe como middleware plugável, não como algo a desenhar
  do zero: pausar antes de uma ação consequente e esperar aprovar/rejeitar/
  redirecionar.
- **`SubAgentMiddleware`/`AsyncSubAgentMiddleware` mapeiam diretamente no
  "A3/A9 criam sub-agentes sob demanda"** do desenho de 14 agentes — é a
  mesma ideia (delegar sub-tarefas com contexto limpo) já existindo como
  componente nomeado, reusável, em vez de mecanismo a inventar.
- **Task-harness fit é outro ângulo do mesmo teste que
  [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] e a
  leitura direta de [[How we built our multi-agent research system]]** já
  aplicam ao [[Fluxo Agêntico diagram]]: será que cada agente da arquitetura
  realmente precisa do harness (e do grau de autonomia) que o desenho propõe,
  ou está sendo especificado de forma genérica demais? O artigo não entra
  nesse debate diretamente (é um guia de produto LangChain, não uma
  crítica), mas o vocabulário de "fit" é reaproveitável na avaliação.
- **Não resolve a pergunta do próprio [[Agent Harness Template]]** sobre qual
  substrato roda o harness de Luís — este artigo assume LangChain/
  `create_agent` como o substrato, enquanto o time já está olhando para o
  [[Claude Agent SDK]] como alternativa; útil como ponto de comparação, não
  como resposta.

## Não lido em profundidade

A tabela completa de middlewares pré-construídos (link
`docs.langchain.com/oss/python/langchain/middleware/built-in`) não foi
explorada — fora do escopo desta ingestão.
