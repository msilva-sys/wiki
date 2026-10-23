---
type: project
status: active
updated: 2026-10-21
aliases: [guia de um builder, guia do builder, P-PRO-16, Programa de Governança e Segurança em IA]
tags: [governanca, seguranca, ia, builders, carolina]
---

# Guia de um Builder

Projeto Linear `Guia de um Builder` (P-PRO-16), que abriga o **Programa de
Governança e Segurança em IA**. Dona: [[Carolina Bezerra]]. O nome do projeto
é também o da página de onboarding ao padrão corporativo, uma das peças do
programa.

## Peças

| Peça | O que é | Página |
|---|---|---|
| Guia de um Builder (página) | Onboarding ao padrão corporativo — https://guia-de-um-builder.livemode.space/ (repo `livemode-org/guia-de-um-builder`) | esta |
| Sentinela | Checagem de segurança e homologação de projetos | [[Sentinela]] |
| Vitrine de IA | Mesmo programa-mãe; vitrine do que as áreas constroem | [[Vitrine de IA]] |

## A página

- Deploy em Cloudflare Pages com trava de domínio em
  `functions/_middleware.js`. É o único repo do inventário com trava extra no
  CI, que bloqueia o deploy sem os 3 secrets — ver
  [[Trava de domínio e autenticação — inventário para o LiveAuth]].
- O `governanca.md` do repo registra que a trava de domínio por e-mail
  substituiu a senha única em 2026-09-22.
- "Etiqueta" é o nome antigo de "TAG" (pré-v2.0, segundo o `homologacao.md`).

## Histórico com msilva

- 2026-10-06: Carolina pediu feedback sobre a página. msilva respondeu por
  Slack com três achados confirmados no código: "etiqueta"/"TAG" misturados
  no `index.html`, revisão trimestral sem mecanismo no contrato, "SSO" usado
  sem explicação. Ver [[Feedback Carol guia de um builder]]. A leitura também
  corrigiu o entendimento de msilva sobre o [[Sentinela]].

## Open questions

- Escopo completo do P-PRO-16 além das três peças acima — a wiki só conhece o
  projeto pelas bordas (unverified: confirmar no Linear).
- Se os três achados do feedback de 2026-10-06 foram corrigidos na página.
- A página é pública ou atrás da trava? O inventário do LiveAuth diz as duas
  coisas ("deliberadamente público" e trava de domínio no `_middleware.js`);
  conferir abrindo a URL sem sessão.
