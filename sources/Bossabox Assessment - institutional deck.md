---
type: source
status: active
updated: 2026-08-26
date: 2026-08-26
aliases: [bossabox assessment deck, material institucional bossabox]
source: "raw/Material Institucional - Assessment.pptx.pdf"
tags: [bossabox, vendor, assessment, dora, vsm, team-topologies]
---

# Bossabox Assessment — deck institucional

Anexo do e-mail em [[LiveMode e Bossabox Services - email thread]]. Deck
de vendas (12 páginas) apresentando o produto "Assessment" da
[[Bossabox Engagement]] — mais detalhado do que qualquer coisa registrada
até agora sobre a metodologia deles.

## Tese central

*"IA não conserta uma operação que já trava"* — colocar IA sobre uma
operação quebrada desloca o gargalo, não elimina. Divide as dores em
duas fases:

- **Upstream (antes do código)**: requisitos vagos, fila/priorização
  pouco clara, ciclos de aprovação/governança.
- **Downstream (do código à produção)**: débito técnico, concentração
  de bugs, qualidade de código (code review lento).

## O "Modelo Operacional Digital" — agora com detalhe concreto

Estende o que [[Bossabox Engagement]] já registrava sobre "Sistema
Operacional / OS":

- **Método**: pipeline padronizado, testado em +200 iniciativas — Backlog
  → Protot. → Design Critique → Epic Spec → DoD Approval → Tech Refining
  → Ready to Dev → In Dev → QA (Dev) → QA (Stage) → Ready to Prod → Prod
  Validate. Dividido em Upstream/Downstream nesse mesmo ponto.
- **AI embarcada em toda etapa**, com retenção de contexto e segurança —
  **+40 agentes**, **8 quality gates**, contexto mantido entre etapas,
  conectores via **MCP** com ferramentas de mercado.
- **Software**: interface proprietária de orquestração/gestão de todo o
  Método.

## O Assessment — mecanismo

Um "motor agêntico" extrai sinais direto das ferramentas que o time já
usa, analisa contra benchmarks de operação digital, um especialista
valida e fecha.

- **Fontes lidas**: Jira, GitHub, Linear, Confluence, entrevistas — quase
  o conjunto exato de ferramentas da Livemode (Linear e GitHub
  certamente; Jira é o legado; Confluence não confirmado aqui no vault).
- **Entregáveis**: relatório executivo, apresentação (handoff), plano de
  trabalho.
- **Diferenciais alegados**: motor agêntico em vez de horas de consultor
  (semanas, não meses); orientado por dado, qualitativo só como
  complemento; "ponte para execução" — plano já configurado para o
  próximo passo, não material para comitê discutir.

## Instrumentos de leitura (exemplos, dados fictícios/anonimizados)

| Instrumento | O que mede |
|---|---|
| VSM (fluxo de valor) | tempo gasto por etapa, da ideia à produção |
| DORA | frequência de deploy, lead time, taxa de falha, tempo de recuperação |
| DevEx | percepção de quem desenvolve, por dimensão (deploy, code review, monitoramento...) |
| Squad Analytics | squads lado a lado em fluxo/qualidade/estabilidade/velocidade |
| Inventário de arquitetura | sistemas, integrações, saúde técnica por domínio, onde o risco técnico concentra |
| Team Topologies | classifica times por tipo/interação; aponta sobrecarga, dependência, pontos únicos de falha |
| AI Readiness | maturidade 0–4 por dimensão (adoção de IA, dados, infra, entrega, qualidade, observabilidade, specs) |
| Tech Capabilities | o que está implementado/parcial/ausente (CI/CD, IaC, code review como gate, testes automatizados...) |

## Modalidades e investimento

- **Por escopo**: um time / múltiplos times / organização inteira.
- **Por profundidade**: entrada **gratuita** (leitura inicial, hipótese e
  direção, sem custo) vs. **aprofundada** (causa-raiz validada, AI
  Readiness Score, plano executável — cobrada por escopo, "abaixo da
  consultoria tradicional", entrevistas como camada opcional).

## Case anonimizado citado como prova

Um cliente (ecommerce, "AcmeCommerce", abril 2026, anonimizado):
**flow efficiency acima da média** (46%), mas **lead time dominado por
upstream** (10,4 dias, 67% do lead time é fila de "Ready" antes de
entrar em execução), e **risco concentrado no squad "Pedidos"** (42% dos
bugs, pior velocity ratio, bus factor em DevOps). Três ações priorizadas
por esforço×impacto: formalizar Definition of Ready com checklist;
squad health review mensal com métricas lado a lado; SLA de resolução
para bugs críticos em "Pedidos".

## Relevância para Livemode

- **O conjunto de fontes que eles leem (Jira/GitHub/Linear/Confluence)
  bate quase exatamente com o toolset real da Livemode** — reforça que a
  proposta é plugável sem trabalho de integração pesado da nossa parte.
- **A entrada gratuita** é provavelmente o que está por trás do
  "assessment proposal" mencionado como próximo passo em
  [[Bossabox Engagement]] — a modalidade paga só entraria depois, e por
  escopo.
- O framing "IA não conserta operação que já trava" é quase um espelho
  direto do próprio ceticismo de Carolina sobre priorizar [[Farol]] e
  sobre agentes de monitoramento no [[Agent Flow]] — vale considerar se
  isso reforça ou compete com o argumento dela.

## Open questions

- Isso será a modalidade gratuita (leitura inicial) ou já a aprofundada
  paga? Não dito no e-mail nem no deck.
- Qual escopo — um squad, múltiplos, ou a área inteira?

## Ver também

[[What Bossabox's Assessment suggests for Agent Flow]] — leitura cruzada
completa contra o planejamento do [[Agent Flow]], pedida por msilva
2026-08-26.
