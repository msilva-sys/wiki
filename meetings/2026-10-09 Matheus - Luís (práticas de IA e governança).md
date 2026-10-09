---
type: meeting
status: stable
date: 2026-10-09
updated: 2026-10-09
attendees: [Matheus Oliveira da Silva, Luís Fernandez]
aliases: []
tags: [agent-flow, langfuse, open-router, caching, dry, cognee]
---

# Matheus / Luís (práticas de IA e governança) — 2026-10-09

> Fonte: Granola (notes.granola.ai/d/ee29120c-76da-406a-bb9f-8a5497ccd2d3).
> Transcrição de baixa confiança (STT, canal único sem diarização) — vários
> termos corrigidos por confirmação de msilva: "Linnea" → Linear, "Manual
> Rápido"/"Turbo Rápido" → Turborepo, "Homem Zero cognitivo" → Mem0 e
> Cognee. Atribuição de fala entre Matheus e Luís não é confiável neste
> transcript — tratado como discussão conjunta, sem atribuir
> individualmente salvo onde óbvio pelo conteúdo.

## Decisions

- Memória do [[Agent Flow]] continua em tabela Postgres por enquanto —
  migração pra uma solução de memória mais robusta (Mem0 e/ou Cognee) fica
  pra depois, não prioritária agora. Reafirma o que já estava em aberto em
  [[Cognee como memória dos agentes e do time]].
- Cache da análise da IA é desnecessário (resultado já persistido em
  tabela) — remover.
- Língua dos prompts (pt-BR vs. inglês) segue sem decisão — resistência a
  trabalhar em inglês mantida em aberto, sem fechar agora.
- Adotar DRY em todos os projetos daqui pra frente.

## Commitments

- Revisar o TTL do cache de prompt e alinhar com a frequência real de uso
  (o default da OpenAI serve, ou precisa de ajuste por contexto).
- Apresentar a visão do LangFuse com tokens cacheados pro time.
- Otimizar as queries ao Linear no código do Agent Flow — investigação já
  feita, redução de 35 pra 3-4 chamadas possível só trocando as queries.

## Open questions

- Vale adotar o Open Router como camada de governança de LLM (conta única
  da empresa, visibilidade de uso por pessoa, controle de limites)?
  Levantado como proposta, sem decisão.
- LangFuse como fonte única de versionamento de prompt — visto como
  solução segura, mas ainda não é opinião fechada; plano de abrir acesso
  pra Gabi e Carol.
- Estrutura atual do monorepo (Turborepo, Python + React/Next.js) é
  necessária ou over-engineering pro tamanho do projeto? Autoquestionamento
  levantado, não resolvido.

## Facts stated

- Memória do Agent Flow usa adaptador do LangChain sobre tabela Postgres
  hoje.
- Cache de queries do Linear: investigação encontrou queries ineficientes,
  redução de 35 pra 3-4 chamadas possível só trocando as queries.
- Prompt caching (OpenAI/LangFuse): parte estática do prompt sempre no
  início, parte dinâmica no início quebra o cache. TTL importa — cachear
  por um período mais curto que a janela real de acesso gera perda de
  cache.
- LangFuse já mostra tokens cacheados de forma visível.
- Planejamento antes de codar: "60% certo já é melhor do que sair sem
  plano."
- IA como revisora de código é útil mas precisa de direcionamento ativo —
  correções baratas agora ficam caras se acumuladas.

## Notable quotes

(nenhuma com confiança suficiente pra verbatim — STT de baixa qualidade,
canal único sem diarização.)
