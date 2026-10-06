---
type: source
status: stable
updated: 2026-10-06
aliases: [feedback guia de um builder]
tags: [guia de um builder, sentinela, feedback]
---

# Feedback Carol guia de um builder

Notas próprias de msilva (`raw/Feedback Carol guia de um builder.md`),
escritas pra preparar o feedback pedido por Carolina Bezerra sobre o **Guia
de um Builder** (https://guia-de-um-builder.livemode.space/). Enviadas a ela
por Slack em 2026-10-06, reescritas na voz dele (ver perfil em
`C:\Users\msilva\.claude\skills\team-comms\SKILL.md`).

## Pontos elogiados

- Introdução do CTA ("os erros deixam de ser só seus") — reforça autonomia +
  padrão, alinhado à cultura da LiveMode.
- Seção sobre gravidade de vazamento de dado sensível, com bons exemplos
  (senha num projeto vazio vs. senha com dado de pessoa).
- Seção "honestidade" sobre o Sentinela — delega parcialmente a
  responsabilidade de rodar a checagem pro próprio builder.
- FAQ cobre a maioria das dúvidas reais de quem lê.

## Achados confirmados contra o código-fonte do repo (2026-10-06)

Três dúvidas do rascunho original viraram achados reais depois de ler
`livemode-org/guia-de-um-builder` por completo (não há cópia em `raw/`,
repo lido direto via GitHub):

- **"Etiqueta" e "TAG" são a mesma coisa** (`homologacao.md` confirma —
  etiqueta é o nome antigo, pré-v2.0), mas o `index.html` da própria página
  usa os dois termos em pontos diferentes do texto — inconsistência real de
  vocabulário, não falta de atenção de quem lê.
- **Revisão trimestral sem mecanismo especificado** — não há menção a
  "trimestral" em `homologacao.md`/`.json` nem nos 12 itens do contrato; a
  única referência é uma frase solta no `index.html`, que trata o mecanismo
  como se já fosse automático via Sentinela.
- **"SSO" não explicado, usado uma única vez** (`index.html`, lista de
  responsabilidades do gestor) — o mecanismo real documentado em todo o
  resto do repo é login Google restrito a `@livemode.com`, nunca chamado de
  SSO em outro lugar.

## Correção que saiu desse mergulho

A investigação pro feedback também corrigiu o entendimento de msilva sobre
o **Sentinela** — ver [[Sentinela]], seção "Correção à minha própria nota
anterior".

## Open questions

- Se Carolina vai corrigir a inconsistência etiqueta/TAG e explicitar o
  mecanismo da revisão trimestral.
