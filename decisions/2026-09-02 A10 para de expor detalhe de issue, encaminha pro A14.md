---
type: decision
status: active
updated: 2026-09-02
date: 2026-09-02
aliases: [fronteira PM vs Portfolio, A10 não vê issue, A10 fala só de portfólio]
tags: [agents, agent-flow, a10, a14, langgraph, product-scope]
---

# A10 para de expor detalhe de issue — encaminha pro A14

## Resumo

Implementado e testado no mesmo dia do feedback: A10 não fala mais de issue
específica em nenhuma superfície — digest publicado, dashboard, chat. Quem
quer detalhe de uma issue (motivo do sinalizamento, status, o que mudou) é
direcionado pro A14. A10 continua lendo issues cruas internamente pra montar
o agregado (contagem por critério, tendência, concentração de carga por
projeto) — a regra é sobre o que ele **expõe**, não sobre o que ele **lê**.

## Origem

[[2026-09-02 1-1 Matheus - Luís]]: Luís deu feedback sobre o dashboard de
portfólio (18 itens, critérios: item estagnado, gargalo de capacidade,
priorização desalinhada, escopo descontrolado) — *"portfólio não deveria
descer a nível de tarefa"*. O exemplo concreto que virou regra de
implementação (relatado por msilva em chat ao decidir isso, não estava
literal na nota do Granola que virou aquela página): *"o A10 olha o
portfólio como um todo e se alguém quiser saber algo de determinada issue de
um projeto, precisa falar com o A14."*

## O que mudou (mesmo dia, commit `aa8d89a`, branch `langgraph`)

- **Digest publicado no Linear** (`a10/formatting.py::format_portfolio_digest`):
  parou de listar issue por issue com link/reasoning; agora conta por
  critério, ordenado por frequência, com contagem de recorrência — sem citar
  qual issue.
- **API `/a10/run`**: passa a devolver `A10PublicOutput` (`summary`,
  `portfolio_health`, `suggestion_count`) em vez do `A10Output` completo —
  `suggestions` (issue-a-issue) não sai mais do backend pro frontend.
- **Dashboard** (`frontend/src/pages/a10-page.tsx`): tabela de issues
  removida — era literalmente o "dashboard de 18 itens" que o Luís citou na
  reunião.
- **Chat** (`a10/agent.py::chat_a10`): tools escopados por issue
  (`list_issues`, tools de repo por `issue_identifier`) saem do toolset de
  chat — fica só `calculate_assignee_workload` (agregado); o cache injetado
  no prompt não carrega mais `suggestions` cru (`exclude={"suggestions"}`); o
  prompt (Langfuse, prompt `a10-portfolio`, label `development`) redireciona
  pergunta de issue específica pro A14 — testado ao vivo no dashboard,
  resposta: *"Isso é detalhe por issue — fala com o A14, que tem essa
  visão."* Pergunta agregada legítima ("qual projeto tem mais risco")
  continua respondida normalmente.
- **A análise interna não mudou**: `list_issues()`,
  `calculate_assignee_workload()`, o loop do LLM produzindo sugestão por
  issue — tudo continua rodando exatamente igual, só virou insumo interno
  pro agregado e pra memória de recorrência/cooldown, nunca mais o produto
  exposto.

## Nuance em aberto — não é a resposta final da fronteira PM vs. Portfolio

msilva ainda tinha pedido a Luís, na mesma reunião, que trouxesse fontes e
argumentos (inclusive uma resposta de IA "crua," sem viés) pra embasar a
fronteira PM vs. Portfolio antes de fechar a **direção de produto** do
A10/A14 — isso ainda não foi recebido. O que foi implementado aqui é a
**regra de interação mais estreita e concreta** que Luís já tinha dado como
exemplo (A10 nunca fala de issue específica), não necessariamente a versão
final e mais ampla da fronteira. Se a análise mais ampla de Luís chegar e
apontar pra outra direção, essa implementação pode precisar de revisão.

## Trade-off e conexão com decisão anterior

[[2026-08-28 Remover list_issues do toolset de chat do A10]] já tinha tirado
`list_issues` do chat por **custo** (evitar re-fetch caro do backlog
inteiro). Esta decisão reaproveita e estende o mesmo parâmetro (renomeado de
`include_list_issues` pra `include_issue_detail`) — agora cobrindo também os
tools de repo por issue, por um motivo diferente (**escopo de produto**, não
custo). Efeito colateral bom: reduz ainda mais a superfície de tool no chat.

**Ativação em produção pendente**: o prompt novo só está publicado no
Langfuse com label `development`; promover pra `production` é passo manual
separado, ainda não feito.

## Ligação com o resto do projeto

Fecha (parcialmente — ver nuance acima) a questão registrada em
[[Agent Flow]] sobre onde fica a fronteira PM vs. Portfolio.
