---
type: project
status: active
updated: 2026-09-24
aliases: [liveauth, liveauth poc]
tags: [auth, security, liveauth, proxy]
---

# LiveAuth

> Related: [[LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode]] · [[Airtable Proxy]]

Autorização por grupo compartilhada entre apps internos da Livemode. POC em
andamento no Linear — iniciativa **LiveAuth**, projeto **LiveAuth - POC**
([P-PRO-29](https://linear.app/projetos-livemode/project/liveauth-poc-c6dfa1c702b0)),
lead msilva, team `Projetos-livemode`. O desenho completo (objetivo,
interfaces, segurança, open issues) vive na synthesis linkada acima; esta
página acompanha o estado de execução.

## Estado, 2026-09-24

Status Linear: **In Progress**. `startDate` 2026-09-23, `targetDate`
**2026-09-25** (amanhã).

- [x] PRO-711 — Restringir login do LiveAuth a contas @livemode.com (Blocking Function no Firebase Auth)
- [x] PRO-714 — Login único do LiveAuth decide quem entra em cada app parceiro
- [x] PRO-713 — Tela pra montar o grupo `admin-proxy` e gerenciar membros
- [ ] PRO-717 (In Progress, msilva) — skill separada que aponta apps com dado
      sensível sem autenticação, ou com login fora do padrão único (fora do
      LiveAuth e do Sentinela por enquanto)

**Falta issue de deploy em produção** — nenhuma das quatro cobre isso, e o
target date é amanhã. Sinalizado por msilva em 2026-09-24, sem issue criada.

## Open questions (herdadas da synthesis)

Nome "LiveAuth" fechado com Luís, falta validar com a Gabi; dono da infra e
projeto Firebase a usar seguem indefinidos — ver a lista completa na
synthesis.
