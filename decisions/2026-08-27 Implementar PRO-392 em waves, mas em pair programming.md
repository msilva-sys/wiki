---
type: decision
status: active
updated: 2026-08-27
date: 2026-08-27
aliases: [waves, packer waves, implementação em waves, pair programming A10/A14]
tags: [agents, agent-flow, a10, a14, langgraph, linear, orchestration]
---

# Implementar PRO-392 em waves, mas por pair programming — não agente sozinho

**Correção da mesma sessão**: a primeira versão desta página descrevia o
modelo de [[Gabriel Packer - DAG-driven agent orchestration]] ao pé da
letra — agente de implementação escrevendo sozinho por ticket, msilva só
como quality gate no merge do PR. msilva corrigiu na hora: *"eu não
quero construir sozinho / iremos construir juntos por pair programming
/ eu queria era estudar antes as 3 soluções"*. Ou seja: nem "msilva
escreve tudo sozinho" (a leitura original do `HANDOFF.md`), nem "agente
escreve tudo sozinho" (a primeira leitura desta decisão) — **pair
programming**, os dois construindo junto em tempo real. E antes de
qualquer wave de implementação, o objetivo real é **estudar as três
frentes** (LangGraph, Skill do Claude Code, Agent SDK) comparativamente,
não só entregar `PRO-392`.

Isso **substitui, só para esta frente de execução**, a instrução gravada
em `HANDOFF.md`: *"msilva quer estudar e construir isso ele mesmo, sem
escrever código por ele"*. [[I'll study X = self-study]] continua
valendo como preferência padrão — esta página é o registro de que, para
`PRO-392` especificamente, "estudar" virou "estudar construindo em
dupla", não "estudar sozinho lendo o que Claude escreve".

## O que fica do modelo do Packer, e o que não fica

O que **fica**: a estrutura de waves vinda do DAG nativo das sub-issues
(`blockedBy`) — ela organiza a ordem/paralelismo do trabalho
independente de quem digita o código.

| Wave | Issues | Blocker |
|---|---|---|
| 1 | `PRO-475` — configurar projeto | nenhum — **feita** 2026-08-27 |
| 2 | `PRO-476` — cliente do Linear | `PRO-475` |
| 3 | `PRO-477` (A10) + `PRO-478` (A14) | `PRO-476` — paralelas entre si |
| 4 | `PRO-479` — orquestração + HTML | `PRO-477` e `PRO-478` |

O que **não fica**: o "agente sozinho por ticket, worktree isolado,
humano só no PR" — isso presume um orquestrador que despacha implementação
autônoma, e é exatamente o modo que msilva rejeitou. Sem worktrees
paralelos por agente autônomo; sessão de pair programming direta no
branch `langgraph`, uma issue de cada vez (mesmo as paralelas da wave 3
— "paralelo" aqui é sobre dependência, não sobre execução simultânea por
múltiplos agentes).

## Resolvido — ordem real do trabalho

msilva escolheu seguir direto na wave 2 (`PRO-476`) em pair programming,
sem pausar antes pra estudar as 3 frentes comparativamente. A pergunta
"estudar antes de construir" continua em pé como preferência de fundo,
só não bloqueia o início desta wave.

## Reconfirmado — sem sub-agent nem na wave 3

msilva perguntou explicitamente se, seguindo "waves", um sub-agent podia
implementar sozinho as issues que não se bloqueiam entre si (`PRO-477` +
`PRO-478`, únicas paralelas de verdade no DAG). Opções levantadas: (a)
pair programming numa, sub-agent sozinho na outra; (b) sub-agent nas
duas, msilva só revisa os PRs; (c) só planejar a mecânica antes de
decidir. **Escolhido**: nenhuma das três — pair programming também na
wave 3, sem paralelismo de execução por sub-agents por enquanto. Reforça
o que a seção acima já previa: "paralelo" nas waves é sobre a ordem que
o DAG permite, não sobre múltiplos agentes escrevendo código ao mesmo
tempo.
