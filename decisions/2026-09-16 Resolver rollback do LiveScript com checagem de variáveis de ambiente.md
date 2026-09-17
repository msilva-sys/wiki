---
type: decision
status: active
updated: 2026-09-17
aliases: [rollback do proxy, plano de rollback livescript]
tags: [airtable-proxy, livescript, rollback, vercel]
---

# Resolver rollback do LiveScript com checagem de variáveis de ambiente

**Decidido 2026-09-16, Slack (DM Matheus–Luís).** Antes de qualquer coisa ir
pra produção com [[LiveScript]] apontando pro [[Airtable Proxy]], Luís pediu
um plano de rollback rápido. A hipótese original dele — trocar só a variável
de ambiente — foi investigada por Matheus e **refutada no mesmo dia**: a
Vercel não aplica mudança de env var em Serverless Function sem redeploy
(~1–3min, não instantâneo).

Matheus propôs uma alternativa mais robusta: uma flag lida em runtime no
Firestore (já dependência do repo `livemode-roteiros-nextjs`), com cache em
memória de ~5–10s no wrapper que já centraliza as chamadas ao Airtable
(`lib/services/airtable-monitoring.ts`), evitando estourar volume de leitura
nos 17 pontos que tocam esse wrapper.

**Luís rejeitou a proposta, não por estar errada, mas por ser complexidade
desnecessária agora**: *"A solução que vc apresentou é o caminho ótimo, mas
mais complexo e arriscado. Não creio que precisemos dela agora. Só queremos
nos precaver."* Redeploy de 1–3min não é visto como crítico — *"não me parece
ser crítico."*

## O que fica decidido

Rollback = **checagem de variáveis de ambiente no build/deploy**: se
`AIRTABLE_PROXY_KEY`/`AIRTABLE_PROXY_URL` existirem e forem válidas, aponta
pro proxy; caso contrário, mantém o comportamento atual (Airtable direto via
PAT). Sem flag em runtime, sem Firestore, sem cache adicional. Matheus
concorda (*"Tudo bem, concordo!"*).

A ideia de uma feature flag de verdade (Luís cita
[OpenFeature](https://openfeature.dev/) e
[Vercel Flags](https://vercel.com/docs/flags)) fica anotada como possibilidade
futura, sem compromisso — só se a necessidade de rollback instantâneo
aparecer de fato.

## Relacionado

- [[Airtable Proxy]]
- [[LiveScript]]
- [[2026-09-15 Proxy e Fluxo Agêntico com Luís]]
- [[Luís Fernandez]]
