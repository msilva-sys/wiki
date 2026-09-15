---
type: synthesis
status: open
updated: 2026-09-14
aliases: [gap de priorizacao do A10, A10 nao prioriza, esforco e retorno faltando]
tags: [agents, a10, a14, portfolio, priorizacao, airtable, linear]
---

# A10 avalia saúde, não prioriza portfólio

Aberta em [[2026-09-14 Carolina - Matheus (critérios A10-A14)]]. Carolina
(perspectiva de produto) apontou que o A10, como implementado, não é o
"portfólio" da proposta original de [[2026-08-24 Start Agent Flow with A10
Portfolio]] — é uma segunda instância do A14, olhando saúde por iniciativa
em vez de projeto. Ver também [[Fronteira A10×A14 (informação e
métricas)]], que já documenta a granularidade "iniciativa" do A10 mas sob o
ângulo de fronteira A10/A14, não sob o ângulo "isso ainda é priorização de
portfólio?".

## O que está resolvido

A distinção A10 (iniciativa) × A14 (projeto) enquanto **saúde/status** está
clara e implementada — ver [[Fronteira A10×A14 (informação e métricas)]].
Carolina concorda que os dois merecem existir nesse formato.

## O que está em aberto

**A proposta original do A10 era comparar iniciativas para decidir onde
alocar capacidade — priorização entre concorrentes, não status de uma
única iniciativa isolada.** O que existe hoje julga se uma iniciativa está
saudável, não se ela deveria ganhar mais ou menos capacidade que as outras.
Carolina: *"Eu acho que para mim o A10, ele é um A14 para iniciativas [...]
Ele olha saúde, ele não olha planejamento."*

Para priorizar de verdade entre iniciativas, faltam 3 dados que hoje não
existem no Linear:
1. **Tamanho da demanda** (esforço)
2. **Ganho esperado** (retorno)
3. **Risco associado**

Essas informações vivem hoje no **Airtable**, não no Linear. Gabi já tem
regras de priorização de portfólio definidas (fato novo para msilva — não
sabia disso antes desta reunião). Com a migração Airtable → Linear em
andamento ([[2026-08-14 Migrate project management from Jira to
Linear]] é sobre a saída do Jira, não do Airtable — este é um fluxo de
dado separado), fica em aberto se esses campos migram também.

## Correção — os "critérios já definidos" não são esforço/ganho/risco prontos

[[2026-09-15 Discovery A1 e A2 com Gabrielle]]: Carolina (2026-09-14)
descreveu os critérios de Gabrielle como esforço/ganho/risco, dando a
entender que já existe uma régua pronta pra reaproveitar. Na conversa
direta com Gabrielle, os critérios reais são outros e vêm de um **GPT
customizado que ela mesma construiu** ("priorizador de projetos",
https://chatgpt.com/gpts/editor/g-6967b8ab3140819197cda61702e3a006):
valor pro negócio (peso 2), complexidade de desenvolvimento,
escalabilidade, economia de tempo — rodava toda quinta antes da reunião
de priorização (Gabrielle, Carol, Luís, Arthur). **A própria Gabrielle
diz que esses critérios caíram em desuso e não devem servir de base** pra
qualquer priorização nova. Ou seja: a "regra já definida" é só ponto de
partida histórico, não uma decisão pronta — os critérios de verdade ainda
precisam ser definidos do zero, numa reunião que msilva vai marcar com
Carol, Luís e Gabrielle.

## Caminhos possíveis, não decididos

- Trazer esforço/retorno para campos nativos no Linear (iniciativa e/ou
  projeto), viabilizando o A10 calcular priorização real.
- Manter esses dados no Airtable e o A10 ler as duas fontes.
- Construir um terceiro agente dedicado à comparação de portfólio,
  deixando o A10 atual como está (segundo A14, por iniciativa) — sugestão
  de Carolina, não aprofundada na reunião.

## Rastreamento

- PRO-595 — entender as regras de priorização já definidas e decidir a
  migração de esforço/retorno.

## Ligação com o resto do projeto

Refina [[2026-08-24 Start Agent Flow with A10 Portfolio]] (o que essa
decisão não resolveu) e [[Fronteira A10×A14 (informação e métricas)]]
(fronteira de escopo, não só de dado).
