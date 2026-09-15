---
type: meeting-prep
status: draft
updated: 2026-09-15
date: 2026-09-15
aliases: [prep luís 15/09, prep proxy e fluxo agêntico]
tags: [luís, airtable-proxy, agent-flow, a1, a2, meeting-prep]
---
# Proxy e Fluxo Agêntico com Luís — 2026-09-15

## Pauta

**1. Padrão de entrada do A1/A2 — perspectiva de produto do Luís**
Combinado em 09-10: decidir o padrão de entrada junto, antes de começar —
dev começa hoje. Pergunta pra ele: como ele imagina o comportamento desses
dois agentes (intake + classificação/roteamento) do ponto de vista de
produto, não de arquitetura. Contexto de fundo, não pauta em si: hoje cada
canal de entrada tem formato e roteamento próprios, sem critério unificado
— ver `Meeting prep - Discovery A1 e A2 com Gabrielle - 2026-09-15.md` pro
levantamento de canais (se essa conversa já rolou, trazer o que saiu de
lá).

**2. Qual consumidor apontar pro proxy**
Proxy já em produção (Cloud Run público, `PRO-84`), mas nenhum serviço
aponta pra ele ainda. Aberto — sem candidato pré-decidido. Pontos de
contexto:
- Compat. com SDKs fora do Node **já verificada** (Python/`pyairtable`,
  09-03) — não é mais bloqueio pra escolher um app não-Node.
- Luís puxou um alvo interno de "pelo menos um app apontando pro proxy"
  pra 09-10 ([[Luís Fernandez]]) — já passou, sem consumidor definido.
- LiveScript é *um* consumidor possível (já roteado via `PRO-96`), não
  necessariamente o escolhido.

## Se sobrar tempo

**O que a Gabi já tem sobre priorização de portfólio (via Carolina, 09-14)**
Carolina revelou que Gabi já tem regras de priorização definidas
(esforço/retorno/risco), hoje vivendo no Airtable — fato novo pro msilva.
Isso reabre o escopo do A10: ele hoje julga saúde por iniciativa, não
compara iniciativas pra priorizar entre si (proposta original). Caminhos
não decididos: trazer esforço/retorno pro Linear, manter no Airtable e o
A10 ler as duas fontes, ou um terceiro agente dedicado à comparação de
portfólio. Ver [[A10 avalia saúde, não prioriza portfólio]] (`PRO-595`).
