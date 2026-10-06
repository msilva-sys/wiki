---
type: person
status: active
updated: 2026-09-30
aliases: []
tags: [people, fiscal, contabilidade, liveauth]
---

# Gustavo Cruz

Livemode, área fiscal/contábil. Dono e operador do **Hub de Automações
Fiscais** ("projeto de contabilidade"): dashboards protegidos por senha
sobre dados extraídos do ERP **TOTVS** via API própria — fechamento
fiscal, painel de faturamento, impostos pagos, painel de compras. Dados
sensíveis incluem salários e valores de fornecedores. Credenciais e
processos vivem no GitHub (plano Pro). Não é o único usuário do hub —
outros também acessam por senha.

Fonte: [[2026-09-30 LiveAuth - Discovery com Gustavo Cruz (Hub Fiscal)]].

## Relevância pro LiveAuth

O hub é um caso real do problema que o [[LiveAuth]] quer resolver: acesso
externo travado só por senha, sem rate limiting, vulnerável a força bruta.
Vai rodar o [[Sentinela]] (skill de checagem de segurança) no hub como
primeira mitigação.

## Open questions

- Se o hub vai migrar pro LiveAuth ou só recebe mitigação via Sentinela.
