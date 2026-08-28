---
type: decision
status: active
updated: 2026-08-28
date: 2026-08-28
aliases: [custo do chat_a10, list_issues cost bug, trace caro do A10]
tags: [agents, agent-flow, a10, langgraph, cost, tokens, langfuse]
---

# Remover `list_issues` do toolset de chat do A10 — custo de re-fetch do backlog inteiro

Achado investigando por que um trace do Langfuse custou caro demais,
puxando o fio a partir de `queria saber se já investigamos pq o preço
dessa operação está alto`, sem apontar qual operação — a investigação
identificou o caso sozinha via API do Langfuse.

## O que os traces mostraram

Dois traces muito fora da curva num dia de teste (~$0,001–0,004 é o
normal): **`ae00d787...` — $1,62** e **`6bf25b44...` — $1,57**. Comparados
via `GET /api/public/observations?traceId=...` do Langfuse:

- `ae00d787...`: sem `sessionId`, prompt fixo "Analise o backlog e gere
  sugestões." → é `run_a10` de verdade (foi essa execução que gravou
  `agent_cache` às 16:04:04).
- `6bf25b44...`: `sessionId = "a10:...:teste-1"`, pergunta "Quantas issues
  estagnadas você viu?" → é `chat_a10`, testado manualmente 26 min depois.

**A causa raiz é a mesma nos dois**: a tool `list_issues()` (`a10/tools.py`)
devolve o backlog inteiro do time (~404 issues, com `description`) como um
único resultado de tool call — **~1MB de texto, ~281 mil tokens**. A
primeira chamada ao `gpt-5.4` depois desse tool result paga o preço cheio
desses 281 mil tokens de entrada (não tem como ter cache — é a primeira vez
que esse prefixo existe): **$1,42 só nessa geração**, a maior parte do
custo do trace inteiro.

O que **não** é o problema: o loop determinístico que roda depois
(`calculate_days_since_update` várias vezes) já se beneficia do prompt
caching automático da OpenAI — uma geração de 282 mil tokens de entrada
custou só $0,15 porque 281 mil vieram de `input_cache_read`. Isso já
funciona sem precisar de mudança de código.

## O problema específico do chat

`run_a10` só paga esse custo quando o cache do app (`cache.py`,
`AGENT_CACHE_TTL_SECONDS=86400`) está frio — no máximo uma vez por dia,
por design. `chat_a10`, por natureza, **sempre** chama o LLM (é uma
conversa), e o código adicionado no mesmo dia (b319b4b) já injeta a última
análise cacheada no prompt como contexto — mas o toolset do agente de chat
ainda incluía `list_issues`, e o modelo decidiu chamar de novo o tool caro
em vez de confiar no resumo já injetado. Ou seja: **toda pergunta no chat
corria o risco de repetir o custo de $1,50+**, não só a primeira execução
do dia.

## Decisão

`a10/tools.py`: `create_tools(team_id, *, include_list_issues=True)` —
`run_a10` continua recebendo as 3 tools; `chat_a10` passa
`include_list_issues=False`, ficando só com `calculate_days_since_update`
e `calculate_assignee_workload` (ambas devolvem um número/dict pequeno pro
LLM, mesmo buscando o backlog inteiro internamente via
`linear_client.list_issues` — o que importa pro custo é o tamanho do que
*volta pro LLM*, não o que a tool lê por baixo).

## Trade-off aceito

O chat perde acesso a dado bruto do backlog — só responde com base no
resumo cacheado injetado + os dois cálculos pontuais. Uma pergunta fora
desse escopo (ex. "quais issues o João tem atribuídas com label X?") não
tem mais como ser respondida ao vivo. Aceito para esta fase: se o chat
precisar explorar o backlog ao vivo no futuro, o caminho é um tool novo e
deliberadamente enxuto (filtra/agrega em Python, devolve pouco pro LLM),
não reintroduzir `list_issues` cru no chat.

## Ligação com o resto do projeto

Instância concreta do lever de custo já nomeado em [[Agent Flow]]
("**narrow fetching** — a design decision per agent") e do mesmo padrão
genérico do incidente de $7/dia da CazéTV (mesma página, seção
"Constraints that surfaced later") — nesse caso auto-diagnosticado,
$ por trace, e corrigido no mesmo dia via traces do Langfuse (que já
estava instrumentado desde o desenho da PoC, ver
[[Desenho do agente LangGraph para A10+A14]]).
