---
type: source
status: stable
date: 2026-09-02
updated: 2026-09-04
aliases: [resposta IA fronteira A10 A14, doc de fronteira do Luís]
tags: [agents, agent-flow, a10, a14, product-scope, metrics]
---

# A10 A14 Fronteira, Informação e Métricas

Resumo de `raw/2026-09-02 A10 A14 fronteira - resposta IA trazida por Luís.html`.

## O que é

Resposta de IA — não texto original do Luís — que ele trouxe a pedido de
msilva em [[2026-09-02 1-1 Matheus - Luís]]: "fontes e argumentos, inclusive
resposta de IA crua, sem viés," para embasar a fronteira PM (A14) vs.
Portfolio (A10) antes de fechar a direção de produto dos dois agentes. É um
framework genérico de fronteira multi-agente, com um ajuste explícito no
próprio texto para o nosso contexto: aqui A14 é "menos produto e mais
entrega" — tudo interno, sem mercado, o "usuário" é uma área da casa. Isso
reposiciona A14 para escopo/sequência/dependência/prazo, e devolve a
pergunta "isso deveria ser feito?" para o A10.

## Conteúdo

Framework completo fanned-out em
[[Fronteira A10×A14 (informação e métricas)]]: teste de cinco perguntas para
classificar de quem é uma decisão; o que cada agente possui (recebe/entrega/
nunca faz); modelo de níveis de informação N0–N4 com a regra de agregação
("A10 nunca recebe item individual abaixo de N1"); painel de métricas por
agente (sete cada, incluindo "efeito medido na área" no painel do A14) mais
uma lista do que não serve como métrica em contexto interno (velocity,
NPS de amostra pequena, contagem de entregas, adoção de ferramenta
obrigatória); sete pares faça/não-faça; tabela de roteamento de exemplo para
o A2; heurísticas de detecção de vazamento para o eval (teste de verbo,
prompt espelhado, contexto vazio); linhas de guardrail candidatas para o
system prompt de cada agente; e uma seção final nomeando a lacuna do loop de
retorno A14→A10 — o efeito medido de uma entrega não fecha o ciclo e não
sobe para o A10 decidir o próximo passo.

## Confiança

Documento é resposta de IA genérica, não opinião pessoal do Luís nem
decisão já tomada — tratar como material de referência para desenhar a
fronteira, não como algo já adotado. A fronteira final ainda depende de
msilva decidir o que aceitar daqui.

## Open questions

- O framework completo (N0–N4, painéis de métrica, faça/não-faça) ainda não
  foi confrontado com o código real além da regra já implementada (A10
  nunca fala de issue específica) — ver
  [[2026-09-02 A10 para de expor detalhe de issue, encaminha pro A14]].
- O loop de retorno A14→A10 (efeito medido) é a lacuna mais concreta que o
  doc nomeia — ainda sem desenho nem implementação, ver
  [[Fronteira A10×A14 (informação e métricas)]].
