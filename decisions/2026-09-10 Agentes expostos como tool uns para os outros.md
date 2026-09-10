---
type: decision
status: active
updated: 2026-09-10
date: 2026-09-10
aliases: [ask_agent, agent-as-tool, chamada cross-agent, agentes se chamando]
tags: [agent-flow, agents, harness, tool-calling, orchestration]
---

# Agentes expostos como tool uns para os outros

## Origem

msilva, 2026-09-10, mesma sessão de código em `livemode-fluxo-agentico`
(branch `langgraph`) que fechou [[2026-09-10 Memória de fatos do agente (agent_facts)]].
Puxado a partir de uma pergunta concreta sobre `outcomes.py`: hoje A10 lê o
outcome que A14 escreveu num store compartilhado; a pergunta era se A10
deveria em vez disso **chamar A14 diretamente**. Generalizou pra um
princípio maior: agentes deveriam poder se comunicar quando necessário pra
alcançar o objetivo, não só trocar fatos por um store passivo.

## Decisão

Duas coisas que a pergunta original misturava, separadas:

**Fatos compartilhados (`outcomes.py`, `agent_facts`) continuam como
store passivo, sem mudança** — é observação (A escreve, B lê quando
quiser), não invocação de capacidade. Nenhuma razão pra virar chamada
síncrona.

**Chamada de capacidade** é outra coisa: um agente, no meio do próprio
raciocínio, precisa de uma resposta que só outro agente sabe dar — não um
fato já publicado, uma pergunta nova. Decidido: **sim, por padrão**, todo
agente ganha a capacidade de chamar qualquer outro através do contrato
público dele (`Input → Output`, o mesmo port que `main.py`/`*_api.py` já
usam) — não por padrão restrito a um par específico (A10↔A14) nem
gateado por critério/contexto (cogitado e descartado: reduziria a
superfície mas o msilva prefere disponível por padrão).

Isso não quebra "cada agente define a própria interface" — quebraria se um
agente importasse o pacote interno do outro. Chamar pelo contrato público é
o mesmo tipo de acesso que qualquer consumidor externo já tem.

## Primitivo, não tool por par

Em vez de um tool bespoke por par de agente (`ask_a14` dentro do A10,
`ask_a10` dentro do A14, e N² tools conforme os 14 agentes existirem), um
primitivo genérico do próprio molde:

```
ask_agent(agent_key: str, question: str) -> <output do agente-alvo>
```

Resolve `agent_key` pro contrato registrado daquele agente e invoca. Todo
agente ganha a mesma capability de graça; a pilha de chamada e o limite de
profundidade (abaixo) vivem nesse primitivo uma vez, não replicados por
tool.

## Guardrails obrigatórios — não são simplificação opcional

Viraram não-negociáveis exatamente porque a exposição é por padrão, não uma
exceção pontual gateada:

- **Pilha de chamada, no host** — `recursion_limit` de hoje protege o loop
  de tool-calling *dentro* de um agente; uma chamada cross-agent é um
  `agent.invoke()` novo, com limite próprio zerado. Sem uma lista de
  `agent_key` já visitados nesta cadeia (propagada via `config`/metadata),
  nada impede A10→A14→A10→A14 em ciclo. Se o alvo já está na pilha, recusa
  antes de gastar token.
- **Profundidade máxima da cadeia** — independente de ciclo, um teto duro
  (ex. 2) no total de saltos cross-agent numa única execução.
- **Rastreamento cross-agent visível** — a chamada precisa aparecer como
  span/trace ligado no Langfuse (mesmo `session_id` da cadeia ou
  equivalente); sem isso o custo de uma cadeia de vários agentes fica
  invisível até a fatura.

## Escopo — maior que a decisão do `agent_facts`

`agent_facts` só tocava o PRO-517 na metade global; a metade individual não
tinha objeção registrada. Esta decisão **é sistêmica desde o início** —
"todo agente pode chamar qualquer outro por padrão" é o tipo de compromisso
que sustenta o roteamento A2→A3/A4/A7 (HANDOFF.md) e afeta os 14 agentes,
não só A10/A14. Por isso pesa mais que `agent_facts` na necessidade de
alinhar com Luís antes de codar — não é mais uma decisão local da PoC.

**Nenhuma implementação foi autorizada nesta sessão — só o desenho, com os
guardrails acima marcados como requisito, não como nice-to-have.**

## Relacionado

- [[Como deve funcionar o molde de agente]]
- [[Agent Harness Template]]
- [[2026-09-10 Memória de fatos do agente (agent_facts)]]
- [[Agent Flow]]
- [[2026-09-10 1-1 Matheus - Luís]] — precursor informal desta decisão, mesmo
  dia (conversa sobre comunicação entre agentes antes do desenho técnico)
