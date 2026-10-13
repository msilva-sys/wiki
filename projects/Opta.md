---
type: project
status: active
updated: 2026-10-13
aliases: [opta]
tags: [opta, transição]
---

# Opta

Integração interna com a **Opta**, fornecedor externo de dados estatísticos
de futebol (muito usado pelo time de conteúdo, e conhecido fora da empresa —
é o "supercomputador" citado por comentaristas esportivos). Compartilhado
por Matheus e [[Luís Fernandez]] enquanto ele estiver presente na empresa
([[2026-10-07 Divisão de projetos na transição de Luís]]) — Matheus como
guardião técnico e revisor de PR. Detalhado em
[[2026-10-09 Opta - O que é isso]].

## Histórico de ownership

Nenhum dono formal/único até hoje. [[Pessanha]] começou a assumir a
responsabilidade antes de Luís entrar na empresa (já tinha saído quando
Luís chegou). [[Pedro Alves]] (também ligado ao [[Orca (CDE)]]) virou o
dono informal — conhecia bem o sistema, era procurado por quem tinha
dúvida. [[Vini]] aprendeu bastante durante a Copa, perto do Pedro. Quem
hoje é o responsável interno pelo **contrato comercial** com a Opta não é
conhecido — pergunta em aberto, Luís ia levar a [[Carolina Bezerra]] e
possivelmente [[Gabrielle Ferreira]].

## Arquitetura — três projetos separados

- **Opta API** — proxy que encapsula os endpoints da Opta que a empresa tem
  direito (não confirmado se cobre todos os endpoints licenciados).
- **Opta MCP** — expõe essa API internamente via MCP; muito usado.
- **Opta Docs** — documentação gerada por Luís via Claude, autenticada
  (não exposta fora da empresa). Mistura orientação de uso (como integrar,
  lista de competições disponíveis) com uma seção de operação interna
  (como adicionar competição, gestão de chave) que não deveria ser pública
  — ainda sem solução de separação.

Luís já cogitou unificar os três repos num monorepo; não chegou a fazer —
hoje é incômodo trabalhar com Claude Code cruzando os três.

## Contrato e chave de API

Contrato não é barato; mudar o escopo (ex: licenciar uma nova competição,
tipo La Liga) é renegociação de contrato, não um simples gerar-de-chave —
valores citados perto de R$150.000, contratos de longo prazo. A empresa usa
**uma única chave de API compartilhada por todo mundo** (ao contrário das
chaves de agente, individuais e revogáveis) — comparada por Luís a "escova
de dente compartilhada." Plano verbal de rotação (não implementado): avisar
a empresa toda, ~30 dias pra migrar pra chave nova, ~60 dias depois desligar
a antiga.

**Credencial hoje é buscada direto do BigQuery, não é secret do GCP** —
mesma classe de dívida que o [[Airtable Proxy]] já resolveu, apontada por
Luís como "um problema" real.

## Uso em produção

No ar desde **30/03/2026**. Relatório de uso feito por Luís a pedido de
[[Maria Fernanda Lemos]] (~2026-08-25): até 28/08/2026, 1556 requests em 31
dias; principal consumidor é tráfego via agente (Claude/Cloud), +25% Node,
7% Python; chamadas de ferramenta estáveis em ~230/mês.

Taxas de sucesso por ferramenta (mesmo relatório):
- `get_schedule`: **51%** de 246 chamadas — muito baixo, bug que Luís acha
  que corrigiu parcialmente, sem certeza; sem detalhamento do erro.
- `get_standings`: 100%.
- `squads`: 97%.

Outros achados: lista de competições disponíveis é um JSON fixo atualizado
manualmente em produção (deveria vir dinamicamente da própria API); log de
uso só começou perto do 5º dia da Copa (não cobre o histórico real); alguns
erros 401 por chave mal configurada do lado de quem consome, sem
identificação de quem é.

Cobrança da Opta: fixa até um limite de chamadas, por faixa adicional acima
disso. Pedro Alves teria feito algum cache seletivo de dado pra reduzir
custo (nível de detalhe não levantado). **Cuidado**: cachear resposta pode
esbarrar em cláusula contratual que proíbe — não ficou claro qual, ver
[[2026-10-09 Opta - O que é isso]].

## Visão de produto

Luís quer que o time trate a Opta como produto próprio (mesmo discurso do
[[LiveScript]]): dar visibilidade real (dashboard no telão da sala de
reunião, como o dashboard "Insights" do [[Airtable Proxy]]), tratar quem usa
como cliente, citar a Opta como case de sucesso do time (como já fizeram com
LiveScript e [[Orca (CDE)]]). Pediu a Matheus e [[Yasmin Macedo]] que
considerassem apresentar a Opta pro time como dupla.

## Backlog de melhorias (não aprovado/priorizado ainda)

- Achar quem é o responsável interno pelo contrato comercial.
- Implementar a rotação de chave planejada acima.
- Investigar e corrigir de vez os 401s e o bug do `get_schedule`.
- Migrar a credencial pra GCP Secret Manager.
- Página de competições disponíveis dinâmica, com fluxo de pedido de acesso
  a competição nova (abre canal no Slack / dispara e-mail pro responsável
  pelo contrato).
- Separar documentação pública de documentação de operação no Opta Docs.
- Considerar reaproveitar um bot/agente de chat interno antigo (parado
  desde fevereiro) dentro do repo do Opta MCP.
- Considerar monorepo pros três projetos.
- Dashboard de uso no telão.

Nada disso está decidido — Luís foi claro que não é pra agir sozinho sem
aprovar com Gabi/Carol primeiro, embora seja a favor e vá levar a elas.
