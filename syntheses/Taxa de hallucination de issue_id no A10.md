---
type: synthesis
status: active
updated: 2026-08-28
tags: [agents, agent-flow, a10, langgraph, hallucination, quality]
aliases: [taxa de hallucination A10, PRO-480]
---

# Taxa de hallucination de `issue_id` no A10

Rastreia, ao longo do tempo, a fração de sugestões do A10 cujo `issue_id`
não corresponde a nenhuma issue real do backlog — pra comparar tentativas
de correção contra uma linha de base real, não contra suposição.

## Contexto

Achado original em `PRO-477`/`PRO-480` ([[Fluxo Agêntico project instruction|HANDOFF.md]]
do repo `livemode-fluxo-agentico`): ~29% das sugestões vinham com
`issue_id` truncado/inventado, não validado. Ficou registrado como
"pendência não-bloqueante" e não foi corrigido até esta sessão.

Reaberto em 2026-08-28 investigando uma inconsistência visual reportada
pela sessão do frontend (tabela de sugestões do A10 mostrando mais linhas
que a soma de `risk_by_project` do `portfolio_health`). Ver
[[2026-08-24 Build A10 and A14 together, PoC first]] pra o histórico da
feature `portfolio_health` em si — esta página é só sobre a métrica de
hallucination.

## Medições

### Baseline — 2026-08-28, antes do fix

Medido contra o cache real (`agent_cache`, key
`a10:36f26e12-1531-411a-85a9-3e6342ce3589`, rodada mais recente):

- **21 sugestões totais, 6 com `issue_id` inválido — 28,6%**.
- Valores encontrados: `'343?'`, `'300?'`, `'292?'`, `'293?'`, `'303?'`,
  `'306?'` — todos parecem tentativas malformadas de escrever o
  **identifier** curto da issue (ex. "PRO-343"), não o UUID de 36
  caracteres que o schema pedia.
- Bate quase exatamente com o ~29% do `PRO-480` — não é regressão nova,
  é o mesmo problema, nunca corrigido.

## Decisão testada

Trocar o campo que o LLM precisa preencher: de `issue_id` (UUID, gerado
livremente) pra `issue_identifier` (ex. "PRO-343", curto, já visível em
cada linha do `list_issues()` — e aparentemente o que o modelo já tenta
escrever por padrão, a julgar pelos valores capturados acima). Resolução
`identifier → id/title/url` real feita em Python depois do
`agent.invoke()`, sem chamada de LLM extra — suggestions não resolvidas
ficam marcadas `unresolved: True` em vez de caírem em `None` silencioso.

Detalhe completo da decisão (por que não virou `workflow`/`StateGraph`,
as opções comparadas) em [[2026-08-24 Build A10 and A14 together, PoC first]].

### Depois do fix — 2026-08-28, mesmo dia

Primeira rodada real depois da troca pra `issue_identifier`: **14
sugestões, 0 não resolvidas — 0%** (contra 28,6% da linha de base).
`portfolio_health.unresolved_count = 0`, soma de `risk_by_project` = 14,
batendo exatamente com o total — o bug de inconsistência visual
(tabela vs. métrica) também some, de graça, pelo mesmo fix.

**Achado colateral na mesma rodada**: `response_format=A10Output`
expunha `summary`/`portfolio_health` pro LLM tentar preencher — ele
alucinou valores qualitativos (`"alto"`) num campo tipado `int`,
quebrando a validação inteira (crash, não só dado errado). Corrigido
junto: LLM agora produz só `A10ModelOutput` (`suggestions`), e
`summary`/`portfolio_health` são montados em Python depois, nunca
expostos ao schema do LLM.

**Ressalva**: uma rodada só não é conclusivo — pode ser sorte da vez.
Vale acumular mais rodadas reais ao longo do tempo antes de declarar a
taxa "resolvida" de verdade.
